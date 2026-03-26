---
category: Network
---

# useWebSocket

响应式 [WebSocket](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket/WebSocket) 客户端。

## 用法

```ts
import { useWebSocket } from '@vueuse/core'

const { status, data, send, open, close, ws } = useWebSocket('ws://websocketurl')
```

### 返回值

| 属性 | 类型                                      | 描述                          |
| -------- | ----------------------------------------- | ------------------------------------ |
| `data`   | `Ref<any>`                                | 最新接收的数据                 |
| `status` | `Ref<'OPEN' \| 'CONNECTING' \| 'CLOSED'>` | 连接状态                    |
| `ws`     | `Ref<WebSocket>`                          | WebSocket 实例                   |
| `send`   | `(data, useBuffer?) => boolean`           | 发送数据（如果未连接则缓冲） |
| `open`   | `() => void`                              | 打开/重新连接连接        |
| `close`  | `(code?, reason?) => void`                | 关闭连接                 |

### 回调

```ts
const { data } = useWebSocket('ws://websocketurl', {
  onConnected(ws) {
    console.log('已连接!')
  },
  onDisconnected(ws, event) {
    console.log('已断开!', event.code)
  },
  onError(ws, event) {
    console.error('错误:', event)
  },
  onMessage(ws, event) {
    console.log('消息:', event.data)
  },
})
```

