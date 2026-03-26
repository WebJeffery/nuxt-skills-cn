---
category: Sensors
---

# useFocusWithin

响应式实用程序，用于跟踪元素或其后代之一是否具有焦点。它旨在匹配 `:focus-within` CSS 伪类的行为。一个常见的用例是在表单元素上，以查看其任何输入当前是否具有焦点。

## 基本用法

```vue
<script setup lang="ts">
import { useFocusWithin } from '@vueuse/core'
import { ref, watch } from 'vue'

const target = ref()
const { focused } = useFocusWithin(target)

watch(focused, (focused) => {
  if (focused)
    console.log('Target contains focused element')
  else
    console.log('Target does NOT contain focused element')
})
</script>

<template>
  <form ref="target">
    <input type="text" placeholder="First Name">
    <input type="text" placeholder="Last Name">
    <input type="text" placeholder="Email">
    <input type="text" placeholder="Password">
  </form>
</template>
```

## 类型声明

```ts
export interface UseFocusWithinReturn {
  /**
   * 如果元素或其任何后代具有焦点，则为 true
   */
  focused: ComputedRef<boolean>
}
/**
 * 跟踪焦点是否包含在目标元素内
 *
 * @see https://vueuse.org/useFocusWithin
 * @param target 要跟踪的目标元素
 * @param options Focus within 选项
 */
export declare function useFocusWithin(
  target: MaybeElementRef,
  options?: ConfigurableWindow,
): UseFocusWithinReturn
```
