for f in .env .env.local .env.development .env.development.local; do
  if [ -f "$f" ]; then
    echo "$f exists"
  else
    echo "$f missing"
  fi
done


for key in EXPO_PUBLIC_SUPABASE_URL EXPO_PUBLIC_SUPABASE_ANON_KEY EXPO_PUBLIC_BACKEND_URL; do
  if grep -q "^${key}=" .env 2>/dev/null || grep -q "^${key}=" .env.local 2>/dev/null; then
    echo "$key: present in environment file"
  else
    echo "$key: not found in .env or .env.local"
  fi
done
