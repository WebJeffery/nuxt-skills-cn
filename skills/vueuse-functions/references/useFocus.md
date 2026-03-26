---
category: Sensors
---

# useFocus

响应式实用程序，用于跟踪或设置 DOM 元素的焦点状态。状态更改以反映目标元素是否为焦点元素。从外部设置响应式值将分别为 `true` 和 `false` 值触发 `focus` 和 `blur` 事件。

## 基本用法

```ts
import { useFocus } from '@vueuse/core'

const target = shallowRef()
const { focused } = useFocus(target)

watch(focused, (focused) => {
  if (focused)
    console.log('input element has been focused')
  else console.log('input element has lost focus')
})
```

## 设置初始焦点

要在元素首次渲染时聚焦元素，可以将 `initialValue` 选项提供为 `true`。这将在目标元素上触发 `focus` 事件。

```ts
import { useFocus } from '@vueuse/core'

const target = shallowRef()
const { focused } = useFocus(target, { initialValue: true })
```

## 更改焦点状态

`focused` 响应式 ref 的更改将分别为 `true` 和 `false` 值自动触发 `focus` 和 `blur` 事件。您可以利用此行为来聚焦目标元素，作为另一个操作的结果（例如，当按钮点击时如下所示）。

```vue
<script setup lang="ts">
import { useFocus } from '@vueuse/core'
import { shallowRef } from 'vue'

const input = shallowRef()
const { focused } = useFocus(input)
</script>

<template>
  <div>
    <button type="button" @click="focused = true">
      Click me to focus input below
    </button>
    <input ref="input" type="text">
  </div>
</template>
```

## 类型声明

```ts
export interface UseFocusOptions extends ConfigurableWindow {
  /**
   * 初始值。如果设置为 true，则将在目标上设置焦点
   *
   * @default false
   */
  initialValue?: boolean
  /**
   * 复制 CSS 的 :focus-visible 行为
   *
   * @default false
   */
  focusVisible?: boolean
  /**
   * 防止在元素聚焦时滚动到元素。
   *
   * @default false
   */
  preventScroll?: boolean
}
export interface UseFocusReturn {
  /**
   * 如果读取为 true，则元素具有焦点。如果读取为 false，则元素不具有焦点
   * 如果设置为 true，则元素将被聚焦。如果设置为 false，则元素将失去焦点。
   */
  focused: WritableComputedRef<boolean>
}
/**
 * 跟踪或设置 DOM 元素的焦点状态。
 *
 * @see https://vueuse.org/useFocus
 * @param target 焦点和模糊事件的目标元素。
 * @param options
 */
export declare function useFocus(
  target: MaybeElementRef,
  options?: UseFocusOptions,
): UseFocusReturn
```
