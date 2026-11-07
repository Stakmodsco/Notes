mike@Mikes-MacBook-Pro Safeguard-app-my-fix-main % cd /Users/mike/Desktop/Safeguard-app-my-fix-main && cat backend/subscription_access.py

from __future__ import annotations

import logging
import os
from datetime import datetime, timedelta, timezone
from typing import Any, Dict, Optional
from urllib.parse import quote

import httpx
from fastapi import HTTPException

logger = logging.getLogger(__name__)

REVENUECAT_API_BASE = "https://api.revenuecat.com/v2"

MONTHLY_PRODUCT_ID = "safeguard_pro_monthly"
YEARLY_PRODUCT_ID = "safeguard_pro_yearly"


def _utc_now() -> datetime:
    return datetime.now(timezone.utc)


def _normalize_datetime(value: Any) -> Optional[datetime]:
    if not isinstance(value, datetime):
        return None

    if value.tzinfo is None:
        return value.replace(tzinfo=timezone.utc)

    return value.astimezone(timezone.utc)


async def _get_trial_access(
    db: Any,
    user_id: str,
) -> Dict[str, Any]:
    record = await db.subscription_access.find_one(
        {
            "$or": [
                {"_id": user_id},
                {"user_id": user_id},
            ]
        }
    )

    if not record:
        now = _utc_now()

        await db.subscription_access.update_one(
            {"user_id": user_id},
            {
                "$setOnInsert": {
                    "user_id": user_id,
                    "trial_started_at": now,
                    "trial_ends_at": now + timedelta(days=30),
                    "created_at": now,
                },
                "$set": {
                    "updated_at": now,
                },
            },
            upsert=True,
        )

        record = await db.subscription_access.find_one(
            {"user_id": user_id}
        )

        if not record:
            raise HTTPException(
                status_code=503,
                detail="Subscription access is temporarily unavailable.",
            )

    trial_started_at = _normalize_datetime(
        record.get("trial_started_at")
    )
    trial_ends_at = _normalize_datetime(
        record.get("trial_ends_at")
    )

    if not trial_ends_at:
        return {
            "active": False,
            "trial_started_at": trial_started_at,
            "trial_ends_at": None,
        }

    return {
        "active": _utc_now() < trial_ends_at,
        "trial_started_at": trial_started_at,
        "trial_ends_at": trial_ends_at,
    }


async def _revenuecat_get(path: str) -> Dict[str, Any]:
    api_key = os.environ.get(
        "REVENUECAT_V2_SECRET_KEY",
        "",
    ).strip()

    project_id = os.environ.get(
        "REVENUECAT_PROJECT_ID",
        "",
    ).strip()

    if not api_key or not project_id:
        raise RuntimeError(
            "RevenueCat backend credentials are not configured."
        )

    url = (
        f"{REVENUECAT_API_BASE}/projects/"
        f"{quote(project_id, safe='')}{path}"
    )

    headers = {
        "Authorization": f"Bearer {api_key}",
        "Accept": "application/json",
    }

    async with httpx.AsyncClient(timeout=12.0) as client:
        response = await client.get(
            url,
            headers=headers,
        )

    if response.status_code == 404:
        return {}

    if response.status_code >= 400:
        logger.error(
            "[SUBSCRIPTION] RevenueCat API failed status=%s body=%s",
            response.status_code,
            response.text[:500],
        )
        raise RuntimeError(
            f"RevenueCat request failed with status "
            f"{response.status_code}"
        )

    try:
        return response.json()
    except Exception as exc:
        raise RuntimeError(
            "RevenueCat returned an invalid response."
        ) from exc


async def _has_active_pro_entitlement(
    user_id: str,
) -> bool:
    entitlement_id = os.environ.get(
        "REVENUECAT_PRO_ENTITLEMENT_ID",
        "",
    ).strip()

    if not entitlement_id:
        raise RuntimeError(
            "RevenueCat Pro entitlement ID is not configured."
        )

    customer_id = quote(
        user_id,
        safe="",
    )

    data = await _revenuecat_get(
        f"/customers/{customer_id}/active_entitlements"
    )

    items = data.get("items", [])

    if not isinstance(items, list):
        return False

    for item in items:
        if not isinstance(item, dict):
            continue

        candidate_id = str(
            item.get("id")
            or item.get("entitlement_id")
            or ""
        ).strip()

        if candidate_id == entitlement_id:
            return True

    return False


def _extract_product_id(item: dict) -> str:
    candidates = (
        item.get("product_id"),
        item.get("product_identifier"),
        item.get("store_product_identifier"),
    )

    for value in candidates:
        if value:
            return str(value)

    return ""


def _extract_expiration_ms(item: dict) -> int:
    candidates = (
        item.get("current_period_ends_at"),
        item.get("expires_at"),
        item.get("expiration_at"),
    )

    for value in candidates:
        if value is None:
            continue

        try:
            return int(value)
        except (TypeError, ValueError):
            continue

    return 0


