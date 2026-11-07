mike@Mikes-MacBook-Pro Desktop % cd /Users/mike/Desktop

gh auth status

git clone https://github.com/Stakmodsco/Safeguard-app-my-fix.git NotMe-main

cd NotMe-main

git switch main

git pull origin main

printf "\n=== LATEST COMMIT ===\n"
git log -1 --oneline

printf "\n=== GIT STATUS ===\n"
git status

github.com
  ✓ Logged in to github.com account Stakmodsco (keyring)
  - Active account: true
  - Git operations protocol: https
  - Token: gho_************************************
  - Token scopes: 'gist', 'read:org', 'repo', 'workflow'
Cloning into 'NotMe-main'...
remote: Enumerating objects: 24354, done.
remote: Counting objects: 100% (1520/1520), done.
remote: Compressing objects: 100% (543/543), done.
remote: Total 24354 (delta 1089), reused 1095 (delta 969), pack-reused 22834 (from 2)
Receiving objects: 100% (24354/24354), 101.61 MiB | 1.79 MiB/s, done.
Resolving deltas: 100% (14370/14370), done.
Already on 'main'
Your branch is up to date with 'origin/main'.
From https://github.com/Stakmodsco/Safeguard-app-my-fix
 * branch              main       -> FETCH_HEAD
Already up to date.

=== LATEST COMMIT ===
2ba41a11 (HEAD -> main, origin/main, origin/HEAD) Merge pull request #7 from Stakmodsco/fix/remove-obsolete-store-trial

=== GIT STATUS ===
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
