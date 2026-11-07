cd /Users/mike/Desktop/NotMe-main/frontend

printf "\n=== NODE ===\n"
node -v

printf "\n=== YARN ===\n"
yarn -v

printf "\n=== DEPENDENCIES ===\n"
if [ -d node_modules ]; then echo "node_modules exists"; else echo "node_modules missing"; fi

printf "\n=== ENVIRONMENT FILE ===\n"
if [ -f .env ]; then echo ".env exists"; else echo ".env missing"; fi

printf "\n=== EXPO CONFIG ===\n"
npx expo config --type public
