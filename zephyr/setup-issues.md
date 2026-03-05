* with-zephyr does not support metro's codemod, but the tutorial shows it as supported:
![alt text](image.png)
![alt text](image-1.png)
* https://docs.zephyr-cloud.io/tutorials/metro, react-native cli moved to react-native-community/cli, like npx @react-native-community/cli@latest init MiniApp
![alt text](image-2.png)
![alt text](image-3.png)
* Error when bundle mini application: 
  ○ I don't see zephyrCommandWrapper exported: https://github.com/ZephyrCloudIO/zephyr-packages/blob/main/libs/zephyr-metro-plugin/src/index.ts
`npx react-native bundle-mf-remote --platform ios --dev false`
error Failed to load configuration of your project.

```
TypeError: zephyrCommandWrapper is not a function
    at Object.<anonymous> (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/react-native.config.js:6:28)
    at Module._compile (node:internal/modules/cjs/loader:1554:14)
    at Object..js (node:internal/modules/cjs/loader:1706:10)
    at Module.load (node:internal/modules/cjs/loader:1289:32)
    at Function._load (node:internal/modules/cjs/loader:1108:12)
    at TracingChannel.traceSync (node:diagnostics_channel:322:14)
    at wrapModuleLoad (node:internal/modules/cjs/loader:220:24)
    at Module.require (node:internal/modules/cjs/loader:1311:12)
    at module.exports (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/import-fresh/index.js:33:91)
    at loadJsSync (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/cosmiconfig/dist/loaders.js:18:12)
```

