mike@Mikes-MacBook-Pro Safeguard-app-my-fix-main % cd /Users/mike/Desktop/Safeguard-app-my-fix-main && printf '\n=== SUBSCRIPTION ACCESS IMPORTS ===\n' && grep -nE 'subscription_access|get_subscription_access|require_plus_access|require_ai_access' backend/server.py backend/*.py


=== SUBSCRIPTION ACCESS IMPORTS ===
backend/server.py:2:from subscription_access import require_plus_access
backend/server.py:886:async def get_subscription_access(
backend/server.py:909:    collection = db["subscription_access"]
backend/server.py:1020:    await require_plus_access(db, user)
backend/server.py:1092:    await require_plus_access(db, user)
backend/server.py:1149:    await require_plus_access(db, user)
backend/server.py:1619:                mongo_subscription_access = await db.subscription_access.delete_many(
backend/server.py:1638:                    f"mongodb_subscription_access_deleted:{mongo_subscription_access.deleted_count}"
backend/server.py:1646:                    f"subscription_access={mongo_subscription_access.deleted_count}"
backend/server.py:2:from subscription_access import require_plus_access
backend/server.py:886:async def get_subscription_access(
backend/server.py:909:    collection = db["subscription_access"]
backend/server.py:1020:    await require_plus_access(db, user)
backend/server.py:1092:    await require_plus_access(db, user)
backend/server.py:1149:    await require_plus_access(db, user)
backend/server.py:1619:                mongo_subscription_access = await db.subscription_access.delete_many(
backend/server.py:1638:                    f"mongodb_subscription_access_deleted:{mongo_subscription_access.deleted_count}"
backend/server.py:1646:                    f"subscription_access={mongo_subscription_access.deleted_count}"
backend/subscription_access.py:38:    record = await db.subscription_access.find_one(
backend/subscription_access.py:50:        await db.subscription_access.update_one(
backend/subscription_access.py:66:        record = await db.subscription_access.find_one(
backend/subscription_access.py:306:async def get_subscription_access(
backend/subscription_access.py:424:async def require_plus_access(
backend/subscription_access.py:428:    access = await get_subscription_access(
backend/subscription_access.py:442:async def require_ai_access(
backend/subscription_access.py:446:    access = await require_plus_access(
backend/test_account_deletion_flow.py:33:MONGO = ('documents', 'user_preferences', 'user_settings', 'subscription_access')
backend/test_account_deletion_flow.py:67:                expected = {'$or': [{'user_id': OWNER}, {'_id': OWNER}]} if name == 'subscription_access' else {'user_id': OWNER}
