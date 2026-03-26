---
category: Network
---

# useEventSource

[EventSource](https://developer.mozilla.org/en-US/docs/Web/API/EventSource) 或 [Server-Sent-Events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events) 实例打开与 HTTP 服务器的持久连接，该服务器以 text/event-stream 格式发送事件。

## 用法

```ts
import { useEventSource } from '@vueuse/core'

const { status, data, error, close } = useEventSource('https://event-source-url')
```

有关更多选项，请参见 [类型声明](#类型声明)。

### 命名事件

您可以使用第二个参数定义命名事件

```ts
import { useEventSource } from '@vueuse/core'
// ---cut---
const { event, data } = useEventSource(
  'https://event-source-url',
  ['notice', 'update']
)
```

### immediate

默认启用。

在调用组合式函数时立即建立连接。

### autoConnect

默认启用。

如果 url 作为 ref 提供，当 url 更改时，它将自动重新连接到新的 url。

### 错误时自动重新连接

在错误时自动重新连接（默认禁用）。

```ts
import { useEventSource } from '@vueuse/core'
// ---cut---
const { status, data, close } = useEventSource(
  'https://event-source-url',
  [],
  {
    autoReconnect: true,
  }
)
```

或者对其行为进行更多控制：

```ts
import { useEventSource } from '@vueuse/core'
// ---cut---
const { status, data, close } = useEventSource(
  'https://event-source-url',
  [],
  {
    autoReconnect: {
      retries:3,
      delay: 1000,
      onFailed() {
        alert('Failed to connect EventSource after 3 retries')
      },
    },
  }
)
```

### 数据序列化

使用序列化函数对传入的数据应用自定义转换。

```ts
import { useEventSource } from '@vueuse/core'
// ---cut---
const { data } = useEventSource(
  'https://event-source-url',
  [],
  {
    serializer: {
      read: rawData => JSON.parse(rawData),
    },
  }
)

// 如果服务器发送: '{"name":"John","age":30}'
// data.value 将是: { name: 'John', age: 30 }
```

## 类型声明

```ts
export type EventSourceStatus = "CONNECTING" | "OPEN" | "CLOSED"
export interface UseEventSourceOptions<Data> extends EventSourceInit {
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
         * 或者您可以传递一个谓词函数（如果您想重试，则返回 true）。
         *
         * @default -1
         */
        retries?: number | (() => boolean)
        /**
         * 重新连接的延迟，以毫秒为单位
         *
         * @default 1000
         */
        delay?: number
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
   * 自定义数据序列化
   */
  serializer?: {
    read: (v?: string) => Data
  }
}
export interface UseEventSourceReturn<Events extends string[], Data = any> {
  /**
   * 对通过 EventSource 接收的最新数据的引用，
   * 可以被监视以响应传入的消息
   */
  data: ShallowRef<Data | null>
  /**
   * 连接的当前状态，只能是以下之一：
   * 'CONNECTING', 'OPEN' 'CLOSED'
   */
  status: ShallowRef<EventSourceStatus>
  /**
   * 最新的命名事件
   */
  event: ShallowRef<Events[number] | null>
  /**
   * 当前错误
   */
  error: ShallowRef<Event | null>
  /**
   * 优雅地关闭 EventSource 连接。
   */
  close: EventSource["close"]
  /**
   * 重新打开 EventSource 连接。
   * 如果当前有一个处于活动状态，将在打开新连接之前关闭它。
   */
  open: Fn
  /**
   * 对当前 EventSource 实例的引用。
   */
  eventSource: Ref<EventSource | null>
  /**
   * 最后一个事件 ID 字符串，用于服务器发送的事件。
   * @see https://developer.mozilla.org/en-US/docs/Web/API/MessageEvent/lastEventId
   */
  lastEventId: ShallowRef<string | null>
}
/**
 * EventSource 的响应式包装器。
 *
 * @see https://vueuse.org/useEventSource
 * @see https://developer.mozilla.org/en-US/docs/Web/API/EventSource/EventSource EventSource
 * @param url
 * @param events
 * @param options
 */
export declare function useEventSource<Events extends string[], Data = any>(
  url: MaybeRefOrGetter<string | URL | undefined>,
  events?: Events,
  options?: UseEventSourceOptions<Data>,
): UseEventSourceReturn<Events, Data>
```

<!--
Source references:
- https://vueuse.org/core/useEventSource/
-->