* After patched zephyrCommandWrapper, error when bundle:
```
npx react-native bundle-mf-remote --platform ios --dev false
 ZEPHYR   No zephyr:dependencies found in package.json, to give you more control over your dependencies, check out our documentation:
 ZEPHYR   https://docs.zephyr-cloud.io/features/remote-dependencies
 ZEPHYR   Git repository not found. Zephyr REQUIRES a git repository with remote origin.
 ZEPHYR   Manual configuration is NOT recommended and WILL cause errors in production.
 ZEPHYR
 ZEPHYR   To properly use Zephyr, you MUST:
 ZEPHYR   1. Initialize git: git init
 ZEPHYR   2. Add remote: git remote add origin git@github.com:ORG/REPO.git
 ZEPHYR   3. For CI/production reliability, add at least one commit: git add . && git commit -m "Initial commit"
 ZEPHYR
 ZEPHYR   Alternative: Use our CLI for automatic setup: npx create-zephyr-apps
 ZEPHYR   📝 Documentation: https://docs.zephyr-cloud.io
 ZEPHYR   No zephyr:dependencies found in package.json, to give you more control over your dependencies, check out our documentation:
 ZEPHYR   https://docs.zephyr-cloud.io/features/remote-dependencies
 ZEPHYR   Configuration accepted for THIS BUILD ONLY.
 ZEPHYR   This manual configuration will NOT work for production deployments.
 ZEPHYR   Zephyr REQUIRES a proper git repository with remote origin to function correctly.
 ZEPHYR   Please set up git before your next deployment: https://docs.zephyr-cloud.io
 ZEPHYR   Authentication required - You need to log in to Zephyr Cloud
 ZEPHYR   ZE10018: Failed to authenticate with Zephyr.
 ZEPHYR
 ZEPHYR   Please make sure you have a valid Zephyr account and you are logged in.
 ZEPHYR
 ZEPHYR   Could not get authentication URL
 ZEPHYR
 ZEPHYR   Visit https://docs.zephyr-cloud.io/errors/ZE10018 for more information
 ZEPHYR   Or join our Discord server at https://zephyr-cloud.io/discord
 ZEPHYR
 ZEPHYR   Complete error details available at /var/folders/_s/4qj_8nsn2hv8_g5rxyz_d72c0000gp/T/ze7999540041.log
 ZEPHYR   No zephyr:dependencies found in package.json, to give you more control over your dependencies, check out our documentation:
 ZEPHYR   https://docs.zephyr-cloud.io/features/remote-dependencies
 ZEPHYR   Git repository not found. Zephyr REQUIRES a git repository with remote origin.
 ZEPHYR   Manual configuration is NOT recommended and WILL cause errors in production.
 ZEPHYR
 ZEPHYR   To properly use Zephyr, you MUST:
 ZEPHYR   1. Initialize git: git init
 ZEPHYR   2. Add remote: git remote add origin git@github.com:ORG/REPO.git
 ZEPHYR   3. For CI/production reliability, add at least one commit: git add . && git commit -m "Initial commit"
 ZEPHYR
 ZEPHYR   Alternative: Use our CLI for automatic setup: npx create-zephyr-apps
 ZEPHYR   📝 Documentation: https://docs.zephyr-cloud.io
 ZEPHYR   No zephyr:dependencies found in package.json, to give you more control over your dependencies, check out our documentation:
 ZEPHYR   https://docs.zephyr-cloud.io/features/remote-dependencies
 ZEPHYR   Configuration accepted for THIS BUILD ONLY.
 ZEPHYR   This manual configuration will NOT work for production deployments.
 ZEPHYR   Zephyr REQUIRES a proper git repository with remote origin to function correctly.
 ZEPHYR   Please set up git before your next deployment: https://docs.zephyr-cloud.io
 ZEPHYR   Authentication required - You need to log in to Zephyr Cloud
error Unknown error: {"code":"ZE10018","template":{"message":"Could not get authentication URL"},"cause":{"code":"ZE00000","template":{"message":"Unknown error occurred"},"cause":{"message":"self-signed certificate in certificate chain","name":"Error","stack":"Error: self-signed certificate in certificate chain\n    at AxiosError.from (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/axios/dist/node/axios.cjs:962:24)\n    at RedirectableRequest.handleRequestError (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/axios/dist/node/axios.cjs:3794:29)\n    at RedirectableRequest.emit (node:events:518:28)\n    at eventHandlers.<computed> (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/follow-redirects/index.js:49:24)\n    at ClientRequest.emit (node:events:518:28)\n    at emitErrorEvent (node:_http_client:104:11)\n    at TLSSocket.socketErrorListener (node:_http_client:518:5)\n    at TLSSocket.emit (node:events:518:28)\n    at emitErrorNT (node:internal/streams/destroy:170:8)\n    at emitErrorCloseNT (node:internal/streams/destroy:129:3)\n    at Axios.request (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/axios/dist/node/axios.cjs:5110:41)\n    at process.processTicksAndRejections (node:internal/process/task_queues:105:5)\n    at async fetchWithRetries (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/zephyr-agent/dist/lib/http/fetch-with-retries.js:122:26)\n    at async makeHttpRequest (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/zephyr-agent/dist/lib/http/http-request.js:56:26)\n    at async getAuthenticationURL (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/zephyr-agent/dist/lib/auth/login.js:151:31)\n    at async checkAuth (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/zephyr-agent/dist/lib/auth/login.js:57:25)\n    at async ZephyrEngine.create (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/zephyr-agent/dist/zephyr-engine/index.js:97:9)\n    at async ZephyrMetroPlugin.beforeBuild (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/zephyr-metro-plugin/dist/lib/zephyr-metro-plugin.js:20:30)\n    at async /Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/zephyr-metro-plugin/dist/lib/zephyr-metro-command-wrapper.js:29:13\n    at async Command.handleAction (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/@react-native-community/cli/build/index.js:139:9)","config":{"transitional":{"silentJSONParsing":true,"forcedJSONParsing":true,"clarifyTimeoutError":false,"legacyInterceptorReqResOrdering":true},"adapter":["xhr","http","fetch"],"transformRequest":[null],"transformResponse":[null],"timeout":0,"xsrfCookieName":"XSRF-TOKEN","xsrfHeaderName":"X-XSRF-TOKEN","maxContentLength":-1,"maxBodyLength":-1,"env":{},"headers":{"Accept":"application/json, text/plain, */*","User-Agent":"axios/1.13.6","Accept-Encoding":"gzip, compress, deflate, br"},"method":"get","url":"https://zeapi.zephyrcloud.app/authorize-link?state=rP0bQHHYsKru","allowAbsoluteUrls":true,"axios-retry":{"retries":3,"shouldResetTimeout":false,"validateResponse":null,"retryCount":0,"lastRequestTime":1772724552888}},"code":"SELF_SIGNED_CERT_IN_CHAIN"}}}.
Error: Unknown error: {"code":"ZE10018","template":{"message":"Could not get authentication URL"},"cause":{"code":"ZE00000","template":{"message":"Unknown error occurred"},"cause":{"message":"self-signed certificate in certificate chain","name":"Error","stack":"Error: self-signed certificate in certificate chain\n    at AxiosError.from (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/axios/dist/node/axios.cjs:962:24)\n    at RedirectableRequest.handleRequestError (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/axios/dist/node/axios.cjs:3794:29)\n    at RedirectableRequest.emit (node:events:518:28)\n    at eventHandlers.<computed> (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/follow-redirects/index.js:49:24)\n    at ClientRequest.emit (node:events:518:28)\n    at emitErrorEvent (node:_http_client:104:11)\n    at TLSSocket.socketErrorListener (node:_http_client:518:5)\n    at TLSSocket.emit (node:events:518:28)\n    at emitErrorNT (node:internal/streams/destroy:170:8)\n    at emitErrorCloseNT (node:internal/streams/destroy:129:3)\n    at Axios.request (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/axios/dist/node/axios.cjs:5110:41)\n    at process.processTicksAndRejections (node:internal/process/task_queues:105:5)\n    at async fetchWithRetries (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/zephyr-agent/dist/lib/http/fetch-with-retries.js:122:26)\n    at async makeHttpRequest (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/zephyr-agent/dist/lib/http/http-request.js:56:26)\n    at async getAuthenticationURL (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/zephyr-agent/dist/lib/auth/login.js:151:31)\n    at async checkAuth (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/zephyr-agent/dist/lib/auth/login.js:57:25)\n    at async ZephyrEngine.create (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/zephyr-agent/dist/zephyr-engine/index.js:97:9)\n    at async ZephyrMetroPlugin.beforeBuild (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/zephyr-metro-plugin/dist/lib/zephyr-metro-plugin.js:20:30)\n    at async /Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/zephyr-metro-plugin/dist/lib/zephyr-metro-command-wrapper.js:29:13\n    at async Command.handleAction (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/@react-native-community/cli/build/index.js:139:9)","config":{"transitional":{"silentJSONParsing":true,"forcedJSONParsing":true,"clarifyTimeoutError":false,"legacyInterceptorReqResOrdering":true},"adapter":["xhr","http","fetch"],"transformRequest":[null],"transformResponse":[null],"timeout":0,"xsrfCookieName":"XSRF-TOKEN","xsrfHeaderName":"X-XSRF-TOKEN","maxContentLength":-1,"maxBodyLength":-1,"env":{},"headers":{"Accept":"application/json, text/plain, */*","User-Agent":"axios/1.13.6","Accept-Encoding":"gzip, compress, deflate, br"},"method":"get","url":"https://zeapi.zephyrcloud.app/authorize-link?state=rP0bQHHYsKru","allowAbsoluteUrls":true,"axios-retry":{"retries":3,"shouldResetTimeout":false,"validateResponse":null,"retryCount":0,"lastRequestTime":1772724552888}},"code":"SELF_SIGNED_CERT_IN_CHAIN"}}}
    at /Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/zephyr-metro-plugin/dist/lib/zephyr-metro-command-wrapper.js:36:19
    at process.processTicksAndRejections (node:internal/process/task_queues:105:5)
    at async Command.handleAction (/Users/zhongwu/Documents/workspace/zephyr/MiniApp/node_modules/@react-native-community/cli/build/index.js:139:9)
```