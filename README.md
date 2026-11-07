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

mike@Mikes-MacBook-Pro frontend % sed -n '1,220p' app.config.js
/**
 * Expo App Configuration
 * 
 * This file dynamically injects environment variables into the app config
 * for both development (expo start) and EAS builds.
 * 
 * EAS builds receive env vars from eas.json build profiles.
 * This config ensures they're accessible via Constants.expoConfig.extra
 */

module.exports = ({ config }) => {
  require('./scripts/public-config-guard.cjs').assertPublicConfig(process.env, config.extra || {});
  // Get backend URL from environment (EAS injects these during build)
  const backendUrl = process.env.EXPO_PUBLIC_BACKEND_URL || 
                     'https://safeguard-api-rtiu.onrender.com';
  
  // Get Supabase config
  const supabaseUrl = process.env.EXPO_PUBLIC_SUPABASE_URL || '';
  const supabaseAnonKey = process.env.EXPO_PUBLIC_SUPABASE_ANON_KEY || '';
  
  // Get RevenueCat keys
  const revenueCatIosKey = process.env.EXPO_PUBLIC_REVENUECAT_IOS_KEY || '';
  const revenueCatAndroidKey = process.env.EXPO_PUBLIC_REVENUECAT_ANDROID_KEY || '';
  
  console.log('[app.config.js] Building with:');
  console.log('  EXPO_PUBLIC_BACKEND_URL:', backendUrl);
  console.log('  EXPO_PUBLIC_SUPABASE_URL:', supabaseUrl ? 'configured' : 'NOT SET');
  console.log('  Environment:', process.env.EXPO_PUBLIC_ENV || 'development');

  return {
    ...config,
    extra: {
      ...config.extra,
      // Inject environment variables into the app
      EXPO_PUBLIC_BACKEND_URL: backendUrl,
      EXPO_PUBLIC_SUPABASE_URL: supabaseUrl,
      EXPO_PUBLIC_SUPABASE_ANON_KEY: supabaseAnonKey,
      EXPO_PUBLIC_REVENUECAT_IOS_KEY: revenueCatIosKey,
      EXPO_PUBLIC_REVENUECAT_ANDROID_KEY: revenueCatAndroidKey,
      EXPO_PUBLIC_ENV: process.env.EXPO_PUBLIC_ENV || 'development',
      // EAS project config
      eas: {
        projectId: 'd64dedd5-e5bb-4bbf-8aa6-01e482e9f295',
      },
      router: {},
    },
  };
};
