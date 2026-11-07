mike@Mikes-MacBook-Pro ios % cd /Users/mike/Desktop/NotMe-main/frontend/ios

printf "\n=== XCODE SCHEMES ===\n"
xcodebuild -list -workspace NotMe.xcworkspace

printf "\n=== AVAILABLE IPHONE SIMULATORS ===\n"
xcrun simctl list devices available | grep -E 'iPhone'


=== XCODE SCHEMES ===
Command line invocation:
    /Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild -list -workspace NotMe.xcworkspace

Information about workspace "NotMe":
    Schemes:
        EXApplication
        EXApplication-ExpoApplication_privacy
        EXConstants
        EXConstants-EXConstants
        EXConstants-ExpoConstants_privacy
        EXImageLoader
        EXJSONUtils
        EXManifests
        EXNotifications
        EXNotifications-ExpoNotifications_privacy
        Expo
        expo-dev-client
        expo-dev-launcher
        expo-dev-launcher-EXDevLauncher
        expo-dev-menu
        expo-dev-menu-EXDevMenu
        expo-dev-menu-interface
        ExpoAsset
        ExpoBlur
        ExpoDevice
        ExpoDevice-ExpoDevice_privacy
        ExpoDocumentPicker
        ExpoFileSystem
        ExpoFileSystem-ExpoFileSystem_privacy
        ExpoFont
        ExpoHaptics
        ExpoHead
        ExpoImage
        ExpoImageManipulator
        ExpoImagePicker
        ExpoKeepAwake
        ExpoLinearGradient
        ExpoLinking
        ExpoLocalAuthentication
        ExpoLocalization
        ExpoLocalization-ExpoLocalization_privacy
        ExpoModulesCore
        ExpoSecureStore
        ExpoSharing
        ExpoSplashScreen
        ExpoSQLite
        ExpoSymbols
        ExpoSystemUI
        ExpoSystemUI-ExpoSystemUI_privacy
        ExpoWebBrowser
        EXUpdatesInterface
        FBLazyVector
        hermes-engine
        libavif
        libdav1d
        libwebp
        NotMe
        Pods-NotMe
        PurchasesHybridCommon
        PurchasesHybridCommon-PurchasesHybridCommon
        RCTDeprecation
        RCTRequired
        RCTTypeSafety
        React
        React-callinvoker
        React-Core
        React-Core-prebuilt
        React-Core-React-Core_privacy
        React-CoreModules
        React-cxxreact
        React-cxxreact-React-cxxreact_privacy
        React-debug
        React-defaultsnativemodule
        React-domnativemodule
        React-Fabric
        React-FabricComponents
        React-FabricImage
        React-featureflags
        React-featureflagsnativemodule
        React-graphics
        React-hermes
        React-idlecallbacksnativemodule
        React-ImageManager
        React-jserrorhandler
        React-jsi
        React-jsiexecutor
        React-jsinspector
        React-jsinspectorcdp
        React-jsinspectornetwork
        React-jsinspectortracing
        React-jsitooling
        React-jsitracing
        React-logger
        React-Mapbuffer
        React-microtasksnativemodule
        react-native-get-random-values
        react-native-keyboard-controller
        react-native-safe-area-context
        react-native-webview
        React-NativeModulesApple
        React-oscompat
        React-perflogger
        React-performancetimeline
        React-RCTActionSheet
        React-RCTAnimation
        React-RCTAppDelegate
        React-RCTBlob
        React-RCTFabric
        React-RCTFBReactNativeSpec
        React-RCTImage
        React-RCTLinking
        React-RCTNetwork
        React-RCTRuntime
        React-RCTSettings
        React-RCTText
        React-RCTVibration
        React-rendererconsistency
        React-renderercss
        React-rendererdebug
        React-RuntimeApple
        React-RuntimeCore
        React-runtimeexecutor
        React-RuntimeHermes
        React-runtimescheduler
        React-timing
        React-utils
        ReactAppDependencyProvider
        ReactCodegen
        ReactCommon
        ReactNativeDependencies
        RevenueCat
        RevenueCat-RevenueCat
        RNCAsyncStorage
        RNCAsyncStorage-RNCAsyncStorage_resources
        RNDateTimePicker
        RNGestureHandler
        RNPurchases
        RNReanimated
        RNScreens
        RNSVG
        RNSVG-RNSVGFilters
        RNWorklets
        SDWebImage
        SDWebImage-SDWebImage
        SDWebImageAVIFCoder
        SDWebImageSVGCoder
        SDWebImageWebPCoder
        Yoga


=== AVAILABLE IPHONE SIMULATORS ===
    iPhone 16 Pro (505C67A8-F1AC-4571-8FB5-F5D1A86419E4) (Shutdown) 
    iPhone 16 Pro Max (12A2EBEA-33DE-4335-B7DA-753299959607) (Shutdown) 
    iPhone 16e (D09ED350-CDAF-49CB-8A11-9D9CDBE128FA) (Shutdown) 
    iPhone 16 (32B5F0C5-181D-4CB7-8455-E5017518AEFC) (Shutdown) 
    iPhone 16 Plus (A6368951-A138-43BE-960E-A3987D9D356B) (Shutdown) 
    iPhone SE (3rd generation) (CA7662F5-2F49-47B6-8CB1-38F3F1EDE791) (Shutdown) 
    iPhone 17 Pro (93364794-6476-4515-BBDD-76BC3CC9DD53) (Shutdown) 
    iPhone 17 Pro Max (374BF87C-8880-4095-A319-9D8FB0241D8C) (Shutdown) 
    iPhone 17e (DC6921E5-088A-499A-9662-1AF7006D029B) (Shutdown) 
    iPhone Air (AD2050F3-9744-44B5-97A8-A9D579093F72) (Shutdown) 
    iPhone 17 (F1EF611E-31B0-4B22-8E5F-A199BE8110AC) (Shutdown) 
mike@Mikes-MacBook-Pro ios % 
