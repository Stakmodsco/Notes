mike@Mikes-MacBook-Pro Safeguard-app-my-fix-main % cd /Users/mike/Desktop/Safeguard-app-my-fix-main && sed -n '927,990p' backend/server.py

    record = await collection.find_one(
        {"user_id": user_id},
        {
            "_id": 0,
            "user_id": 1,
            "trial_started_at": 1,
            "trial_ends_at": 1,
        },
    )

    if not record:
        logger.error(
            "[SUBSCRIPTION] Trial record unavailable for user %s",
            user_id,
        )
        raise HTTPException(
            status_code=503,
            detail="Subscription access is temporarily unavailable.",
        )

    trial_started_at = record.get("trial_started_at")
    trial_ends_at = record.get("trial_ends_at")

    if not isinstance(trial_started_at, datetime) or not isinstance(
        trial_ends_at,
        datetime,
    ):
        logger.error(
            "[SUBSCRIPTION] Invalid trial timestamps for user %s",
            user_id,
        )
        raise HTTPException(
            status_code=503,
            detail="Subscription access is temporarily unavailable.",
        )

    is_trial_active = now < trial_ends_at

    remaining_seconds = max(
        0.0,
        (trial_ends_at - now).total_seconds(),
    )

    trial_days_remaining = (
        int((remaining_seconds + 86399) // 86400)
        if is_trial_active
        else 0
    )

    return {
        "trialStartedAt": trial_started_at.isoformat() + "Z",
        "trialEndsAt": trial_ends_at.isoformat() + "Z",
        "isTrialActive": is_trial_active,
        "trialDaysRemaining": trial_days_remaining,
        "trialDocumentLimit": None,
        "trialDurationDays": trial_duration_days,
    }

@api_router.get("/health")
async def health_check():
    return {"status": "healthy", "timestamp": datetime.utcnow().isoformat(),
            "revision": os.environ.get("RENDER_GIT_COMMIT") or None}

@api_router.post("/status", response_model=StatusCheck)
