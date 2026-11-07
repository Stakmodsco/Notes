mike@Mikes-MacBook-Pro Safeguard-app-my-fix-main % cd /Users/mike/Desktop/Safeguard-app-my-fix-main && find . -maxdepth 4 -type f \( -path '*/api/*' -o -path '*/server/*' -o -path '*/backend/*' -o -path '*/supabase/*' \) -not -path '*/node_modules/*' -not -path '*/.git/*' | head -80

./backend/subscription_access.py
./backend/ai_gateway.py
./backend/legal_pages.py
./backend/server.py
./backend/requirements.txt
./backend/ai_chat.py
./backend/check_deployment.py
./backend/test_rate_limiter_bounds.py
./backend/security.py
./backend/requirements-deletion-tests.txt
./backend/tests/test_security_boundaries.py
./backend/tests/test_account_deletion_proof.py
./backend/test_account_deletion_deploy_check.py
./backend/test_account_deletion_mfa.py
./backend/public_errors.py
./backend/email_service.py
./backend/test_data_minimization.py
./backend/test_public_errors.py
./backend/ops/audit_access_policies.sql
./backend/.env.example
./backend/install_ai_chat.py
./backend/check_public_pages.py
./backend/test_account_deletion_flow.py
./backend/access_control.py
./backend/test_security_hardening.py
mike@Mikes-MacBook-Pro Safeguard-app-my-fix-main % 
