---
category: Browser
---

# useTextareaAutosize

根据内容自动更新 textarea 的高度。

## 用法

### 简单示例

```vue
<script setup lang="ts">
import { useTextareaAutosize } from '@vueuse/core'

const { textarea, input } = useTextareaAutosize()
</script>

<template>
  <textarea
    ref="textarea"
    v-model="input"
    class="resize-none"
    placeholder="What's on your mind?"
  />
</template>
```

::: info

建议重置 textarea 元素的滚动条样式，以避免大量文本时出现不正确的高度值。

```css
textarea {
  -ms-overflow-style: none;
  scrollbar-width: none;
}

textarea::-webkit-scrollbar {
  display: none;
}
```

:::

### 带有 `rows` 属性

如果您需要在 textarea 元素上支持 rows 属性，则应该将 `styleProp` 选项设置为 `minHeight`。

```vue
<script setup lang="ts">
import { useTextareaAutosize } from '@vueuse/core'

const { textarea, input } = useTextareaAutosize({ styleProp: 'minHeight' })
</script>

<template>
  <textarea
    ref="textarea"
    v-model="input"
    class="resize-none"
    placeholder="What's on your mind?"
    rows="3"
  />
</template>
```

## 类型声明

```ts
export interface UseTextareaAutosizeOptions extends ConfigurableWindow {
  /** 要自动调整大小的 Textarea 元素。 */
  element?: MaybeRef<HTMLTextAreaElement | undefined | null>
  /** Textarea 内容。 */
  input?: MaybeRef<string>
  /** 应触发 textarea 调整大小的监视源。 */
  watch?: WatchSource | MultiWatchSources
  /** 当 textarea 大小更改时调用的函数。 */
  onResize?: () => void
  /** 指定样式目标以根据 textarea 内容应用高度。如果未提供，它将使用 textarea 本身。  */
  styleTarget?: MaybeRef<HTMLElement | undefined>
  /** 指定用于操作高度的样式属性。可以是 `height | minHeight`。默认值为 `height`。 */
  styleProp?: "height" | "minHeight"
}
export interface UseTextareaAutosizeReturn {
  textarea: Ref<HTMLTextAreaElement | undefined | null>
  input: Ref<string>
  triggerResize: () => void
}
export declare function useTextareaAutosize(
  options?: UseTextareaAutosizeOptions,
): UseTextareaAutosizeReturn
```
