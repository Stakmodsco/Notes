mike@Mikes-MacBook-Pro Safeguard-app-my-fix-main % 
mike@Mikes-MacBook-Pro Safeguard-app-my-fix-main % cd /Users/mike/Desktop

git clone https://github.com/Stakmodsco/Safeguard-app-my-fix.git NotMe-main

cd NotMe-main

git switch main

git pull origin main

printf "\n=== LATEST COMMIT ===\n"
git log -1 --oneline

printf "\n=== GIT STATUS ===\n"
git status

Cloning into 'NotMe-main'...
remote: Invalid username or token. Password authentication is not supported for Git operations.
fatal: Authentication failed for 'https://github.com/Stakmodsco/Safeguard-app-my-fix.git/'
cd: no such file or directory: NotMe-main
fatal: not a git repository (or any of the parent directories): .git
fatal: not a git repository (or any of the parent directories): .git

=== LATEST COMMIT ===
fatal: not a git repository (or any of the parent directories): .git

=== GIT STATUS ===
fatal: not a git repository (or any of the parent directories): .git
