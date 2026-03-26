---
category: Network
---

# useFetch

响应式 [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)，提供中止请求、在请求发送前拦截请求、当 url 更改时自动重新获取请求以及使用预定义选项创建您自己的 `useFetch` 的能力。

<CourseLink href="https://vueschool.io/lessons/vueuse-utilities-usefetch-and-reactify?friend=vueuse">通过 Vue School 的这个免费视频课程学习 useFetch！</CourseLink>

::: tip
当与 Nuxt 3 一起使用时，此函数将**不会**自动导入，以支持 Nuxt 内置的 [`useFetch()`](https://v3.nuxtjs.org/api/composables/use-fetch)。如果您想使用 VueUse 中的函数，请使用显式导入。
:::

## 用法

### 基本用法

只需提供 url 即可使用 `useFetch` 函数。url 可以是字符串或 `ref`。`data` 对象将包含请求结果，`error` 对象将包含任何错误，`isFetching` 对象将指示请求是否正在加载。

```ts
import { useFetch } from '@vueuse/core'

const { isFetching, error, data } = useFetch(url)
```

### 异步用法

`useFetch` 也可以像普通的 fetch 一样被等待。请注意，每当组件是异步的，使用它的任何组件都必须将组件包装在 `<Suspense>` 标签中。您可以在 [Vue 3 官方文档](https://vuejs.org/guide/built-ins/suspense.html) 中阅读更多关于 suspense api 的信息。

```ts
import { useFetch } from '@vueuse/core'
// ---cut---
const { isFetching, error, data } = await useFetch(url)
```

### URL 更改时重新获取

为 url 参数使用 `ref` 将允许 `useFetch` 函数在 url 更改时自动触发另一个请求。

```ts
import { useFetch } from '@vueuse/core'
// ---cut---
const url = ref('https://my-api.com/user/1')

const { data } = useFetch(url, { refetch: true })

url.value = 'https://my-api.com/user/2' // 将触发另一个请求
```

### 防止请求立即触发

将 `immediate` 选项设置为 false 将防止请求触发，直到调用 `execute` 函数。

```ts
import { useFetch } from '@vueuse/core'
// ---cut---
const { execute } = useFetch(url, { immediate: false })

execute()
```

### 中止请求

可以使用 `useFetch` 函数中的 `abort` 函数中止请求。`canAbort` 属性指示请求是否可以中止。

```ts
import { useFetch } from '@vueuse/core'
// ---cut---
const { abort, canAbort } = useFetch(url)

setTimeout(() => {
  if (canAbort.value)
    abort()
}, 100)
```

还可以通过使用 `timeout` 属性自动中止请求。当达到给定的超时时间时，它将调用 `abort` 函数。

```ts
import { useFetch } from '@vueuse/core'
// ---cut---
const { data } = useFetch(url, { timeout: 100 })
```

### 拦截请求

`beforeFetch` 选项可以在请求发送之前拦截请求并修改请求选项和 url。

```ts
import { useFetch } from '@vueuse/core'
// ---cut---
const { data } = useFetch(url, {
  async beforeFetch({ url, options, cancel }) {
    const myToken = await getMyToken()

    if (!myToken)
      cancel()

    options.headers = {
      ...options.headers,
      Authorization: `Bearer ${myToken}`,
    }

    return {
      options,
    }
  },
})
```

`afterFetch` 选项可以在响应数据更新之前拦截响应数据。

```ts
import { useFetch } from '@vueuse/core'
// ---cut---
const { data } = useFetch(url, {
  afterFetch(ctx) {
    if (ctx.data.title === 'HxH')
      ctx.data.title = 'Hunter x Hunter' // 修改响应数据

    return ctx
  },
})
```

当 `updateDataOnError` 设置为 `true` 时，`onFetchError` 选项可以在响应数据和错误更新之前拦截它们。

```ts
import { useFetch } from '@vueuse/core'
// ---cut---
const { data } = useFetch(url, {
  updateDataOnError: true,
  onFetchError(ctx) {
    // 当 5xx 响应时 ctx.data 可能为 null
    if (ctx.data === null)
      ctx.data = { title: 'Hunter x Hunter' } // 修改响应数据

    ctx.error = new Error('Custom Error') // 修改错误
    return ctx
  },
})

console.log(data.value) // { title: 'Hunter x Hunter' }
```

### 设置请求方法和返回类型

可以通过在 `useFetch` 末尾添加适当的方法来设置请求方法和返回类型。

```ts
import { useFetch } from '@vueuse/core'
// ---cut---
// 请求将使用 GET 方法发送，数据将被解析为 JSON
const { data } = useFetch(url).get().json()

// 请求将使用 POST 方法发送，数据将被解析为文本
const { data } = useFetch(url).post().text()

// 或者使用选项设置方法

// 请求将使用 GET 方法发送，数据将被解析为 blob
const { data } = useFetch(url, { method: 'GET' }, { refetch: true }).blob()
```

### 创建自定义实例

`createFetch` 函数将返回一个 useFetch 函数，其中包含提供给它的任何预配置选项。这对于在整个应用程序中与使用相同基本 URL 或需要 Authorization 标头的 API 交互非常有用。

```ts
import { createFetch } from '@vueuse/core'
// ---cut---
const useMyFetch = createFetch({
  baseUrl: 'https://my-api.com',
  options: {
    async beforeFetch({ options }) {
      const myToken = await getMyToken()
      options.headers.Authorization = `Bearer ${myToken}`

      return { options }
    },
  },
  fetchOptions: {
    mode: 'cors',
  },
})

const { isFetching, error, data } = useMyFetch('users')
```

如果您想在预配置实例和新生成的实例之间控制 `beforeFetch`、`afterFetch`、`onFetchError` 的行为。您可以提供 `combination` 选项以在 `overwrite` 或 `chaining` 之间切换。

```ts
import { createFetch } from '@vueuse/core'
// ---cut---
const useMyFetch = createFetch({
  baseUrl: 'https://my-api.com',
  combination: 'overwrite',
  options: {
    // 预配置实例中的 beforeFetch 仅在新生成的实例不传递 beforeFetch 时运行
    async beforeFetch({ options }) {
      const myToken = await getMyToken()
      options.headers.Authorization = `Bearer ${myToken}`

      return { options }
    },
  },
})

// 使用 useMyFetch beforeFetch
const { isFetching, error, data } = useMyFetch('users')

// 使用自定义 beforeFetch
const { isFetching, error, data } = useMyFetch('users', {
  async beforeFetch({ url, options, cancel }) {
    const myToken = await getMyToken()

    if (!myToken)
      cancel()

    options.headers = {
      ...options.headers,
      Authorization: `Bearer ${myToken}`,
    }

    return {
      options,
    }
  },
})
```

您可以通过在 `afterFetch` 或 `onFetchError` 中调用 `execute` 方法来重新执行请求。这是一个刷新令牌的简单示例：

```ts
import { createFetch } from '@vueuse/core'
// ---cut---
let isRefreshing = false
const refreshSubscribers: Array<() => void> = []

const useMyFetch = createFetch({
  baseUrl: 'https://my-api.com',
  options: {
    async beforeFetch({ options }) {
      const myToken = await getMyToken()
      options.headers.Authorization = `Bearer ${myToken}`

      return { options }
    },
    afterFetch({ data, response, context, execute }) {
      if (needRefreshToken) {
        if (!isRefreshing) {
          isRefreshing = true
          refreshToken().then((newToken) => {
            if (newToken.value) {
              isRefreshing = false
              setMyToken(newToken.value)
              onRefreshed()
            }
            else {
              refreshSubscribers.length = 0
              // 处理刷新令牌错误
            }
          })
        }

        return new Promise((resolve) => {
          addRefreshSubscriber(() => {
            execute().then((response) => {
              resolve({ data, response })
            })
          })
        })
      }

      return { data, response }
    },
    // 或使用带有 updateDataOnError 的 onFetchError
    updateDataOnError: true,
    onFetchError({ error, data, response, context, execute }) {
      // 与 afterFetch 相同
      return { error, data }
    },
  },
  fetchOptions: {
    mode: 'cors',
  },
})

async function refreshToken() {
  const { data, execute } = useFetch<string>('refresh-token', {
    immediate: false,
  })

  await execute()
  return data
}

function onRefreshed() {
  refreshSubscribers.forEach(callback => callback())
  refreshSubscribers.length = 0
}

function addRefreshSubscriber(callback: () => void) {
  refreshSubscribers.push(callback)
}

const { isFetching, error, data } = useMyFetch('users')
```

### 事件

`onFetchResponse` 和 `onFetchError` 将分别在 fetch 请求响应和错误时触发。

```ts
import { useFetch } from '@vueuse/core'
// ---cut---
const { onFetchResponse, onFetchError } = useFetch(url)

onFetchResponse((response) => {
  console.log(response.status)
})

onFetchError((error) => {
  console.error(error.message)
})
```

## 类型声明

```ts
export interface UseFetchReturn<T> {
  /**
   * 指示 fetch 请求是否已完成
   */
  isFinished: Readonly<ShallowRef<boolean>>
  /**
   * HTTP fetch 响应的状态码
   */
  statusCode: ShallowRef<number | null>
  /**
   * fetch 响应的原始响应
   */
  response: ShallowRef<Response | null>
  /**
   * 可能发生的任何 fetch 错误
   */
  error: ShallowRef<any>
  /**
   * 成功时的 fetch 响应体，可能是 JSON 或文本
   */
  data: ShallowRef<T | null>
  /**
   * 指示请求当前是否正在获取
   */
  isFetching: Readonly<ShallowRef<boolean>>
  /**
   * 指示 fetch 请求是否可以中止
   */
  canAbort: ComputedRef<boolean>
  /**
   * 指示 fetch 请求是否已中止
   */
  aborted: ShallowRef<boolean>
  /**
   * 中止 fetch 请求
   */
  abort: (reason?: any) => void
  /**
   * 手动调用 fetch
   * （默认不抛出错误）
   */
  execute: (throwOnFailed?: boolean) => Promise<any>
  /**
   * 在 fetch 请求完成后触发
   */
  onFetchResponse: EventHookOn<Response>
  /**
   * 在 fetch 请求错误后触发
   */
  onFetchError: EventHookOn
  /**
   * 在 fetch 完成后触发
   */
  onFetchFinally: EventHookOn
  get: () => UseFetchReturn<T> & PromiseLike<UseFetchReturn<T>>
  post: (
    payload?: MaybeRefOrGetter<unknown>,
    type?: string,
  ) => UseFetchReturn<T> & PromiseLike<UseFetchReturn<T>>
  put: (
    payload?: MaybeRefOrGetter<unknown>,
    type?: string,
  ) => UseFetchReturn<T> & PromiseLike<UseFetchReturn<T>>
  delete: (
    payload?: MaybeRefOrGetter<unknown>,
    type?: string,
  ) => UseFetchReturn<T> & PromiseLike<UseFetchReturn<T>>
  patch: (
    payload?: MaybeRefOrGetter<unknown>,
    type?: string,
  ) => UseFetchReturn<T> & PromiseLike<UseFetchReturn<T>>
  head: (
    payload?: MaybeRefOrGetter<unknown>,
    type?: string,
  ) => UseFetchReturn<T> & PromiseLike<UseFetchReturn<T>>
  options: (
    payload?: MaybeRefOrGetter<unknown>,
    type?: string,
  ) => UseFetchReturn<T> & PromiseLike<UseFetchReturn<T>>
  json: <JSON = any>() => UseFetchReturn<JSON> &
    PromiseLike<UseFetchReturn<JSON>>
  text: () => UseFetchReturn<string> & PromiseLike<UseFetchReturn<string>>
  blob: () => UseFetchReturn<Blob> & PromiseLike<UseFetchReturn<Blob>>
  arrayBuffer: () => UseFetchReturn<ArrayBuffer> &
    PromiseLike<UseFetchReturn<ArrayBuffer>>
  formData: () => UseFetchReturn<FormData> &
    PromiseLike<UseFetchReturn<FormData>>
}
type Combination = "overwrite" | "chain"
export interface BeforeFetchContext {
  /**
   * 当前请求的计算 url
   */
  url: string
  /**
   * 当前请求的请求选项
   */
  options: RequestInit
  /**
   * 取消当前请求
   */
  cancel: Fn
}
export interface AfterFetchContext<T = any> {
  response: Response
  data: T | null
  context: BeforeFetchContext
  execute: (throwOnFailed?: boolean) => Promise<any>
}
export interface OnFetchErrorContext<T = any, E = any> {
  error: E
  data: T | null
  response: Response | null
  context: BeforeFetchContext
  execute: (throwOnFailed?: boolean) => Promise<any>
}
export interface UseFetchOptions {
  /**
   * Fetch 函数
   */
  fetch?: typeof window.fetch
  /**
   * 使用 `useFetch` 时将自动运行 fetch
   *
   * @default true
   */
  immediate?: boolean
  /**
   * 当以下情况时将自动重新获取：
   * - 如果 URL 是 ref，则 URL 更改
   * - 如果 payload 是 ref，则 payload 更改
   *
   * @default false
   */
  refetch?: MaybeRefOrGetter<boolean>
  /**
   * 请求完成之前的初始数据
   *
   * @default null
   */
  initialData?: any
  /**
   * 在给定毫秒数后中止请求的超时时间
   * `0` 表示使用浏览器默认值
   *
   * @default 0
   */
  timeout?: number
  /**
   * 允许在 fetch 错误时更新 `data` ref，无论是否提供，或在 `onFetchError` 回调中修改
   *
   * @default false
   */
  updateDataOnError?: boolean
  /**
   * 将在 fetch 请求调度之前立即运行
   */
  beforeFetch?: (
    ctx: BeforeFetchContext,
  ) =>
    | Promise<Partial<BeforeFetchContext> | void>
    | Partial<BeforeFetchContext>
    | void
  /**
   * 将在 fetch 请求返回后立即运行。
   * 在任何 2xx 响应后运行
   */
  afterFetch?: (
    ctx: AfterFetchContext,
  ) => Promise<Partial<AfterFetchContext>> | Partial<AfterFetchContext>
  /**
   * 将在 fetch 请求返回后立即运行。
   * 在任何 4xx 和 5xx 响应后运行
   */
  onFetchError?: (
    ctx: OnFetchErrorContext,
  ) => Promise<Partial<OnFetchErrorContext>> | Partial<OnFetchErrorContext>
}
export interface CreateFetchOptions {
  /**
   * 将前缀到所有 url 的基本 URL，除非 url 是绝对的
   */
  baseUrl?: MaybeRefOrGetter<string>
  /**
   * 确定 beforeFetch、afterFetch、onFetchError 的继承行为
   * @default 'chain'
   */
  combination?: Combination
  /**
   * useFetch 函数的默认选项
   */
  options?: UseFetchOptions
  /**
   * fetch 请求的选项
   */
  fetchOptions?: RequestInit
}
export declare function createFetch(
  config?: CreateFetchOptions,
): typeof useFetch
export declare function useFetch<T>(
  url: MaybeRefOrGetter<string>,
): UseFetchReturn<T> & PromiseLike<UseFetchReturn<T>>
export declare function useFetch<T>(
  url: MaybeRefOrGetter<string>,
  useFetchOptions: UseFetchOptions,
): UseFetchReturn<T> & PromiseLike<UseFetchReturn<T>>
export declare function useFetch<T>(
  url: MaybeRefOrGetter<string>,
  options: RequestInit,
  useFetchOptions?: UseFetchOptions,
): UseFetchReturn<T> & PromiseLike<UseFetchReturn<T>>
```
