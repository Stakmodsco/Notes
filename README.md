corepack yarn --version

node -p "require('./package.json').packageManager || 'Not specified'"

node -p "JSON.stringify(require('./package.json').engines || 'No engines specified', null, 2)"

head -n 8 yarn.lock
