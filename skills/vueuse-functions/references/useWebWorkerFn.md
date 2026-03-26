---
category: Browser
---

# useWebWorkerFn

使用简单的语法运行昂贵的函数而不阻塞 UI，该语法利用了 Promise。这是 [alewin/useWorker](https://github.com/alewin/useWorker) 的移植版本。

## 用法

### 基本示例

```ts
import { useWebWorkerFn } from '@vueuse/core'

const { workerFn } = useWebWorkerFn(() => {
  // 在 web worker 中要完成的一些繁重工作
})
```

### 带依赖项

```ts {7-9}
import { useWebWorkerFn } from '@vueuse/core'

const { workerFn, workerStatus, workerTerminate } = useWebWorkerFn(
  dates => dates.sort(dateFns.compareAsc),
  {
    timeout: 50000,
    dependencies: [
      'https://cdnjs.cloudflare.com/ajax/libs/date-fns/1.30.1/date_fns.js', // dateFns
    ],
  },
)
```

### 带本地依赖项

```ts {9-9}
import { useWebWorkerFn } from '@vueuse/core'

const pow = (a: number) => a * a

const { workerFn, workerStatus, workerTerminate } = useWebWorkerFn(
  numbers => pow(numbers),
  {
    timeout: 50000,
    localDependencies: [pow]
  },
)
```

## Web Worker

在开始使用此函数之前，我们建议您阅读 [Web Worker](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers) 文档。

## 致谢

此函数是 https://github.com/alewin/useWorker 的 Vue 移植版本，原作者为 Alessio Koci，在 [@Donskelle](https://github.com/Donskelle) 的帮助下进行了迁移。

## 类型声明

```ts
export type WebWorkerStatus =
  | "PENDING"
  | "SUCCESS"
  | "RUNNING"
  | "ERROR"
  | "TIMEOUT_EXPIRED"
export interface UseWebWorkerOptions extends ConfigurableWindow {
  /**
   * 终止 worker 之前的毫秒数
   *
   * @default undefined
   */
  timeout?: number
  /**
   * 包含运行 worker 所需的外部依赖项的数组
   */
  dependencies?: string[]
  /**
   * 包含运行 worker 所需的本地依赖项的数组
   */
  localDependencies?: Function[]
}
export interface UseWebWorkerFnReturn<T extends (...fnArgs: any[]) => any> {
  workerFn: (...fnArgs: Parameters<T>) => Promise<ReturnType<T>>
  workerStatus: ShallowRef<WebWorkerStatus>
  workerTerminate: (status?: WebWorkerStatus) => void
}
/**
 * 使用简单的语法运行昂贵的函数而不阻塞 UI，该语法利用了 Promise。
 *
 * @see https://vueuse.org/useWebWorkerFn
 * @param fn
 * @param options
 */
export declare function useWebWorkerFn<T extends (...fnArgs: any[]) => any>(
  fn: T,
  options?: UseWebWorkerOptions,
): UseWebWorkerFnReturn<T>
```
