---
category: Component
---

::: info
此函数将在未来版本中移除。

Vue 3.5 引入了 `useTemplateRef` API,它可以有效地替换 `templateRef` 的功能,因此我们推荐使用原生方法。
:::

# templateRef

将 ref 绑定到模板元素的简写。

## 用法

<!-- eslint-skip -->

```vue
<script lang="ts">
import { templateRef } from '@vueuse/core'

export default {
  setup() {
    const target = templateRef('target')

    // 不需要返回 `target`,它会神奇地绑定到 ref
  },
}
</script>

<template>
  <div ref="target" />
</template>
```

### 使用 JSX/TSX

```tsx
import { templateRef } from '@vueuse/core'

export default {
  setup() {
    const target = templateRef<HTMLElement | null>('target', null)

    // 使用字符串 ref
    return () => <div ref="target"></div>
  },
}
```

### `<script setup>`

当与 `<script setup>` 一起使用时,不需要这样做,因为所有变量都将暴露给模板。它将与 `ref` 完全相同。

```vue
<script setup lang="ts">
import { ref } from 'vue'

const target = ref<HTMLElement | null>(null)
</script>

<template>
  <div ref="target" />
</template>
```

## 类型声明

```ts
/**
 * @deprecated 改用 Vue 内置的 `useTemplateRef`。
 *
 * 将 ref 绑定到模板元素的简写。
 *
 * @see https://vueuse.org/templateRef
 * @param key
 * @param initialValue
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function templateRef<
  T extends HTMLElement | SVGElement | Component | null,
  Keys extends string = string,
>(key: Keys, initialValue?: T | null): Readonly<Ref<T>>
```
