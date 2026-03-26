---
category: Component
---

# useMounted

ref 中的挂载状态。

## 用法

```ts
import { useMounted } from '@vueuse/core'

const isMounted = useMounted()
```

这本质上是以下内容的简写：

```ts
const isMounted = ref(false)

onMounted(() => {
  isMounted.value = true
})
```

## 类型声明

```ts
/**
 * ref 中的挂载状态。
 *
 * @see https://vueuse.org/useMounted
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useMounted(): ShallowRef<boolean, boolean>
```
