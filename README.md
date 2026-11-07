mike@Mikes-MacBook-Pro Safeguard-app-my-fix-main % cd /Users/mike/Desktop/Safeguard-app-my-fix-main && grep -RniE 'trial_started_at|trial_ends_at|subscription_access\.(delete|update|insert)|subscription_access\[' backend --include='*.py' --exclude='test_*'

backend/subscription_access.py:50:        await db.subscription_access.update_one(
backend/subscription_access.py:55:                    "trial_started_at": now,
backend/subscription_access.py:56:                    "trial_ends_at": now + timedelta(days=30),
backend/subscription_access.py:76:    trial_started_at = _normalize_datetime(
backend/subscription_access.py:77:        record.get("trial_started_at")
backend/subscription_access.py:79:    trial_ends_at = _normalize_datetime(
backend/subscription_access.py:80:        record.get("trial_ends_at")
backend/subscription_access.py:83:    if not trial_ends_at:
backend/subscription_access.py:86:            "trial_started_at": trial_started_at,
backend/subscription_access.py:87:            "trial_ends_at": None,
backend/subscription_access.py:91:        "active": _utc_now() < trial_ends_at,
backend/subscription_access.py:92:        "trial_started_at": trial_started_at,
backend/subscription_access.py:93:        "trial_ends_at": trial_ends_at,
backend/server.py:916:                "trial_started_at": now,
backend/server.py:917:                "trial_ends_at": now + timedelta(days=trial_duration_days),
backend/server.py:932:            "trial_started_at": 1,
backend/server.py:933:            "trial_ends_at": 1,
backend/server.py:947:    trial_started_at = record.get("trial_started_at")
backend/server.py:948:    trial_ends_at = record.get("trial_ends_at")
backend/server.py:950:    if not isinstance(trial_started_at, datetime) or not isinstance(
backend/server.py:951:        trial_ends_at,
backend/server.py:963:    is_trial_active = now < trial_ends_at
backend/server.py:967:        (trial_ends_at - now).total_seconds(),
backend/server.py:977:        "trialStartedAt": trial_started_at.isoformat() + "Z",
backend/server.py:978:        "trialEndsAt": trial_ends_at.isoformat() + "Z",
backend/server.py:1619:                mongo_subscription_access = await db.subscription_access.delete_many(
backend/server.py:1638:                    f"mongodb_subscription_access_deleted:{mongo_subscription_access.deleted_count}"
backend/server.py:1646:                    f"subscription_access={mongo_subscription_access.deleted_count}"
mike@Mikes-MacBook-Pro Safeguard-app-my-fix-main % cd /Users/mike/Desktop/Safeguard-app-my-fix-main && grep -RniE '7[[:space:]_-]*day|five documents|5 documents|5 uploads|MAX_DOCUMENTS[[:space:]]*[:=][[:space:]]*5|DURATION_DAYS[[:space:]]*:[[:space:]]*7|documentLimit[[:space:]]*[:=][[:space:]]*5|uploadLimit' frontend/app frontend/src backend --exclude-dir=node_modules --exclude-dir=__pycache__

frontend/src/i18n/locales/en.json:1203:      "approve": "Grant {{name}} read-only access to your documents for 7 days?",
frontend/src/services/revenuecat.ts:302:      return { hasFreeTrial: true, trialDays: 7, trialPeriodLabel: '7 days free' };
frontend/src/store/emergencyAccessStore.ts:29:  expiresAt: string; // Access expires after 7 days
frontend/src/store/emergencyAccessStore.ts:264:    // Create granted access (valid for 7 days)
