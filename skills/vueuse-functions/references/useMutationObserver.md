---
category: Elements
---

# useMutationObserver

监视对 DOM 树所做的更改。[MutationObserver MDN](https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver)

## 用法

```vue
<script setup lang="ts">
import { useMutationObserver } from '@vueuse/core'
import { ref, useTemplateRef } from 'vue'

const el = useTemplateRef('el')
const messages = ref([])

useMutationObserver(el, (mutations) => {
  if (mutations[0])
    messages.value.push(mutations[0].attributeName)
}, {
  attributes: true,
})
</script>

<template>
  <div ref="el">
    Hello VueUse
  </div>
</template>
```

## 类型声明

```ts
export interface UseMutationObserverOptions
  extends MutationObserverInit, ConfigurableWindow {}
export interface UseMutationObserverReturn extends Supportable {
  stop: () => void
  takeRecords: () => MutationRecord[] | undefined
}
/**
 * 监视对 DOM 树所做的更改。
 *
 * @see https://vueuse.org/useMutationObserver
 * @see https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver MutationObserver MDN
 * @param target
 * @param callback
 * @param options
 */
export declare function useMutationObserver(
  target:
    | MaybeComputedElementRef
    | MaybeComputedElementRef[]
    | MaybeRefOrGetter<MaybeElement[]>,
  callback: MutationCallback,
  options?: UseMutationObserverOptions,
): UseMutationObserverReturn
```
