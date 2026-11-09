G  [SecurityStore] supportedAuthenticationTypesAsync result {"hasFacial": true, "hasFingerprint": false, "types": [2]}
 LOG  [SecureStorage 15:07:17] SecureStore getItem {"key":"sb-trqfppifgddzcpflamyr-auth-token-user","hasValue":false}
 LOG  [SecurityStore] getEnrolledLevelAsync result {"securityLevel": 1}
 LOG  [SecurityStore] Biometric availability final result {"biometricType": "facial", "compatible": true, "enrolled": false, "isBiometricsAvailable": false, "isWeb": false, "typesCount": 1}
 LOG  [SecureStorage 15:07:17] SecureStore getItem {"key":"sb-trqfppifgddzcpflamyr-auth-token","hasValue":true}
 ERROR  [TypeError: Cannot create property 'user' on string '__notme_chunked_v2__:{"count":2,"generation":"mw9dw2ly_4_kao8p82h"}'] 

Call Stack
  _recoverAndRefresh (node_modules/@supabase/auth-js/dist/main/GoTrueClient.js:1910:44)
  next (<native>)
  asyncGeneratorStep (node_modules/@babel/runtime/helpers/asyncToGenerator.js:3:17)
  _next (node_modules/@babel/runtime/helpers/asyncToGenerator.js:17:27)
  tryCallOne (address at (InternalBytecode.js:1:1180)
  anonymous (address at (InternalBytecode.js:1:1874)
 LOG  [SecureStorage 15:07:17] SecureStore getItem {"key":"sb-trqfppifgddzcpflamyr-auth-token","hasValue":true}
