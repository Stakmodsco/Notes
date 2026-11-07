cd /Users/mike/Desktop

gh auth status

git clone https://github.com/Stakmodsco/Safeguard-app-my-fix.git NotMe-main

cd NotMe-main

git switch main

git pull origin main

printf "\n=== LATEST COMMIT ===\n"
git log -1 --oneline

printf "\n=== GIT STATUS ===\n"
git status
