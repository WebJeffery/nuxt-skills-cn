---
category: '@Router'
---

# useRouteHash

响应式 `route.hash` 的简写。

## 用法

```ts
import { useRouteHash } from '@vueuse/router'

const search = useRouteHash()

console.log(search.value) // route.hash
search.value = 'foobar' // router.replace({ hash: 'foobar' })
```

## Type Declarations

```ts
export declare function useRouteHash(
  defaultValue?: MaybeRefOrGetter<RouteHashValueRaw>,
  { mode, route, router }?: ReactiveRouteOptions,
): Ref<RouteHashValueRaw, RouteHashValueRaw>
```