更多选项请参阅[类型声明](#type-declarations)。

### immediate

默认启用。

调用组合式函数时立即建立连接。

### autoConnect

默认启用。

如果 URL 作为 ref 提供，当 URL 更改时，它将自动重新连接到新 URL。

### autoClose

默认启用。

当触发 `beforeunload` 事件或关联的效果作用域停止时，这将自动调用 `close()`。

### autoReconnect

出错时自动重新连接（默认禁用）。

```ts
import { useWebSocket } from '@vueuse/core'
// ---cut---
const { status, data, close } = useWebSocket('ws://websocketurl', {
  autoReconnect: true,
})
```

或者对其行为进行更多控制：

```ts
import { useWebSocket } from '@vueuse/core'
// ---cut---
const { status, data, close } = useWebSocket('ws://websocketurl', {
  autoReconnect: {
    retries: 3,
    delay: 1000,
    onFailed() {
      alert('重试 3 次后连接 WebSocket 失败')
    },
  },
})
```

您还可以向 `delay` 传递一个函数，根据重试次数计算延迟。这对于实现指数退避很有用：

```ts
import { useWebSocket } from '@vueuse/core'
// ---cut---
const { status, data, close } = useWebSocket('ws://websocketurl', {
  autoReconnect: {
    retries: 5,
    // 指数退避：1s, 2s, 4s, 8s, 16s
    delay: retries => Math.min(1000 * 2 ** (retries - 1), 30000),
  },
})
```

```ts
import { useWebSocket } from '@vueuse/core'
// ---cut---
const { status, data, close } = useWebSocket('ws://websocketurl', {
  autoReconnect: {
    retries: 5,
    // 线性退避：1s, 2s, 3s, 4s, 5s
    delay: retries => retries * 1000,
  },
})
```

显式调用 `close()` 不会触发自动重新连接。

### heartbeat

每隔给定的时间发送一个小消息（心跳）以保持连接活跃是常见的做法。在此函数中，我们提供了一个方便的辅助工具来执行此操作：

```ts
import { useWebSocket } from '@vueuse/core'
// ---cut---
const { status, data, close } = useWebSocket('ws://websocketurl', {
  heartbeat: true,
})
```

或者进行更多控制：

```ts
import { useWebSocket } from '@vueuse/core'
// ---cut---
const { status, data, close } = useWebSocket('ws://websocketurl', {
  heartbeat: {
    message: 'ping',
    scheduler: cb => useIntervalFn(cb, 2000),
    pongTimeout: 1000,
  },
})
```

### Sub-protocols

要使用的一个或多个子协议列表，在本例中为 SOAP 和 WAMP。

```ts
import { useWebSocket } from '@vueuse/core'
// ---cut---
const { status, data, send, open, close } = useWebSocket('ws://websocketurl', {
  protocols: ['soap'], // ['soap', 'wamp']
})
```

## 类型声明

```ts
export type WebSocketStatus = "OPEN" | "CONNECTING" | "CLOSED"
export type WebSocketHeartbeatMessage = string | ArrayBuffer | Blob
export interface UseWebSocketOptions {
  onConnected?: (ws: WebSocket) => void
  onDisconnected?: (ws: WebSocket, event: CloseEvent) => void
  onError?: (ws: WebSocket, event: Event) => void
  onMessage?: (ws: WebSocket, event: MessageEvent) => void
  /**
   * 每隔 x 毫秒发送心跳
   *
   * @default false
   */
  heartbeat?:
    | boolean
    | (ConfigurableScheduler & {
        /**
         * 心跳消息
         *
         * @default 'ping'
         */
        message?: MaybeRefOrGetter<WebSocketHeartbeatMessage>
        /**
         * 心跳响应消息，如果未定义则使用消息
         */
        responseMessage?: MaybeRefOrGetter<WebSocketHeartbeatMessage>
        /**
         * 间隔，以毫秒为单位
         *
         * @deprecated 请改用 `scheduler` 选项
         * @default 1000
         */
        interval?: number
        /**
         * 心跳响应超时，以毫秒为单位
         *
         * @default 1000
         */
        pongTimeout?: number
      })
  /**
   * 启用自动重新连接
   *
   * @default false
   */
  autoReconnect?:
    | boolean
    | {
        /**
         * 最大重试次数。
         *
         * 或者您可以传递一个谓词函数（如果您想重试则返回 true）。
         *
         * @default -1
         */
        retries?: number | ((retried: number) => boolean)
        /**
         * 重新连接的延迟，以毫秒为单位
         *
         * 或者您可以传递一个函数来根据重试次数计算延迟。
         *
         * @default 1000
         */
        delay?: number | ((retries: number) => number)
        /**
         * 达到最大重试次数时。
         */
        onFailed?: Fn
      }
  /**
   * 调用此组合式函数时立即打开连接
   *
   * @default true
   */
  immediate?: boolean
  /**
   * 当 URL 更改时自动连接到 websocket
   *
   * @default true
   */
  autoConnect?: boolean
  /**
   * 自动关闭连接
   *
   * @default true
   */
  autoClose?: boolean
  /**
   * 一个或多个子协议字符串的列表
   *
   * @default []
   */
  protocols?: string[]
}
export interface UseWebSocketReturn<T> {
  /**
   * 通过 websocket 接收的最新数据的引用，
   * 可以监视它以响应传入的消息
   */
  data: Ref<T | null>
  /**
   * 当前的 websocket 状态，只能是以下之一：
   * 'OPEN', 'CONNECTING', 'CLOSED'
   */
  status: ShallowRef<WebSocketStatus>
  /**
   * 优雅地关闭 websocket 连接。
   */
  close: WebSocket["close"]
  /**
   * 重新打开 websocket 连接。
   * 如果当前连接处于活动状态，则在打开新连接之前将其关闭。
   */
  open: Fn
  /**
   * 通过 websocket 连接发送数据。
   *
   * @param data
   * @param useBuffer 当 socket 尚未打开时，将数据存储到缓冲区并在连接时发送。默认为 true。
   */
  send: (data: string | ArrayBuffer | Blob, useBuffer?: boolean) => boolean
  /**
   * WebSocket 实例的引用。
   */
  ws: Ref<WebSocket | undefined>
}
/**
 * 响应式 WebSocket 客户端。
 *
 * @see https://vueuse.org/useWebSocket
 * @param url
 */
export declare function useWebSocket<Data = any>(
  url: MaybeRefOrGetter<string | URL | undefined>,
  options?: UseWebSocketOptions,
): UseWebSocketReturn<Data>
```
