# Node.js & Typescript OKX (OKEX) API & WebSocket SDK

> 原仓库：https://github.com/tiagosiebler/okx-api

[![okx okex SDK for nodejs rest & websockets api](https://github.com/tiagosiebler/okx-api/blob/master/docs/images/logo2.png?raw=true)][1]

Node.js连接器，用于OKX APIs和WebSockets：
1. 与所有OKX APIs的完全集成。
2. 支持TypeScript（具有大多数API请求和响应的类型声明）。
3. 超过100个端到端测试进行真实的API调用和WebSocket连接，在它们到达npm之前验证任何更改。
4. 强大的WebSocket集成
- 可配置连接心跳（自动检测失败的连接）。
- 自动重新连接，然后重新订阅工作流程。
- 自动身份验证和心跳处理。
5. 浏览器支持（通过webpack捆绑包，请参见下面的“浏览器使用”）。


## 下载
```bash
npm install okx-api-new
```

## 文档说明
大多数方法接受JS对象。这些可以使用okx的API文档中指定的参数进行填充，或者检查rest-client类方法中的类型定义。
- [RestClient](src/rest-client.ts).
- [OKX API Documentation](https://www.okx.com/docs-v5/en/#rest-api).
- [TSDoc Documentation (generated using typedoc via npm module)](https://tsdocs.dev/docs/okx-api)

## Contributions & Thanks

Support my efforts to make algo trading accessible to all - register with my referral links:

- [Bybit](https://www.bybit.com/en-US/register?affiliate_id=9410&language=en-US&group_id=0&group_type=1)
- [Binance](https://www.binance.com/en/register?ref=20983262)
- [OKX](https://www.okx.com/join/18504944)

For more ways to give thanks & support my efforts, visit [Contributions & Thanks](https://github.com/tiagosiebler/awesome-crypto-examples/wiki/Contributions-&-Thanks)!

## 项目结构
该项目使用TypeScript，资源存储在三个关键结构中。
- [src](./src) - the whole connector written in typescript
- [lib](./lib) - the javascript version of the project (compiled from typescript). This should not be edited directly, as it will be overwritten with each release. This is also the version published to npm.
- [dist](./dist) - the packed bundle of the project for use in browser environments (manual, using webpack).
- [examples](./examples) - some implementation examples & demonstrations. Contributions are welcome!

---

# 使用方法

- [申请API key](https://www.okx.com/account/my-api)


**基本使用实例**
1. 根据需求在[OKX API Documentation](https://www.okx.com/docs-v5/en/#rest-api)找到对应的接口 -> A
2. 在代码中引入之后，点击'okx-api-new' 进入包，找到rest-client.js 文件，搜索 A ,复制函数名，
3. 在代码中使用


```js

// 实例一
import { RestClient, OrderRequest } from 'okx-api-new';
const API_KEY = "生成的key"
const API_SECRET = "生成的密钥"
const API_PASS = "密码"

const client = new RestClient({
  apiKey: API_KEY,
  apiSecret: API_SECRET,
  apiPass: API_PASS,
});

async function main() {
  const args = {
    instId: "TRX-USDT-SWAP",
  }
  // 获取资金费率
  const info = await client.getFundingRateHistory(args)
  console.log(JSON.stringify(info));
}

main()

```


```js
// 实例二
import { RestClient, OrderRequest, WebsocketClient } from 'okx-api-new';
const API_KEY = "生成的key"
const API_SECRET = "生成的密钥"
const API_PASS = "密码"

const client = new RestClient({
  apiKey: API_KEY,
  apiSecret: API_SECRET,
  apiPass: API_PASS,
});
/*
 * 如果需要测试的相关配置，看WebsocketClient可选项
 * 1. 收到打开ws, 会先执行new WebsocketClient() 打印一段信息
 * 2. 第二步 wsClient.on('open',
 * 3. 第三步 wsClient.on('response',
 * 4. 第四步 wsClient.on('update'
 * 
 *
 */

// 私有频道
// const wsClient = new WebsocketClient(
//   {
//     accounts: [
//       {
//         apiKey: API_KEY,
//         apiSecret: API_SECRET,
//         apiPass: API_PASS,
//       },
//     ],
//   },
// );

// 共有频道
const wsClient = new WebsocketClient({});


wsClient.on('update', (data) => {
  console.log(new Date(), " 接受到的消息: ", JSON.stringify(data));
});
wsClient.on('open', (data) => {
  console.log('ws connection opened open:', data.wsKey);
});

wsClient.on('response', (data) => {
  console.log('ws response received: ', JSON.stringify(data, null, 2));
});
// 尝试自动重连接
wsClient.on('reconnect', ({ wsKey }) => {
  console.log('ws automatically reconnecting.... ', wsKey);
});
// 自动重连成功
wsClient.on('reconnected', (data) => {
  console.log('ws has reconnected ', data?.wsKey);
});
// 报错
wsClient.on('error', (data) => {
  console.error('ws exception: ', data);
});


// ====>>> 订阅 资金费率
wsClient.subscribe({
  channel: "funding-rate",
  instId: "BTC-USDT-SWAP"
});
```










## npm 发布流程





## REST Client

### Requests & Responses
- [官方 API docs](https://www.okx.com/cn/okx-api).
- 统一返回数据格式
```js
HTTP 200 and "code" in the response body === "0"
```
如果返回非正常，请看[APIResponse<T>](./src/types/rest/shared.ts).






## Websocket Client

此连接器包括用于 OKX 公共和私有 websockets 的高性能 node.js 和 typescript websocket 客户端。

- If your IDE doesn't have IntelliSense, check the [websocket-client.ts](./src/websocket-client.ts) for a list of methods, params & return types.
- When subscribing to channels, only the "args" should be passed as an object or array when calling the websocket client subcribe() function: [API docs](https://www.okx.com/docs-v5/en/#websocket-api-subscribe).
- TypeScript recommended (but it is not required) for a richer experience:
  ![typescript-subscribe](./docs/images/subscribe-with-typescript.gif)
- The ws client will automatically open connections as needed when subscribing to a channel.
- If the connection is lost for any reason, the ws client will detect this (via the connection heartbeats). It will then:
  - Automatically teardown the dead connection.
  - Automatically respawn a fresh connection.
  - Automatically reauthenticate, if using private channels.
  - Automatically resubscribe to previously subscribed topics.
  - Resume producing events as before, without extra handling needed in your logic.
- The ws client will automatically authenticate if accounts are provided and a private channel is subscribed to.
- Up to 100 accounts are supported on the private connection, as per the [API docs](https://www.okx.com/docs-v5/en/#websocket-api-login). Authentication is automatic if accounts are provided.
- For examples in using the websocket client, check the examples in the repo:
  - Private channels (account data): [examples/ws-private.ts](./examples/ws-private.ts)
  - Public chanels (market data): [examples/ws-public.ts](./examples/ws-public.ts)
  - These examples are written in TypeScript, so can be executed with ts-node for easy testing:
    `ts-node examples/ws-private.ts`
  - Or convert them to javascript:
    - Change the `import { ... } from 'okx-api'` to `const { ... } = require('okx-api');`
    - Rename the file to `ws-private.js`
    - And execute with node: `node examples/ws-private.js`

### Public Events

See [examples/ws-public.ts](./examples/ws-public.ts) for a full example:

![typescript-events-public](./docs/images/subscribe-events-public.gif)

### Private Events

See [examples/ws-private.ts](./examples/ws-private.ts) for a full example:

![typescript-events](./docs/images/subscribe-events.gif)

## Browser/Frontend Usage

### Import

This is the "modern" way, allowing the package to be directly imported into frontend projects with full typescript support.

1. Install these dependencies
   ```sh
   npm install crypto-browserify stream-browserify
   ```
2. Add this to your `tsconfig.json`
   ```json
   {
     "compilerOptions": {
       "paths": {
         "crypto": [
           "./node_modules/crypto-browserify"
         ],
         "stream": [
           "./node_modules/stream-browserify"
         ]
   }
   ```
3. Declare this in the global context of your application (ex: in polyfills for angular)
   ```js
   (window as any).global = window;
   ```

### Webpack

This is the "old" way of using this package on webpages. This will build a minified js bundle that can be pulled in using a script tag on a website.

Build a bundle using webpack:

- `npm install`
- `npm build`
- `npm pack`

The bundle can be found in `dist/`. Altough usage should be largely consistent, smaller differences will exist. Documentation is still TODO.

---

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=tiagosiebler/bitget-api,tiagosiebler/bybit-api,tiagosiebler/binance,tiagosiebler/orderbooks,tiagosiebler/okx-api,tiagosiebler/awesome-crypto-examples&type=Date)](https://star-history.com/#tiagosiebler/bybit-api&tiagosiebler/binance&tiagosiebler/orderbooks&tiagosiebler/okx-api&tiagosiebler/bitget-api&tiagosiebler/awesome-crypto-examples&tiagosiebler/ftx-api&Date)
