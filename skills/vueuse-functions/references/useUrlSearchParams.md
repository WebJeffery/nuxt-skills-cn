---
category: Browser
---

# useUrlSearchParams

响应式 [URLSearchParams](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams)

## 用法

```ts
import { useUrlSearchParams } from '@vueuse/core'

const params = useUrlSearchParams('history')

console.log(params.foo) // 'bar'

params.foo = 'bar'
params.vueuse = 'awesome'
// url 更新为 `?foo=bar&vueuse=awesome`
```

### Hash 模式

使用哈希模式路由时，将 `mode` 指定为 `hash`

```ts
import { useUrlSearchParams } from '@vueuse/core'

const params = useUrlSearchParams('hash')

params.foo = 'bar'
params.vueuse = 'awesome'
// url 更新为 `#/your/route?foo=bar&vueuse=awesome`
```

### Hash Params

使用历史模式路由，但想使用哈希作为参数时，将 `mode` 指定为 `hash-params`

```ts
import { useUrlSearchParams } from '@vueuse/core'

const params = useUrlSearchParams('hash-params')

params.foo = 'bar'
params.vueuse = 'awesome'
// url 更新为 `/your/route#foo=bar&vueuse=awesome`
```

### Custom Stringify Function

您可以提供自定义函数，使用 `stringify` 选项来序列化 URL 参数。当您需要为查询字符串进行特殊格式化时，这很有用。

```js
import { useUrlSearchParams } from '@vueuse/core'

// 自定义字符串化函数，为空值删除等号
const params = useUrlSearchParams('history', {
  stringify: (params) => {
    return params.toString().replace(/=(&|$)/g, '$1')
  }
})

params.foo = ''
params.bar = 'value'
// url 更新为 `?foo&bar=value` 而不是 `?foo=&bar=value`
```

## 类型声明

```ts
export type UrlParams = Record<string, string[] | string>
export interface UseUrlSearchParamsOptions<T> extends ConfigurableWindow {
  /**
   * @default true
   */
  removeNullishValues?: boolean
  /**
   * @default false
   */
  removeFalsyValues?: boolean
  /**
   * @default {}
   */
  initialValue?: T
  /**
   * 自动写回 `window.history`
   *
   * @default true
   */
  write?: boolean
  /**
   * 当 `write` 启用时，`window.history` 的写入模式
   * - `replace`：替换当前历史记录条目
   * - `push`：推送新的历史记录条目
   * @default 'replace'
   */
  writeMode?: "replace" | "push"
  /**
   * 用于序列化 URL 参数的自定义函数
   * 当提供时，将使用此函数而不是默认的 URLSearchParams.toString()
   * @param params 要序列化的 URLSearchParams 对象
   * @returns 序列化的查询字符串（不应包含前导 '?' 或 '#'）
   */
  stringify?: (params: URLSearchParams) => string
}
/**
 * 响应式 URLSearchParams
 *
 * @see https://vueuse.org/useUrlSearchParams
 * @param mode
 * @param options
 */
export declare function useUrlSearchParams<
  T extends Record<string, any> = UrlParams,
>(
  mode?: "history" | "hash" | "hash-params",
  options?: UseUrlSearchParamsOptions<T>,
): T
```
