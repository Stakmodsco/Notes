cd /Users/mike/Desktop/NotMe-main/frontend

printf "\n=== NEW PROJECT ===\n"
ls -la .env

printf "\n=== OLD PROJECT ===\n"
ls -la /Users/mike/Desktop/Safeguard-app-my-fix-main/frontend/.env

printf "\n=== REQUIRED VARIABLE NAMES ===\n"
grep -nE 'EXPO_PUBLIC_[A-Z0-9_]+' .env.example | sed 's/=.*/= [VALUE HIDDEN]/'
