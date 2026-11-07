mike@Mikes-MacBook-Pro frontend % yarn install --frozen-lockfile
yarn install v1.22.22
[1/4] 🔍  Resolving packages...
warning Resolution field "postcss@8.5.18" is incompatible with requested version "postcss@~8.4.32"
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
warning " > babel-preset-expo@54.0.11" has unmet peer dependency "react-refresh@>=0.14.0 <1.0.0".
warning "eslint-config-expo > @typescript-eslint/eslint-plugin@8.37.0" has incorrect peer dependency "typescript@>=4.8.4 <5.9.0".
warning "eslint-config-expo > @typescript-eslint/parser@8.37.0" has incorrect peer dependency "typescript@>=4.8.4 <5.9.0".
warning "eslint-config-expo > @typescript-eslint/eslint-plugin > @typescript-eslint/type-utils@8.37.0" has incorrect peer dependency "typescript@>=4.8.4 <5.9.0".
warning "eslint-config-expo > @typescript-eslint/eslint-plugin > @typescript-eslint/utils@8.37.0" has incorrect peer dependency "typescript@>=4.8.4 <5.9.0".
warning "eslint-config-expo > @typescript-eslint/parser > @typescript-eslint/typescript-estree@8.37.0" has incorrect peer dependency "typescript@>=4.8.4 <5.9.0".
warning "eslint-config-expo > @typescript-eslint/parser > @typescript-eslint/typescript-estree > @typescript-eslint/project-service@8.37.0" has incorrect peer dependency "typescript@>=4.8.4 <5.9.0".
warning "eslint-config-expo > @typescript-eslint/parser > @typescript-eslint/typescript-estree > @typescript-eslint/tsconfig-utils@8.37.0" has incorrect peer dependency "typescript@>=4.8.4 <5.9.0".
warning Workspaces can only be enabled in private projects.
[4/4] 🔨  Building fresh packages...
✨  Done in 101.79s.
mike@Mikes-MacBook-Pro frontend % npx expo install --check
[app.config.js] Building with:
  EXPO_PUBLIC_BACKEND_URL: https://safeguard-api-rtiu.onrender.com
  EXPO_PUBLIC_SUPABASE_URL: NOT SET
  Environment: development
The following packages should be updated for best compatibility with the installed expo version:
  react-native-svg@15.15.5 - expected version: 15.12.1
Your project may not work correctly until you install the expected versions of the packages.
? Fix dependencies? › (Y/n)
