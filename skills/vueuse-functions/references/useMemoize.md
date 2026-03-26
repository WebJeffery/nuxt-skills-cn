---
category: Utilities
---

# useMemoize

根据参数缓存函数结果并保持响应性。它也可以用于异步函数，并将重用现有的 promise 以避免同时获取相同的数据。

::: tip
结果不会自动清除。如果不再需要结果，请调用 `clear()` 或使用自己的缓存机制以避免内存泄漏。
:::

## 用法

```ts
import { useMemoize } from '@vueuse/core'

const getUser = useMemoize(
  async (userId: number): Promise<UserData> =>
    axios.get(`users/${userId}`).then(({ data }) => data),
)

const user1 = await getUser(1) // 请求 users/1
const user2 = await getUser(2) // 请求 users/2
// ...
const user1 = await getUser(1) // 从缓存中检索

// ...
const user1 = await getUser.load(1) // 请求 users/1

// ...
getUser.delete(1) // 删除用户 1 的缓存
getUser.clear() // 清除完整缓存
```

与 `computed` 或 `computedAsync` 结合使用以实现响应性：

```ts
import { computedAsync, useMemoize } from '@vueuse/core'

const getUser = useMemoize(
  async (userId: number): Promise<UserData> =>
    axios.get(`users/${userId}`).then(({ data }) => data),
)
// ---cut---
const user1 = computedAsync(() => getUser(1))
// ...
await getUser.load(1) // 也将更新 user1
```

### 解析缓存键

缓存的键由函数的参数决定，默认情况下将使用 `JSON.stringify` 进行序列化。
这将允许相等的对象获得相同的缓存键。如果您想自定义键，可以传递 `getKey`

::: warning 性能考虑
使用 `JSON.stringify` 作为默认键生成器对于大型或复杂对象可能**很慢**。对于复杂参数，强烈建议提供自定义的 `getKey` 函数，该函数基于原始值或唯一标识符生成键。
:::

#### 基本示例

```ts
import { useMemoize } from '@vueuse/core'
// ---cut---
const getUser = useMemoize(
  async (userId: number, headers: AxiosRequestHeaders): Promise<UserData> =>
    axios.get(`users/${userId}`, { headers }).then(({ data }) => data),
  {
    // 仅使用 userId 来获取/设置缓存并忽略 headers
    getKey: (userId, headers) => userId,
  },
)
```

### 自定义缓存机制

默认情况下，结果缓存在 `Map` 中。您可以通过传递具有以下结构的 `cache` 作为选项来实现自己的机制：

```ts
export interface MemoizeCache<Key, Value> {
  /**
   * 获取键的值
   */
  get: (key: Key) => Value | undefined
  /**
   * 设置键的值
   */
  set: (key: Key, value: Value) => void
  /**
   * 如果键存在则返回标志
   */
  has: (key: Key) => boolean
  /**
   * 删除键的值
   */
  delete: (key: Key) => void
  /**
   * 清除缓存
   */
  clear: () => void
}
```

## 类型声明

```ts
type CacheKey = any
/**
 * 自定义 memoize 缓存处理程序
 */
export interface UseMemoizeCache<Key, Value> {
  /**
   * 获取键的值
   */
  get: (key: Key) => Value | undefined
  /**
   * 设置键的值
   */
  set: (key: Key, value: Value) => void
  /**
   * 如果键存在则返回标志
   */
  has: (key: Key) => boolean
  /**
   * 删除键的值
   */
  delete: (key: Key) => void
  /**
   * 清除缓存
   */
  clear: () => void
}
/**
 * Memoized 函数
 */
export interface UseMemoizeReturn<Result, Args extends unknown[]> {
  /**
   * 从缓存获取结果或调用 memoized 函数
   */
  (...args: Args): Result
  /**
   * 调用 memoized 函数并更新缓存
   */
  load: (...args: Args) => Result
  /**
   * 删除给定参数的缓存
   */
  delete: (...args: Args) => void
  /**
   * 清除缓存
   */
  clear: () => void
  /**
   * 为给定参数生成缓存键
   */
  generateKey: (...args: Args) => CacheKey
  /**
   * 缓存容器
   */
  cache: UseMemoizeCache<CacheKey, Result>
}
export interface UseMemoizeOptions<Result, Args extends unknown[]> {
  getKey?: (...args: Args) => string | number
  cache?: UseMemoizeCache<CacheKey, Result>
}
/**
 * 基于参数的响应式函数结果缓存
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useMemoize<Result, Args extends unknown[]>(
  resolver: (...args: Args) => Result,
  options?: UseMemoizeOptions<Result, Args>,
): UseMemoizeReturn<Result, Args>
```
