set -o pipefail
xcodebuild clean build -workspace NotMe.xcworkspace -scheme NotMe -configuration Debug -destination 'platform=iOS Simulator,id=93364794-6476-4515-BBDD-76BC3CC9DD53' -derivedDataPath build/DerivedData 2>&1 | grep -E 'error:|BUILD SUCCEEDED|BUILD FAILED'
