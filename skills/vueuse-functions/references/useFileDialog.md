---
category: Browser
---

# useFileDialog

轻松打开文件对话框。

## 用法

```vue
<script setup lang="ts">
import { useFileDialog } from '@vueuse/core'

const { files, open, reset, onCancel, onChange } = useFileDialog({
  accept: 'image/*', // 设置为仅接受图像文件
  directory: true, // 如果设置为 true，则选择目录而不是文件
})

onChange((files) => {
  /** 对文件做些什么 */
})

onCancel(() => {
  /** 在取消时做些什么 */
})
</script>

<template>
  <button type="button" @click="open">
    选择文件
  </button>
</template>
```

## 类型声明

```ts
export interface UseFileDialogOptions extends ConfigurableDocument {
  /**
   * @default true
   */
  multiple?: MaybeRef<boolean>
  /**
   * @default '*'
   */
  accept?: MaybeRef<string>
  /**
   * 选择输入源以捕获文件。
   * @see [HTMLInputElement Capture](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/capture)
   */
  capture?: MaybeRef<string>
  /**
   * 打开文件对话框时重置。
   * @default false
   */
  reset?: MaybeRef<boolean>
  /**
   * 选择目录而不是文件。
   * @see [HTMLInputElement webkitdirectory](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement/webkitdirectory)
   * @default false
   */
  directory?: MaybeRef<boolean>
  /**
   * 要设置的初始文件。
   * @default null
   */
  initialFiles?: Array<File> | FileList
  /**
   * 用于文件对话框的输入元素。
   * @default document.createElement('input')
   */
  input?: MaybeElementRef<HTMLInputElement>
}
export interface UseFileDialogReturn {
  files: Ref<FileList | null>
  open: (localOptions?: Partial<UseFileDialogOptions>) => void
  reset: () => void
  onChange: EventHookOn<FileList | null>
  onCancel: EventHookOn
}
/**
 * 轻松打开文件对话框。
 *
 * @see https://vueuse.org/useFileDialog
 * @param options
 */
export declare function useFileDialog(
  options?: UseFileDialogOptions,
): UseFileDialogReturn
```
