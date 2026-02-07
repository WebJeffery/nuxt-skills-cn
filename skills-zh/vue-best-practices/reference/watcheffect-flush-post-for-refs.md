---
title: 对带有模板 Ref 的 watchEffect 使用 flush post
impact: MEDIUM
impactDescription: 默认 watchEffect 在 DOM 更新之前运行，导致 refs 不同步
type: gotcha
tags: [vue3, watchEffect, template-refs, flush, dom-timing]
---

# 对带有模板 Ref 的 watchEffect 使用 flush post

**影响：中** - 默认情况下，`watchEffect` 在 DOM 更新之前运行。当监视模板 refs 时，这意味着 effect 可能使用过时或为 null 的 ref 值运行。使用 `flush: 'post'` 确保 effect 在 DOM 更新后运行，此时 refs 是当前的。

这个时序问题特别令人困惑，因为监视器运行了，但 ref 还没有反映当前的 DOM 状态。

## 任务清单

- [ ] 当 watchEffect 访问模板 refs 时使用 `{ flush: 'post' }`
- [ ] 或者，使用 `watchPostEffect` 辅助函数以获得更简洁的语法
- [ ] 仍然包含 null 检查，因为 refs 可能被卸载
- [ ] 考虑使用带有显式 ref 监视的 `watch` 作为替代方案

**错误：**
```vue
<script setup>
import { ref, watchEffect } from 'vue'

const inputEl = ref(null)
const text = ref('')

// 错误：在 DOM 更新之前运行 - ref 可能为 null 或过时
watchEffect(() => {
  // 首次运行时：inputEl.value 为 null（DOM 尚未渲染）
  // 更新时：可能引用旧的元素状态
  if (inputEl.value) {
    console.log('Input value:', inputEl.value.value) // 过时！
    inputEl.value.focus()
  }
})
</script>

<template>
  <input ref="inputEl" v-model="text" />
</template>
```

```vue
<script setup>
import { ref, watchEffect } from 'vue'

const items = ref([1, 2, 3])
const itemRefs = ref([])

// 错误：此时 refs 数组尚未填充
watchEffect(() => {
  console.log('Number of refs:', itemRefs.value.length) // 始终为 0！
})
</script>

<template>
  <div v-for="item in items" :key="item" :ref="el => itemRefs.value.push(el)">
    {{ item }}
  </div>
</template>
```

**正确：**
```vue
<script setup>
import { ref, watchEffect } from 'vue'

const inputEl = ref(null)
const text = ref('')

// 正确：flush: 'post' 在 DOM 更新后运行
watchEffect(() => {
  if (inputEl.value) {
    console.log('Input value:', inputEl.value.value) // 当前值！
    inputEl.value.focus()
  }
}, { flush: 'post' })
</script>

<template>
  <input ref="inputEl" v-model="text" />
</template>
```

```vue
<script setup>
import { ref, watchPostEffect } from 'vue'

const inputEl = ref(null)
const showInput = ref(true)

// 正确：watchPostEffect 是 flush: 'post' 的简写
watchPostEffect(() => {
  if (inputEl.value) {
    inputEl.value.focus()
  }
})
</script>

<template>
  <input v-if="showInput" ref="inputEl" />
</template>
```

```vue
<script setup>
import { ref, watch, onMounted } from 'vue'

const inputEl = ref(null)

// 替代方案：直接在 ref 上使用 watch
watch(inputEl, (el) => {
  if (el) {
    el.focus()
  }
}, { flush: 'post' })

// 替代方案：对于一次性设置，onMounted 就足够了
onMounted(() => {
  inputEl.value?.focus()
})
</script>

<template>
  <input ref="inputEl" />
</template>
```

```vue
<script setup>
import { useTemplateRef, watchPostEffect } from 'vue'

// Vue 3.5+ 使用 useTemplateRef
const input = useTemplateRef('my-input')

// 正确：watchPostEffect 与 useTemplateRef 一起使用
watchPostEffect(() => {
  input.value?.focus()
})
</script>

<template>
  <input ref="my-input" />
</template>
```

## Flush 选项说明

```javascript
// 默认：'pre' - 在 DOM 更新之前运行
watchEffect(() => { ... }) // 与 { flush: 'pre' } 相同

// 'post' - 在 DOM 更新后运行（用于 refs）
watchEffect(() => { ... }, { flush: 'post' })
watchPostEffect(() => { ... }) // 简写

// 'sync' - 同步运行（很少需要，可能导致问题）
watchEffect(() => { ... }, { flush: 'sync' })
watchSyncEffect(() => { ... }) // 简写
```

## 何时使用每种 Flush 模式

| 场景 | 推荐的 Flush |
|----------|-------------------|
| 访问模板 refs | `post` |
| 读取更新的 DOM | `post` |
| 在渲染之前触发 | `pre`（默认）|
| 性能关键的同步更新 | `sync`（谨慎使用）|

## 参考
- [Vue.js 监听器 - 回调 Flush 时序](https://vuejs.org/guide/essentials/watchers.html#callback-flush-timing)
- [Vue.js watchEffect API](https://vuejs.org/api/reactivity-core.html#watcheffect)
