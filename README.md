grep -n -A35 -B5 -E 'platform :ios|post_install|notme_minimum' ios/Podfile && cat ios/Podfile.properties.json
