cp /Users/mike/Desktop/Safeguard-app-my-fix-main/frontend/.env \
   /Users/mike/Desktop/NotMe-main/frontend/.env

chmod 600 /Users/mike/Desktop/NotMe-main/frontend/.env

cd /Users/mike/Desktop/NotMe-main/frontend

for key in EXPO_PUBLIC_ENV EXPO_PUBLIC_BACKEND_URL EXPO_PUBLIC_SUPABASE_URL EXPO_PUBLIC_SUPABASE_ANON_KEY EXPO_PUBLIC_REVENUECAT_IOS_KEY EXPO_PUBLIC_REVENUECAT_ANDROID_KEY; do
  if grep -q "^${key}=." .env; then
    echo "$key: SET"
  else
    echo "$key: MISSING"
  fi
done

npx expo config --type public
