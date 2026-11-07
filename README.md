mike@Mikes-MacBook-Pro frontend % for f in .env .env.local .env.development .env.development.local; do
  if [ -f "$f" ]; then
    echo "$f exists"
  else
    echo "$f missing"
  fi
done
.env missing
.env.local missing
.env.development missing
.env.development.local missing
mike@Mikes-MacBook-Pro frontend % for key in EXPO_PUBLIC_SUPABASE_URL EXPO_PUBLIC_SUPABASE_ANON_KEY EXPO_PUBLIC_BACKEND_URL; do
  if grep -q "^${key}=" .env 2>/dev/null || grep -q "^${key}=" .env.local 2>/dev/null; then
    echo "$key: present in environment file"
  else
    echo "$key: not found in .env or .env.local"
  fi
done

EXPO_PUBLIC_SUPABASE_URL: not found in .env or .env.local
EXPO_PUBLIC_SUPABASE_ANON_KEY: not found in .env or .env.local
EXPO_PUBLIC_BACKEND_URL: not found in .env or .env.local
mike@Mikes-MacBook-Pro frontend % 