async def _get_active_billing_period(
    user_id: str,
) -> Optional[str]:
    customer_id = quote(
        user_id,
        safe="",
    )

    data = await _revenuecat_get(
        f"/customers/{customer_id}/subscriptions"
    )

    items = data.get("items", [])

    if not isinstance(items, list):
        return None

    now_ms = int(
        _utc_now().timestamp() * 1000
    )

    candidates = []

    for item in items:
        if not isinstance(item, dict):
            continue

        product_id = _extract_product_id(item)
        normalized_product_id = product_id.lower()

        if (
            MONTHLY_PRODUCT_ID.lower()
            not in normalized_product_id
            and YEARLY_PRODUCT_ID.lower()
            not in normalized_product_id
        ):
            continue

        status = str(
            item.get("status") or ""
        ).lower()

        expires_at_ms = _extract_expiration_ms(
            item
        )

        is_current = (
            status in {
                "active",
                "trialing",
                "grace_period",
            }
            or expires_at_ms > now_ms
        )

        if not is_current:
            continue

        candidates.append(
            (
                expires_at_ms,
                normalized_product_id,
            )
        )

    candidates.sort(
        reverse=True
    )

    for _, product_id in candidates:
        if YEARLY_PRODUCT_ID.lower() in product_id:
            return "yearly"

        if MONTHLY_PRODUCT_ID.lower() in product_id:
            return "monthly"

    return None


async def get_subscription_access(
    db: Any,
    user: dict,
) -> Dict[str, Any]:
    user_id = str(
        user.get("id") or ""
    ).strip()

    if not user_id:
        raise HTTPException(
            status_code=401,
            detail="Authenticated user ID is unavailable.",
        )

    trial = await _get_trial_access(
        db,
        user_id,
    )

    if trial["active"]:
        return {
            "state": "trial_active",
            "is_active": True,
            "billing_period": "trial",
            "document_limit": None,
            "can_use_ai": True,
            "can_use_document_processing": True,
            "can_use_reminders": True,
            "can_manage_profiles": True,
        }

    try:
        has_pro = await _has_active_pro_entitlement(
            user_id
        )
    except Exception as exc:
        logger.exception(
            "[SUBSCRIPTION] RevenueCat entitlement verification "
            "failed user=%s",
            user_id,
        )
        raise HTTPException(
            status_code=503,
            detail=(
                "Subscription verification is temporarily "
                "unavailable. Please try again."
            ),
        ) from exc

    if not has_pro:
        return {
            "state": "locked",
            "is_active": False,
            "billing_period": None,
            "document_limit": 0,
            "can_use_ai": False,
            "can_use_document_processing": False,
            "can_use_reminders": False,
            "can_manage_profiles": False,
        }

    try:
        billing_period = await _get_active_billing_period(
            user_id
        )
    except Exception as exc:
        logger.exception(
            "[SUBSCRIPTION] RevenueCat subscription lookup "
            "failed user=%s",
            user_id,
        )
        raise HTTPException(
            status_code=503,
            detail=(
                "Subscription verification is temporarily "
                "unavailable. Please try again."
            ),
        ) from exc

    if billing_period == "yearly":
        return {
            "state": "yearly_active",
            "is_active": True,
            "billing_period": "yearly",
            "document_limit": None,
            "can_use_ai": True,
            "can_use_document_processing": True,
            "can_use_reminders": True,
            "can_manage_profiles": True,
        }

    if billing_period == "monthly":
        return {
            "state": "monthly_active",
            "is_active": True,
            "billing_period": "monthly",
            "document_limit": None,
            "can_use_ai": True,
            "can_use_document_processing": True,
            "can_use_reminders": True,
            "can_manage_profiles": True,
        }

    logger.error(
        "[SUBSCRIPTION] Active Pro entitlement has unknown "
        "billing period user=%s",
        user_id,
    )

    raise HTTPException(
        status_code=503,
        detail=(
            "Your subscription could not be verified. "
            "Please restore purchases and try again."
        ),
    )


async def require_plus_access(
    db: Any,
    user: dict,
) -> Dict[str, Any]:
    access = await get_subscription_access(
        db,
        user,
    )

    if not access["is_active"]:
        raise HTTPException(
            status_code=403,
            detail="NotMe Plus is required for this feature.",
        )

    return access


async def require_ai_access(
    db: Any,
    user: dict,
) -> Dict[str, Any]:
    access = await require_plus_access(
        db,
        user,
    )

    if not access["can_use_ai"]:
        raise HTTPException(
            status_code=403,
            detail=(
                "NotMe AI requires an active NotMe Plus "
                "trial or subscription."
            ),
        )

    return access%                                              
