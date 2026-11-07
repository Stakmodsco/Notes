mkdir -p "$HOME/.npm-global"
npm config set prefix "$HOME/.npm-global"

echo 'export PATH="$HOME/.npm-global/bin:$PATH"' >> ~/.zshrc

source ~/.zshrc

npm install -g yarn@1.22.22

yarn --version

