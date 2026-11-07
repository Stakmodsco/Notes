cd /Users/mike/Desktop/NotMe-main/frontend/ios

printf "\n=== XCODE SCHEMES ===\n"
xcodebuild -list -workspace NotMe.xcworkspace

printf "\n=== AVAILABLE IPHONE SIMULATORS ===\n"
xcrun simctl list devices available | grep -E 'iPhone'
