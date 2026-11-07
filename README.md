mike@Mikes-MacBook-Pro frontend % cp /Users/mike/Desktop/Safeguard-app-my-fix-main/frontend/.env \
   /Users/mike/Desktop/NotMe-main/frontend/.env

chmod 600 /Users/mike/Desktop/NotMe-main/frontend/.env
mike@Mikes-MacBook-Pro frontend % cd /Users/mike/Desktop/NotMe-main/frontend

for key in EXPO_PUBLIC_ENV EXPO_PUBLIC_BACKEND_URL EXPO_PUBLIC_SUPABASE_URL EXPO_PUBLIC_SUPABASE_ANON_KEY EXPO_PUBLIC_REVENUECAT_IOS_KEY EXPO_PUBLIC_REVENUECAT_ANDROID_KEY; do
  if grep -q "^${key}=." .env; then
    echo "$key: SET"
  else
    echo "$key: MISSING"
  fi
done

npx expo config --type public
EXPO_PUBLIC_ENV: SET
EXPO_PUBLIC_BACKEND_URL: SET
EXPO_PUBLIC_SUPABASE_URL: SET
EXPO_PUBLIC_SUPABASE_ANON_KEY: SET
EXPO_PUBLIC_REVENUECAT_IOS_KEY: SET
EXPO_PUBLIC_REVENUECAT_ANDROID_KEY: SET
env: load .env
env: export zsh Environment EXPO_PUBLIC_BACKEND_URL EXPO_PUBLIC_ENV EXPO_PUBLIC_REVENUECAT_ANDROID_KEY EXPO_PUBLIC_REVENUECAT_IOS_KEY EXPO_PUBLIC_SUPABASE_ANON_KEY EXPO_PUBLIC_SUPABASE_URL
[app.config.js] Building with:
  EXPO_PUBLIC_BACKEND_URL: https://safeguard-api-rtiu.onrender.com
  EXPO_PUBLIC_SUPABASE_URL: configured
  Environment: production

{
  name: 'NotMe',
  slug: 'safeguard',
  version: '1.0.0',
  orientation: 'portrait',
  icon: './assets/images/icon.png',
  scheme: 'safeguard',
  userInterfaceStyle: 'automatic',
  newArchEnabled: true,
  owner: 'filaend',
  plugins: [
    './plugins/with-ios-pod-minimum-target',
    'expo-router',
    [
      'expo-splash-screen',
      {
        image: './assets/images/splash-icon.png',
        imageWidth: 160,
        resizeMode: 'contain',
        backgroundColor: '#F8FBFF'
      }
    ],
    [
      'expo-notifications',
      {
        icon: './assets/images/icon.png',
        color: '#2563EB'
      }
    ],
    [
      'expo-local-authentication',
      {
        faceIDPermission: 'Allow $(PRODUCT_NAME) to use Face ID for secure authentication'
      }
    ],
    [
      'expo-image-picker',
      {
        microphonePermission: false
      }
    ],
    'expo-document-picker',
    '@react-native-community/datetimepicker',
    'expo-secure-store',
    'expo-sqlite',
    'expo-web-browser',
    'expo-localization',
    'expo-font'
  ],
  description: undefined,
  sdkVersion: '54.0.0',
  platforms: [
    'ios',
    'android',
    'web'
  ],
  ios: {
    supportsTablet: false,
    bundleIdentifier: 'com.filaend.safeguard',
    buildNumber: '2',
    infoPlist: {
      NSCameraUsageDescription: 'Scan documents to track expiry dates',
      NSPhotoLibraryUsageDescription: 'Import documents from your gallery',
      NSFaceIDUsageDescription: 'Unlock NotMe securely with Face ID',
      ITSAppUsesNonExemptEncryption: false
    }
  },
  android: {
    package: 'com.filaend.safeguard',
    versionCode: 1,
    softwareKeyboardLayoutMode: 'resize',
    edgeToEdgeEnabled: true,
    permissions: [
      'android.permission.CAMERA',
      'android.permission.VIBRATE',
      'android.permission.POST_NOTIFICATIONS',
      'com.android.vending.BILLING',
      'android.permission.USE_BIOMETRIC',
      'android.permission.USE_FINGERPRINT'
    ],
    allowBackup: false,
    blockedPermissions: [
      'android.permission.RECORD_AUDIO',
      'android.permission.READ_EXTERNAL_STORAGE',
      'android.permission.WRITE_EXTERNAL_STORAGE',
      'android.permission.SCHEDULE_EXACT_ALARM'
    ],
    adaptiveIcon: {
      foregroundImage: './assets/images/adaptive-icon.png',
      backgroundColor: '#3151AA'
    }
  },
  web: {
    bundler: 'metro',
    output: 'static',
    favicon: './assets/images/favicon.png'
  },
  experiments: {
    typedRoutes: true
  },
  extra: {
    EXPO_PUBLIC_BACKEND_URL: '[VALUE HIDDEN]',
    EXPO_PUBLIC_SUPABASE_URL: '[VALUE HIDDEN]',
    EXPO_PUBLIC_SUPABASE_ANON_KEY: '[VALUE HIDDEN]',
    EXPO_PUBLIC_REVENUECAT_IOS_KEY: '[VALUE HIDDEN]',
    EXPO_PUBLIC_REVENUECAT_ANDROID_KEY: '[VALUE HIDDEN]',
    EXPO_PUBLIC_ENV: '[VALUE HIDDEN]',
    eas: {
      projectId: 'd64dedd5-e5bb-4bbf-8aa6-01e482e9f295'
    },
    router: {}
  },
  androidStatusBar: {
    backgroundColor: '#F8FBFF'
  }
}
