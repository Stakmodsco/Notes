xcrun simctl boot 93364794-6476-4515-BBDD-76BC3CC9DD53
open -a Simulator

xcrun simctl install booted /Users/mike/Desktop/NotMe-main/frontend/ios/build/DerivedData/Build/Products/Debug-iphonesimulator/NotMe.app
xcrun simctl launch booted com.filaend.safeguard
