mike@Mikes-MacBook-Pro frontend % git status --short
fatal: not a git repository (or any of the parent directories): .git
mike@Mikes-MacBook-Pro frontend % grep -n '"react-native-svg"' package.json
grep -A8 -B2 'react-native-svg@' yarn.lock

73:    "react-native-svg": "15.12.1",
    warn-once "^0.1.0"

react-native-svg@15.12.1:
  version "15.12.1"
  resolved "https://registry.yarnpkg.com/react-native-svg/-/react-native-svg-15.12.1.tgz#7ba756dd6a235f86a2c312a1e7911f9b0d18ad3a"
  integrity sha512-vCuZJDf8a5aNC2dlMovEv4Z0jjEUET53lm/iILFnFewa15b4atjVxU6Wirm6O9y6dEsdjDZVD7Q3QM4T1wlI8g==
  dependencies:
    css-select "^5.1.0"
    css-tree "^1.1.3"
    warn-once "0.1.1"

