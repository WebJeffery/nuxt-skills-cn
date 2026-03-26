---
category: Browser
related:
  - useClipboard
---

# useClipboardItems

响应式 [Clipboard API](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard_API)。提供响应剪贴板命令(剪切、复制和粘贴)以及异步读取和写入系统剪贴板的能力。对剪贴板内容的访问受到 [Permissions API](https://developer.mozilla.org/en-US/docs/Web/API/Permissions_API) 的限制。未经用户许可,不允许读取或更改剪贴板内容。

## 与 `useClipboard` 的区别

`useClipboard` 是一个"仅文本"函数,而 `useClipboardItems` 是基于 [ClipboardItem](https://developer.mozilla.org/en-US/docs/Web/API/ClipboardItem) 的函数。您可以使用 `useClipboardItems` 复制 [ClipboardItem](https://developer.mozilla.org/en-US/docs/Web/API/ClipboardItem) 支持的任何内容。

## 用法

```vue
<script setup lang="ts">
import { useClipboardItems } from '@vueuse/core'

const mime = 'text/plain'
const source = ref([
  new ClipboardItem({
    [mime]: new Blob(['plain text'], { type: mime }),
  })
])

const { content, copy, copied, isSupported } = useClipboardItems({ source })
</script>

<template>
  <div v-if="isSupported">
    <button @click="copy(source)">
      <!-- 默认情况下,`copied` 将在 1.5s 后重置 -->
      <span v-if="!copied">复制</span>
      <span v-else>已复制!</span>
    </button>
    <p>
      当前复制: <code>{{ content || '无' }}</code>
    </p>
  </div>
  <p v-else>
    您的浏览器不支持 Clipboard API
  </p>
</template>
```

## 类型声明

```ts
export interface UseClipboardItemsOptions<
  Source,
> extends ConfigurableNavigator {
  /**
   * 为剪贴板启用读取
   *
   * @default false
   */
  read?: boolean
  /**
   * 复制源
   */
  source?: Source
  /**
   * 重置 `copied` ref 状态的毫秒数
   *
   * @default 1500
   */
  copiedDuring?: number
}
export interface UseClipboardItemsReturn<Optional> extends Supportable {
  content: Readonly<Ref<ClipboardItems>>
  copied: Readonly<ShallowRef<boolean>>
  copy: Optional extends true
    ? (content?: ClipboardItems) => Promise<void>
    : (text: ClipboardItems) => Promise<void>
  read: () => void
}
/**
 * 响应式 Clipboard API。
 *
 * @see https://vueuse.org/useClipboardItems
 * @param options
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useClipboardItems(
  options?: UseClipboardItemsOptions<undefined>,
): UseClipboardItemsReturn<false>
export declare function useClipboardItems(
  options: UseClipboardItemsOptions<MaybeRefOrGetter<ClipboardItems>>,
): UseClipboardItemsReturn<true>
```
