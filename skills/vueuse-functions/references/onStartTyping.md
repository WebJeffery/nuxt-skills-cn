---
category: Sensors
---

# onStartTyping

当用户在不可编辑元素上开始输入时触发。当用户在页面上的任何位置开始输入时,对于自动聚焦输入字段很有用。

## 用法

```vue
<script setup lang="ts">
import { onStartTyping } from '@vueuse/core'
import { useTemplateRef } from 'vue'

const input = useTemplateRef('input')

onStartTyping(() => {
  if (!input.value.active)
    input.value.focus()
})
</script>

<template>
  <input ref="input" type="text" placeholder="Start typing to focus">
</template>
```

## 工作原理

回调仅在以下情况下触发:

- 没有可编辑元素(`<input>`、`<textarea>` 或 `contenteditable`)被聚焦
- 按下的键是字母数字(A-Z, 0-9)
- 没有按住修饰键(Ctrl、Alt、Meta)

这允许用户在页面上的任何位置开始输入,而不会在使用键盘快捷键或与表单字段交互时意外触发回调。

## 类型声明

```ts
/**
 * 当用户在不可编辑元素上开始输入时触发。
 *
 * @see https://vueuse.org/onStartTyping
 * @param callback
 * @param options
 */
export declare function onStartTyping(
  callback: (event: KeyboardEvent) => void,
  options?: ConfigurableDocument,
): void
```
