Last login: Sat Nov  7 15:57:38 on ttys000
mike@Mikes-MacBook-Pro ~ % cd /Users/mike/Desktop/Safeguard-app-my-fix-main/frontend
mike@Mikes-MacBook-Pro frontend % npm install -g yarn@1.22.22
npm error code EACCES
npm error syscall mkdir
npm error path /usr/local/lib/node_modules/yarn
npm error errno -13
npm error Error: EACCES: permission denied, mkdir '/usr/local/lib/node_modules/yarn'
npm error     at async mkdir (node:internal/fs/promises:1719:10)
npm error     at async /usr/local/lib/node_modules/npm/node_modules/@npmcli/arborist/lib/arborist/reify.js:624:20
npm error     at async Promise.allSettled (index 0)
npm error     at async #reifyPackages (/usr/local/lib/node_modules/npm/node_modules/@npmcli/arborist/lib/arborist/reify.js:353:11)
npm error     at async Arborist.reify (/usr/local/lib/node_modules/npm/node_modules/@npmcli/arborist/lib/arborist/reify.js:139:7)
npm error     at async Install.exec (/usr/local/lib/node_modules/npm/lib/commands/install.js:158:5)
npm error     at async Npm.exec (/usr/local/lib/node_modules/npm/lib/npm.js:193:9)
npm error     at async module.exports (/usr/local/lib/node_modules/npm/lib/cli/entry.js:67:5) {
npm error   errno: -13,
npm error   code: 'EACCES',
npm error   syscall: 'mkdir',
npm error   path: '/usr/local/lib/node_modules/yarn'
npm error }
npm error
npm error The operation was rejected by your operating system.
npm error It is likely you do not have the permissions to access this file as the current user
npm error
npm error If you believe this might be a permissions issue, please double-check the permissions of the file and its containing directories, or try running the command again as root/Administrator.
npm error A complete log of this run can be found in: /Users/mike/.npm/_logs/2026-11-07T13_08_50_693Z-debug-0.log
mike@Mikes-MacBook-Pro frontend % yarn --version
zsh: command not found: yarn
mike@Mikes-MacBook-Pro frontend % 
