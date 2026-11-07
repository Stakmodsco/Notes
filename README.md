mike@Mikes-MacBook-Pro frontend % brew upgrade ruby
==> Downloading bottle manifests
✔︎ Bottle Manifest ruby (4.0.9)                                              Downloaded   67.8KB/ 67.8KB
==> Would install 1 dependency:
openssl@4 4.1.0
==> Would upgrade 1 dependency:
ca-certificates 2022-07-19_1 -> 2026-11-05
==> Would upgrade 1 requested outdated package
ruby 3.1.2_1 -> 4.0.9 (17.1MB)
==> Do you want to proceed with the upgrade? [y/n]
==> Fetching downloads for: ruby
✔︎ Bottle Manifest ca-certificates (2026-11-05)                              Downloaded    8.1KB/  8.1KB
✔︎ Bottle Manifest openssl@4 (4.1.0)                                         Downloaded   19.6KB/ 19.6KB
✔︎ Bottle ca-certificates (2026-11-05)                                       Downloaded  109.6KB/109.6KB
✔︎ Bottle openssl@4 (4.1.0)                                                  Downloaded   11.5MB/ 11.5MB
✔︎ Bottle ruby (4.0.9)                                                       Downloaded   17.1MB/ 17.1MB
==> Upgrading ruby
  3.1.2_1 -> 4.0.9 
==> Installing dependencies for ruby: ca-certificates ↑ and openssl@4
==> Upgrading ruby dependency: ca-certificates (2022-07-19_1 -> 2026-11-05)
==> Pouring ca-certificates--2026-11-05.all.bottle.tar.gz
🍺  /opt/homebrew/Cellar/ca-certificates/2026-11-05: 5 files, 196.1KB
==> Installing ruby dependency: openssl@4 (4.1.0)
==> Pouring openssl@4--4.1.0.arm64_tahoe.bottle.tar.gz
🍺  /opt/homebrew/Cellar/openssl@4/4.1.0: 6,869 files, 24.6MB
==> Installing ruby
==> Pouring ruby--4.0.9.arm64_tahoe.bottle.tar.gz
🍺  /opt/homebrew/Cellar/ruby/4.0.9: 19,320 files, 59.5MB
==> Cleanup
Removing: /opt/homebrew/Cellar/ruby/3.1.2_1... (15,996 files, 44.8MB)
Disable this behaviour by setting `HOMEBREW_NO_INSTALL_CLEANUP=1`.
Hide these hints with `HOMEBREW_NO_ENV_HINTS=1` (see `man brew`).
==> `brew cleanup` has not been run in the last 30 days, running now...
Disable this behaviour by setting `HOMEBREW_NO_INSTALL_CLEANUP=1`.
Hide these hints with `HOMEBREW_NO_ENV_HINTS=1` (see `man brew`).
Removing: /opt/homebrew/Cellar/ca-certificates/2022-07-19_1... (3 files, 228KB)
Removing: /Users/mike/Library/Caches/Homebrew/mint--0.17.5... (437.5KB)
Removing: /Users/mike/Library/Caches/Homebrew/mint_bottle_manifest--0.17.5... (11.8KB)
Removing: /Users/mike/Library/Caches/Homebrew/portable-ruby-3.1.4.arm64_big_sur.bottle.tar.gz... (13MB)
Pruned 2 symbolic links and 2 directories from /opt/homebrew
==> Upgraded 1 requested outdated package
ruby 3.1.2_1 -> 4.0.9
