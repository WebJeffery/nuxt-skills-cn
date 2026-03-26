---
category: '@Integrations'
---

# useDrauu

[drauu](https://github.com/antfu/drauu) 的响应式实例。

## 安装

```bash
npm i drauu@^0
```

## 用法

```vue
<script setup lang="ts">
import { toRefs } from '@vueuse/core'
import { useDrauu } from '@vueuse/integrations/useDrauu'
import { useTemplateRef } from 'vue'

const target = useTemplateRef('target')
const { undo, redo, canUndo, canRedo, brush } = useDrauu(target)
const { color, size } = toRefs(brush)
</script>

<template>
  <svg ref="target" />
</template>
```

## 类型声明

```ts
export type UseDrauuOptions = Omit<Options, "el">
export interface UseDrauuReturn {
  drauuInstance: Ref<Drauu | undefined>
  load: (svg: string) => void
  dump: () => string | undefined
  clear: () => void
  cancel: () => void
  undo: () => boolean | undefined
  redo: () => boolean | undefined
  canUndo: ShallowRef<boolean>
  canRedo: ShallowRef<boolean>
  brush: Ref<Brush>
  onChanged: EventHookOn
  onCommitted: EventHookOn
  onStart: EventHookOn
  onEnd: EventHookOn
  onCanceled: EventHookOn
}
/**
 * 响应式 drauu
 *
 * @see https://vueuse.org/useDrauu
 * @param target 目标 svg 元素
 * @param options Drauu 选项
 */
export declare function useDrauu(
  target: MaybeComputedElementRef,
  options?: UseDrauuOptions,
): UseDrauuReturn
```
