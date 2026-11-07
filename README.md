for key in EXPO_PUBLIC_SUPABASE_URL EXPO_PUBLIC_SUPABASE_ANON_KEY; do
  if grep -qE "^${key}=.+" .env; then
    echo "$key: CONFIGURED"
  else
    echo "$key: MISSING OR EMPTY"
  fi
done
