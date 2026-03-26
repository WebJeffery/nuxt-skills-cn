---
category: '@Router'
---

# useRouteParams

响应式 `route.params` 的简写。

## 用法

```ts
import { useRouteParams } from '@vueuse/router'

const userId = useRouteParams('userId')

const userId = useRouteParams('userId', '-1') // 或使用默认值

const userId = useRouteParams('page', '1', { transform: Number }) // 或转换值

console.log(userId.value) // route.params.userId
userId.value = '100' // router.replace({ params: { userId: '100' } })
```

## Type Declarations

```ts
export declare function useRouteParams(
  name: string,
): Ref<null | string | string[]>
export declare function useRouteParams<
  T extends RouteParamValueRaw = RouteParamValueRaw,
  K = T,
>(
  name: string,
  defaultValue?: MaybeRefOrGetter<T>,
  options?: ReactiveRouteOptionsWithTransform<T, K>,
): Ref<K>
```
