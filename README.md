mike@Mikes-MacBook-Pro frontend % npx expo config
[app.config.js] Building with:
  EXPO_PUBLIC_BACKEND_URL: https://safeguard-api-rtiu.onrender.com
  EXPO_PUBLIC_SUPABASE_URL: NOT SET
  Environment: development

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
    },
    config: {
      usesNonExemptEncryption: false
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
    EXPO_PUBLIC_BACKEND_URL: 'https://safeguard-api-rtiu.onrender.com',
    EXPO_PUBLIC_SUPABASE_URL: '',
    EXPO_PUBLIC_SUPABASE_ANON_KEY: '',
    EXPO_PUBLIC_REVENUECAT_IOS_KEY: '',
    EXPO_PUBLIC_REVENUECAT_ANDROID_KEY: '',
    EXPO_PUBLIC_ENV: 'development',
    eas: {
      projectId: 'd64dedd5-e5bb-4bbf-8aa6-01e482e9f295'
    },
    router: {}
  },
  _internal: {
    isDebug: false,
    projectRoot: '/Users/mike/Desktop/Safeguard-app-my-fix-main/frontend',
    dynamicConfigPath: '/Users/mike/Desktop/Safeguard-app-my-fix-main/frontend/app.config.js',
    staticConfigPath: '/Users/mike/Desktop/Safeguard-app-my-fix-main/frontend/app.json',
    packageJsonPath: '/Users/mike/Desktop/Safeguard-app-my-fix-main/frontend/package.json',
    pluginHistory: {
      'expo-splash-screen': {
        name: 'expo-splash-screen',
        version: '31.0.13'
      },
      'expo-notifications': {
        name: 'expo-notifications',
        version: '0.32.17'
      },
      'expo-local-authentication': {
        name: 'expo-local-authentication',
        version: '17.0.9'
      },
      'expo-image-picker': {
        name: 'expo-image-picker',
        version: '17.0.11'
      },
      'expo-document-picker': {
        name: 'expo-document-picker',
        version: '14.0.8'
      },
      'expo-secure-store': {
        name: 'expo-secure-store',
        version: '15.0.8'
      },
      'expo-sqlite': {
        name: 'expo-sqlite',
        version: '16.0.10'
      },
      'expo-web-browser': {
        name: 'expo-web-browser',
        version: '15.0.11'
      },
      'expo-font': {
        name: 'expo-font',
        version: '14.0.12'
      }
    }
  },
  androidStatusBar: {
    backgroundColor: '#F8FBFF'
  }
}

