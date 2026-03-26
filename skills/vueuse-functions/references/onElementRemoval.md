---
category: Sensors
---

# onElementRemoval

当元素或包含它的任何元素从 DOM 中移除时触发。

## 用法

```vue {13}
<script setup lang="ts">
import { onElementRemoval } from '@vueuse/core'
import { shallowRef, useTemplateRef } from 'vue'

const btnRef = useTemplateRef('btn')
const btnState = shallowRef(true)
const removedCount = shallowRef(0)

function btnOnClick() {
  btnState.value = !btnState.value
}

onElementRemoval(btnRef, () => ++removedCount.value)
</script>

<template>
  <button
    v-if="btnState"
    @click="btnOnClick"
  >
    recreate me
  </button>
  <button
    v-else
    ref="btnRef"
    @click="btnOnClick"
  >
    remove me
  </button>
  <b>removed times: {{ removedCount }}</b>
</template>
```

### 带有 Mutation Records 的回调

回调接收触发移除的 `MutationRecord` 对象数组。

```ts
import { onElementRemoval } from '@vueuse/core'

onElementRemoval(targetRef, (mutationRecords) => {
  console.log('Element removed', mutationRecords)
})
```

### 返回值

返回一个停止函数来停止观察。

```ts
const stop = onElementRemoval(targetRef, callback)

// 稍后,停止观察
stop()
```

## 类型声明

```ts
export interface OnElementRemovalOptions
  extends
    ConfigurableWindow,
    ConfigurableDocumentOrShadowRoot,
    WatchOptionsBase {}
/**
 * 当元素或包含它的任何元素从 DOM 中移除时触发。
 *
 * @param target
 * @param callback
 * @param options
 */
export declare function onElementRemoval(
  target: MaybeElementRef,
  callback: (mutationRecords: MutationRecord[]) => void,
  options?: OnElementRemovalOptions,
): Fn
```
