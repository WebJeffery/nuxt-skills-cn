---
category: '@Router'
---

# useRouteQuery

响应式 `route.query` 的简写。当 ref 更改时更新 URL 查询参数。

## 用法

```ts
import { useRouteQuery } from '@vueuse/router'

const search = useRouteQuery('search')

const search = useRouteQuery('search', 'foo') // 或使用默认值

const page = useRouteQuery('page', '1', { transform: Number }) // 或转换值

console.log(search.value) // route.query.search
search.value = 'foobar' // router.replace({ query: { search: 'foobar' } })
```

### 导航模式

默认情况下，更改使用 `router.replace()`。设置 `mode: 'push'` 以改用 `router.push()`。

```ts
import { useRouteQuery } from '@vueuse/router'

const search = useRouteQuery('search', '', { mode: 'push' })
```

### 双向转换

您可以提供单独的 `get` 和 `set` 转换来读取和写入值。

```ts
import { useRouteQuery } from '@vueuse/router'

const filters = useRouteQuery('filters', [], {
  transform: {
    get: v => v ? v.split(',') : [],
    set: v => v.join(','),
  },
})

// 读取：'a,b,c' -> ['a', 'b', 'c']
// 写入：['a', 'b', 'c'] -> 'a,b,c'
```

### 默认值行为

当值等于默认值时，查询参数将从 URL 中删除。

```ts
import { useRouteQuery } from '@vueuse/router'

const page = useRouteQuery('page', '1')

page.value = '2' // URL: ?page=2
page.value = '1' // URL:（无 page 参数，因为它等于默认值）
```

## Type Declarations

```ts
export declare function useRouteQuery(
  name: string,
): Ref<undefined | null | string | string[]>
export declare function useRouteQuery<
  T extends RouteQueryValueRaw = RouteQueryValueRaw,
  K = T,
>(
  name: string,
  defaultValue?: MaybeRefOrGetter<T>,
  options?: ReactiveRouteOptionsWithTransform<T, K>,
): Ref<K>
```
