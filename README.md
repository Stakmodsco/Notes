 LOG  [SecureStorage 13:42:49] SecureStore getItem {"key":"sb-trqfppifgddzcpflamyr-auth-token","hasValue":false}
 ERROR  [TypeError: Cannot create property 'user' on string '__notme_chunked_v2__:{"count":2,"generation":"mw99mfso_1_r4pgaqq4"}'] 

Call Stack
  _recoverAndRefresh (node_modules/@supabase/auth-js/dist/main/GoTrueClient.js:1910:44)
  next (<native>)
  asyncGeneratorStep (node_modules/@babel/runtime/helpers/asyncToGenerator.js:3:17)
  _next (node_modules/@babel/runtime/helpers/asyncToGenerator.js:17:27)
  tryCallOne (address at (InternalBytecode.js:1:1180)
  anonymous (address at (InternalBytecode.js:1:1874)
 LOG  [SecureStorage 13:42:49] SecureStore getItem {"key":"sb-trqfppifgddzcpflamyr-auth-token","hasValue":false}




LOG  [SecureStorage 13:58:36] SecureStore getItem (chunked) {"key":"sb-trqfppifgddzcpflamyr-auth-token","hasValue":true,"chunks":2}
 ERROR  [Error: Unsupported top level event type "topSvgLayout" dispatched] 

Code: ReactFabric-dev.js
  14513 |             directDispatchConfig = customDirectEventTypes[topLevelType];
  14514 |           if (!bubbleDispatchConfig && !directDispatchConfig)
> 14515 |             throw Error(
        |                        ^
  14516 |               'Unsupported top level event type "' +
  14517 |                 topLevelType +
  14518 |                 '" dispatched'
Call Stack
  ReactNativeBridgeEventPlugin.extractEvents (node_modules/react-native/Libraries/Renderer/implementations/ReactFabric-dev.js:14515:24)
  batchedUpdates$1$argument_0 (node_modules/react-native/Libraries/Renderer/implementations/ReactFabric-dev.js:1523:59)
  batchedUpdatesImpl (node_modules/react-native/Libraries/Renderer/implementations/ReactFabric-dev.js:16606:18)
  batchedUpdates$1 (node_modules/react-native/Libraries/Renderer/implementations/ReactFabric-dev.js:1461:34)
  dispatchEvent (node_modules/react-native/Libraries/Renderer/implementations/ReactFabric-dev.js:1511:23)
 LOG  [SecureStorage 13:58:36] SecureStore getItem (chunked) {"key":"sb-trqfppifgddzcpflamyr-auth-token","hasValue":true,"chunks":2}
