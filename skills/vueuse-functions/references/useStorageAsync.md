---
category: State
---

# useStorageAsync

响应式 Storage，支持异步。

## 用法

基本用法请参阅 `useStorage`。

## 等待首次加载

当用户进入您的应用程序时，`useStorageAsync()` 将开始从异步存储中加载值，
有时您可能会获得默认的初始值，而不是存储在存储中的真实值
在最开始。

```ts
import { useStorageAsync } from '@vueuse/core'

const accessToken = useStorageAsync('access.token', '', SomeAsyncStorage)

// 在异步存储准备好之前，accessToken.value 可能为空
console.log(accessToken.value) // ""

setTimeout(() => {
  // 一段时间后，异步存储已准备好
  console.log(accessToken.value) // "存储在存储中的真实值"
}, 500)
```

在这种情况下，您可以等待存储准备好，返回的值也是一个 `Promise`，
因此您可以在模板或脚本中等待它解析。

```ts
// 如果您的环境支持，请使用顶层 await
const accessToken = await useStorageAsync('access.token', '', SomeAsyncStorage)

console.log(accessToken.value) // "存储在存储中的真实值"
```

如果您必须等待多个存储，请将它们放入 `Promise.allSettled()`

```ts
router.onReady(async () => {
  await Promise.allSettled([
    accessToken,
    refreshToken,
    userData,
  ])

  app.mount('app')
})
```

选项中有一个名为 `onReady` 的回调：

```ts
import { useStorageAsync } from '@vueuse/core'

// 使用 ES2024 Promise.withResolvers，您可以使用任何 Deferred 对象或 EventBus 来做同样的事情。
const { promise, resolve } = Promise.withResolvers()

const accessToken = useStorageAsync('access.token', '', SomeAsyncStorage, {
  onReady(value) {
    resolve(value)
  }
})

// 在 main.ts 中
router.onReady(async () => {
  // 让我们等待 accessToken 加载
  await promise

  // 现在 accessToken 已加载，我们可以安全地挂载我们的应用程序

  app.mount('app')
})
```

只需使用 `resolve` 作为回调：

```ts
const accessToken = useStorageAsync('access.token', '', SomeAsyncStorage, {
  onReady: resolve
})
```

## Type Declarations

```ts
export interface UseStorageAsyncOptions<T> extends Omit<
  UseStorageOptions<T>,
  "serializer"
> {
  /**
   * Custom data serialization
   */
  serializer?: SerializerAsync<T>
  /**
   * On first value loaded hook.
   */
  onReady?: (value: T) => void
}
export declare function useStorageAsync(
  key: string,
  initialValue: MaybeRefOrGetter<string>,
  storage?: StorageLikeAsync,
  options?: UseStorageAsyncOptions<string>,
): RemovableRef<string> & Promise<RemovableRef<string>>
export declare function useStorageAsync(
  key: string,
  initialValue: MaybeRefOrGetter<boolean>,
  storage?: StorageLikeAsync,
  options?: UseStorageAsyncOptions<boolean>,
): RemovableRef<boolean> & Promise<RemovableRef<boolean>>
export declare function useStorageAsync(
  key: string,
  initialValue: MaybeRefOrGetter<number>,
  storage?: StorageLikeAsync,
  options?: UseStorageAsyncOptions<number>,
): RemovableRef<number> & Promise<RemovableRef<number>>
export declare function useStorageAsync<T>(
  key: string,
  initialValue: MaybeRefOrGetter<T>,
  storage?: StorageLikeAsync,
  options?: UseStorageAsyncOptions<T>,
): RemovableRef<T> & Promise<RemovableRef<T>>
export declare function useStorageAsync<T = unknown>(
  key: string,
  initialValue: MaybeRefOrGetter<null>,
  storage?: StorageLikeAsync,
  options?: UseStorageAsyncOptions<T>,
): RemovableRef<T> & Promise<RemovableRef<T>>
```
