---
category: Browser
---

# useSSRWidth

用于设置全局视口宽度，该宽度将在渲染依赖于视口宽度的 SSR 组件时使用，例如 [`useMediaQuery`](../useMediaQuery/index.md) 或 [`useBreakpoints`](../useBreakpoints/index.md)

## 用法

```ts
import { provideSSRWidth } from '@vueuse/core'

const app = createApp(App)

provideSSRWidth(500, app)
```

或在根组件中

```vue
<script setup lang="ts">
import { provideSSRWidth } from '@vueuse/core'

provideSSRWidth(500)
</script>
```

如果需要在子组件中检索提供的值

```vue
<script setup lang="ts">
import { useSSRWidth } from '@vueuse/core'

const width = useSSRWidth()
</script>
```

## Type Declarations

```ts
export declare function useSSRWidth(): number | undefined
export declare function provideSSRWidth(
  width: number | null,
  app?: App<unknown>,
): void
```
