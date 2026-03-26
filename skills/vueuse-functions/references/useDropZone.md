---
category: Elements
---

# useDropZone

创建可以放置文件的区域。

::: warning

由于 Safari 浏览器限制，文件类型验证仅在放置事件期间可能，而不是在拖动事件期间。因此，`isOverDropZone` 值将在 Safari 中的拖动操作期间始终为 `true`，无论文件类型如何。

:::

## 用法

```vue
<script setup lang="ts">
import { useDropZone } from '@vueuse/core'
import { useTemplateRef } from 'vue'

const dropZoneRef = useTemplateRef('dropZoneRef')

function onDrop(files: File[] | null) {
  // 当文件放置在区域上时调用
}

const { isOverDropZone } = useDropZone(dropZoneRef, {
  onDrop,
  // 指定要接收的数据类型。
  dataTypes: ['image/jpeg'],
  // 控制多文件放置
  multiple: true,
  // 是否防止未处理事件的默认行为
  preventDefaultForUnhandled: false,
})
</script>

<template>
  <div ref="dropZoneRef">
    在此处放置文件
  </div>
</template>
```

## 类型声明

```ts
export interface UseDropZoneReturn {
  files: ShallowRef<File[] | null>
  isOverDropZone: ShallowRef<boolean>
}
export interface UseDropZoneOptions {
  /**
   * 允许的数据类型，如果未设置，则允许所有数据类型。
   * 也可以是检查数据类型的函数。
   */
  dataTypes?:
    | MaybeRef<readonly string[]>
    | ((types: readonly string[]) => boolean)
  /**
   * 与 dataTypes 类似，但为自定义验证暴露 DataTransferItemList。
   * 如果提供，此函数优先于 dataTypes。
   */
  checkValidity?: (items: DataTransferItemList) => boolean
  onDrop?: (files: File[] | null, event: DragEvent) => void
  onEnter?: (files: File[] | null, event: DragEvent) => void
  onLeave?: (files: File[] | null, event: DragEvent) => void
  onOver?: (files: File[] | null, event: DragEvent) => void
  /**
   * 允许放置多个文件。默认为 true。
   */
  multiple?: boolean
  /**
   * 防止未处理事件的默认行为。默认为 false。
   */
  preventDefaultForUnhandled?: boolean
}
export declare function useDropZone(
  target: MaybeRefOrGetter<HTMLElement | Document | null | undefined>,
  options?: UseDropZoneOptions | UseDropZoneOptions["onDrop"],
): UseDropZoneReturn
```
