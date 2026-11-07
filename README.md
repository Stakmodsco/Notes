mike@Mikes-MacBook-Pro frontend % cd /Users/mike/Desktop/NotMe-main/frontend

printf "\n=== NEW PROJECT ===\n"
ls -la .env

printf "\n=== OLD PROJECT ===\n"
ls -la /Users/mike/Desktop/Safeguard-app-my-fix-main/frontend/.env

printf "\n=== REQUIRED VARIABLE NAMES ===\n"
grep -nE 'EXPO_PUBLIC_[A-Z0-9_]+' .env.example | sed 's/=.*/= [VALUE HIDDEN]/'


=== NEW PROJECT ===
ls: .env: No such file or directory

=== OLD PROJECT ===
-rw-r--r--  1 mike  staff  1249 Nov  7 19:05 /Users/mike/Desktop/Safeguard-app-my-fix-main/frontend/.env

=== REQUIRED VARIABLE NAMES ===
9:EXPO_PUBLIC_ENV= [VALUE HIDDEN]
12:EXPO_PUBLIC_BACKEND_URL= [VALUE HIDDEN]
13:EXPO_PUBLIC_SUPABASE_URL= [VALUE HIDDEN]
14:EXPO_PUBLIC_SUPABASE_ANON_KEY= [VALUE HIDDEN]
19:EXPO_PUBLIC_REVENUECAT_IOS_KEY= [VALUE HIDDEN]
20:EXPO_PUBLIC_REVENUECAT_ANDROID_KEY= [VALUE HIDDEN]
