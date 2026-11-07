mike@Mikes-MacBook-Pro frontend % cd /Users/mike/Desktop/NotMe-main/frontend

printf "\n=== EXPO DOCTOR ===\n"
npx expo-doctor

printf "\n=== XCODE ===\n"
xcodebuild -version

printf "\n=== COCOAPODS ===\n"
pod --version

printf "\n=== IOS DIRECTORY ===\n"
if [ -d ios ]; then echo "ios directory exists"; else echo "ios directory missing"; fi

printf "\n=== GIT STATUS ===\n"
git status --short


=== EXPO DOCTOR ===
18/18 checks passed. No issues detected!

=== XCODE ===
Xcode 26.5
Build version 17F42

=== COCOAPODS ===
1.17.0

=== IOS DIRECTORY ===
ios directory missing

=== GIT STATUS ===
