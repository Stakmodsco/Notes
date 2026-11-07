mike@Mikes-MacBook-Pro frontend % cat .env.example
# SafeGuard frontend environment variables
# Copy to .env for local development only. Never commit .env.
#
# For EAS builds, create the same variables in the EAS environment named
# development, preview, or production. Values prefixed EXPO_PUBLIC_ are bundled
# into the client app and must never contain server-side secrets.

# Runtime environment: development | preview | production
EXPO_PUBLIC_ENV=development

# Public API and authentication configuration
EXPO_PUBLIC_BACKEND_URL=https://your-api.example.com
EXPO_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
EXPO_PUBLIC_SUPABASE_ANON_KEY=your-public-supabase-anon-key

# RevenueCat public app-specific SDK keys
# Use Test Store keys only for local/internal testing. Production EAS builds must
# use the iOS App Store and Android Play Store public SDK keys.
EXPO_PUBLIC_REVENUECAT_IOS_KEY=appl_your_public_ios_sdk_key
EXPO_PUBLIC_REVENUECAT_ANDROID_KEY=goog_your_public_android_sdk_key

# Optional Expo/Metro local-development overrides
EXPO_TUNNEL_SUBDOMAIN=
EXPO_PACKAGER_HOSTNAME=
EXPO_PACKAGER_PROXY_URL=
EXPO_USE_FAST_RESOLVER=1
METRO_CACHE_ROOT=

# EXPO_TOKEN and store submission credentials belong in CI/EAS credential
# storage, not in this frontend environment file.
