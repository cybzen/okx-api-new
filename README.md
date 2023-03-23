# Node.js & Typescript OKX (OKEX) API SDK

[1]: https://www.npmjs.com/package/okx-api


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

## 项目结构
该项目使用TypeScript，资源存储在三个关键结构中。
- [src](./src) - the whole connector written in typescript
- [lib](./lib) - the javascript version of the project (compiled from typescript). This should not be edited directly, as it will be overwritten with each release. This is also the version published to npm.
- [dist](./dist) - the packed bundle of the project for use in browser environments (manual, using webpack).
- [examples](./examples) - some implementation examples & demonstrations. Contributions are welcome!

---

# 使用方法

- [申请API key](https://www.okx.com/account/my-api)













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

## Browser Usage
Build a bundle using webpack:
- `npm install`
- `npm build`
- `npm pack`

The bundle can be found in `dist/`. Altough usage should be largely consistent, smaller differences will exist. Documentation is still TODO.

---
