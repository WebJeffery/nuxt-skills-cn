---
title: 组件事件不会冒泡
impact: MEDIUM
impactDescription: Vue 组件事件仅到达直接父组件 - 兄弟和祖父通信需要替代模式
type: gotcha
tags: [vue3, events, emit, event-bubbling, provide-inject, state-management]
---

# 组件事件不会冒泡

**影响：中** - 与原生 DOM 事件不同，Vue 组件事件不会沿组件树向上冒泡。当子组件发出事件时，只有其直接父组件可以监听它。祖父组件和兄弟组件永远不会收到该事件。

对于来自原生 JavaScript 的开发人员来说，这是一个常见的困惑来源，因为事件在 DOM 中自然向上冒泡。

## 任务清单

- [ ] 仅期望来自直接子组件的事件
- [ ] 对深层嵌套通信使用 provide/inject
- [ ] 对复杂的跨组件通信使用状态管理（Pinia）
- [ ] 如果需要手动冒泡，请在每个级别重新发出事件
- [ ] 考虑组件层次结构是否太深

## 问题

```vue
<!-- GrandParent.vue -->
<template>
  <!-- 此处理程序永远不会触发孙子组件事件 -->
  <Parent @item-selected="handleSelect" />
</template>
```

```vue
<!-- Parent.vue -->
<template>
  <Child />
</template>
```

```vue
<!-- Child.vue -->
<script setup>
const emit = defineEmits(['item-selected'])

function selectItem(item) {
  // 此事件到达 Parent，但不到达 GrandParent
  emit('item-selected', item)
}
</script>
```

## 解决方案 1：在每个级别重新发出（简单情况）

通过每个组件手动转发事件。

**正确：**
```vue
<!-- GrandParent.vue -->
<template>
  <Parent @item-selected="handleSelect" />
</template>
```

```vue
<!-- Parent.vue -->
<script setup>
const emit = defineEmits(['item-selected'])
</script>

<template>
  <!-- 重新发出到祖父组件 -->
  <Child @item-selected="(item) => emit('item-selected', item)" />
</template>
```

```vue
<!-- Child.vue -->
<script setup>
const emit = defineEmits(['item-selected'])

function selectItem(item) {
  emit('item-selected', item)
}
</script>
```

**缺点：** 对于深层嵌套的组件变得繁琐。

## 解决方案 2：Provide/Inject（祖先通信）

对于深层嵌套的组件，从祖先提供回调。

**正确：**
```vue
<!-- GrandParent.vue -->
<script setup>
import { provide } from 'vue'

function handleItemSelected(item) {
  console.log('项目已选择：', item)
}

// 将回调提供给所有后代
provide('onItemSelected', handleItemSelected)
</script>

<template>
  <Parent />
</template>
```

```vue
<!-- Parent.vue - 不需要更改 -->
<template>
  <Child />
</template>
```

```vue
<!-- Child.vue -->
<script setup>
import { inject } from 'vue'

// 从任何祖先注入回调
const onItemSelected = inject('onItemSelected', () => {})

function selectItem(item) {
  onItemSelected(item)
}
</script>
```

**优点：**
- 跳过中间组件
- 没有 props 钻取或重新发出
- 在任何嵌套深度工作

## 解决方案 3：状态管理（复杂应用程序）

对于跨组件通信，特别是兄弟组件或不相关组件之间，使用 Pinia。

**正确：**
```js
// stores/selection.js
import { defineStore } from 'pinia'
import { ref } from 'vue'

export const useSelectionStore = defineStore('selection', () => {
  const selectedItem = ref(null)

  function selectItem(item) {
    selectedItem.value = item
  }

  return { selectedItem, selectItem }
})
```

```vue
<!-- DeepChild.vue - 更新状态 -->
<script setup>
import { useSelectionStore } from '@/stores/selection'

const store = useSelectionStore()

function handleSelect(item) {
  store.selectItem(item)
}
</script>
```

```vue
<!-- SiblingComponent.vue - 对状态做出反应 -->
<script setup>
import { useSelectionStore } from '@/stores/selection'

const store = useSelectionStore()
</script>

<template>
  <div v-if="store.selectedItem">
    已选择：{{ store.selectedItem.name }}
  </div>
</template>
```

## 解决方案 4：事件总线（谨慎使用）

对于真正解耦的组件，简单的事件总线可以工作：

```js
// eventBus.js
import mitt from 'mitt'
export const emitter = mitt()
```

```vue
<!-- ComponentA.vue -->
<script setup>
import { emitter } from './eventBus'

function notify() {
  emitter.emit('custom-event', { data: 'value' })
}
</script>
```

```vue
<!-- ComponentB.vue -->
<script setup>
import { onMounted, onUnmounted } from 'vue'
import { emitter } from './eventBus'

function handleEvent(data) {
  console.log('已收到：', data)
}

onMounted(() => emitter.on('custom-event', handleEvent))
onUnmounted(() => emitter.off('custom-event', handleEvent))
</script>
```

**警告：** 事件总线使数据流难以跟踪。首选 provide/inject 或状态管理。

## 比较表

| 方法 | 最适合 | 复杂性 |
|--------|----------|------------|
| 重新发出 | 1-2 层深度 | 低 |
| Provide/Inject | 深层嵌套，祖先通信 | 中 |
| Pinia/状态 | 复杂应用，兄弟通信 | 中 |
| 事件总线 | 真正解耦，罕见情况 | 低（但有风险）|

## 原生事件确实会冒泡

请注意，附加到元素的原生 DOM 事件仍然正常冒泡：

```vue
<!-- GrandParent.vue -->
<template>
  <!-- 原生点击从 Child 内部的按钮向上冒泡 -->
  <div @click="handleClick">
    <Parent />
  </div>
</template>
```

只有 Vue 组件事件（使用 `emit()` 发出的事件）不会冒泡。

## 参考
- [Vue.js 组件事件](https://vuejs.org/guide/components/events.html)
- [Vue.js Provide/Inject](https://vuejs.org/guide/components/provide-inject.html)
