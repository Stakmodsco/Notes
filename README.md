mike@Mikes-MacBook-Pro Safeguard-app-my-fix-main % cd /Users/mike/Desktop/Safeguard-app-my-fix-main && grep -n -A55 -B12 'subscription/access' backend/server.py

873-        else:
874-            # Generic user-friendly message for unknown errors
875-            raise HTTPException(
876-                status_code=500, 
877-                detail="This document could not be scanned. Please try another image or enter details manually."
878-            )
879-
880-# Routes
881-@api_router.get("/")
882-async def root():
883-    return {"message": "Safeguard API is running", "version": "1.0.0"}
884-
885:@api_router.get("/subscription/access")
886-async def get_subscription_access(
887-    user: dict = Depends(require_auth),
888-):
889-    """
890-    Return the authenticated account's server-authoritative NotMe trial.
891-
892-    The trial is created once per Supabase user ID and never restarted by
893-    reinstalling the app, clearing local storage, signing out, or using a
894-    different device.
895-
896-    Paid subscription entitlement remains handled by RevenueCat. This
897-    endpoint is authoritative only for the app-managed 30-day trial.
898-    """
899-    user_id = user.get("id")
900-    if not user_id:
901-        raise HTTPException(
902-            status_code=401,
903-            detail="Authenticated user ID is unavailable.",
904-        )
905-
906-    now = datetime.utcnow()
907-    trial_duration_days = 30
908-
909-    collection = db["subscription_access"]
910-
911-    await collection.update_one(
912-        {"user_id": user_id},
913-        {
914-            "$setOnInsert": {
915-                "user_id": user_id,
916-                "trial_started_at": now,
917-                "trial_ends_at": now + timedelta(days=trial_duration_days),
918-                "created_at": now,
919-            },
920-            "$set": {
921-                "updated_at": now,
922-            },
923-        },
924-        upsert=True,
925-    )
926-
927-    record = await collection.find_one(
928-        {"user_id": user_id},
929-        {
930-            "_id": 0,
931-            "user_id": 1,
932-            "trial_started_at": 1,
933-            "trial_ends_at": 1,
934-        },
935-    )
936-
937-    if not record:
938-        logger.error(
939-            "[SUBSCRIPTION] Trial record unavailable for user %s",
940-            user_id,
