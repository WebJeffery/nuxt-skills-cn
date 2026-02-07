---
title: 使用 v-once 和 v-memo 跳过不必要的更新
impact: MEDIUM
impactDescription: v-once 跳过静态内容的所有未来更新；v-memo 有条件地记忆子树
type: efficiency
tags: [vue3, performance, v-once, v-memo, optimization, directives]
---

# 使用 v-once 和 v-memo 跳过不必要的更新

**影响：中** - Vue 在每次响应式更改时都会重新评估模板。对于永不改变或不经常改变的内容，`v-once` 和 `v-memo` 告诉 Vue 跳过更新，减少渲染工作。

对真正静态的内容使用 `v-once`，对列表中有条件静态的内容使用 `v-memo`。

## 任务清单

- [ ] 对使用运行时数据但永远不需要更新的元素应用 `v-once`
- [ ] 对仅应在特定条件更改时更新的列表项应用 `v-memo`
- [ ] 验证记忆的内容不需要响应其他状态更改
- [ ] 使用 Vue DevTools 进行分析以确认更新跳过

## v-once：渲染一次，永不更新

**错误：**
```vue
<template>
  <!-- 错误：在每次父组件重新渲染时重新评估 -->
  <div class="terms-content">
    <h1>服务条款</h1>
    <p>版本：{{ termsVersion }}</p>
    <div v-html="termsContent"></div>
  </div>

  <!-- 此内容永不改变，但 Vue 每次渲染都检查它 -->
  <footer>
    <p>版权 {{ copyrightYear }} {{ companyName }}</p>
  </footer>
</template>
```

**正确：**
```vue
<template>
  <!-- 正确：渲染一次，在所有未来更新中跳过 -->
  <div class="terms-content" v-once>
    <h1>服务条款</h1>
    <p>版本：{{ termsVersion }}</p>
    <div v-html="termsContent"></div>
  </div>

  <!-- v-once 告诉 Vue 这永远不需要更新 -->
  <footer v-once>
    <p>版权 {{ copyrightYear }} {{ companyName }}</p>
  </footer>
</template>

<script setup>
// 这些值在组件创建时设置一次
const termsVersion = '2.1'
const termsContent = fetchedTermsHTML
const copyrightYear = 2024
const companyName = 'Acme Corp'
</script>
```

## v-memo：列表的条件记忆化

**错误：**
```vue
<template>
  <!-- 错误：当 selectedId 更改时所有项目都重新渲染 -->
  <div v-for="item in list" :key="item.id">
    <div :class="{ selected: item.id === selectedId }">
      <ExpensiveComponent :data="item" />
    </div>
  </div>
</template>
```

**正确：**
```vue
<template>
  <!-- 正确：项目仅在选择状态更改时重新渲染 -->
  <div
    v-for="item in list"
    :key="item.id"
    v-memo="[item.id === selectedId]"
  >
    <div :class="{ selected: item.id === selectedId }">
      <ExpensiveComponent :data="item" />
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const list = ref([/* 许多项目 */])
const selectedId = ref(null)

// 当 selectedId 更改时：
// - 仅之前选中的项目重新渲染（selected: true -> false）
// - 仅新选中的项目重新渲染（selected: false -> true）
// - 所有其他项目被跳过（v-memo 值未更改）
</script>
```

## 具有多个依赖项的 v-memo

```vue
<template>
  <!-- 仅当项目的选择或编辑状态更改时重新渲染 -->
  <div
    v-for="item in items"
    :key="item.id"
    v-memo="[item.id === selectedId, item.id === editingId]"
  >
    <ItemCard
      :item="item"
      :selected="item.id === selectedId"
      :editing="item.id === editingId"
    />
  </div>
</template>

<script setup>
const selectedId = ref(null)
const editingId = ref(null)
const items = ref([/* ... */])
</script>
```

## 带有空数组的 v-memo = v-once

```vue
<template>
  <!-- v-memo="[]" 等同于 v-once -->
  <div v-for="item in staticList" :key="item.id" v-memo="[]">
    {{ item.name }}
  </div>
</template>
```

## 何时不使用这些指令

```vue
<template>
  <!-- 不要：确实需要更新的内容 -->
  <div v-once>
    <span>计数：{{ count }}</span>  <!-- count 不会更新！ -->
  </div>

  <!-- 不要：当子组件有自己的响应式状态时 -->
  <div v-memo="[selected]">
    <InputField v-model="item.name" />  <!-- v-model 不会正常工作 -->
  </div>

  <!-- 不要：当记忆化好处最小时 -->
  <span v-once>{{ simpleText }}</span>  <!-- 开销不值得 -->
</template>
```

## 性能比较

| 场景 | 没有指令 | 使用 v-once/v-memo |
|----------|-------------------|-------------------|
| 静态页眉，父组件重新渲染 100 次 | 重新评估 100 次 | 评估 1 次 |
| 1000 个项目，选择更改 | 1000 个项目重新渲染 | 2 个项目重新渲染 |
| 复杂的子组件 | 完全重新渲染 | 如果被记忆化则跳过 |

## 调试记忆化的组件

```vue
<script setup>
import { onUpdated } from 'vue'

// 如果 v-memo 阻止更新，这不会触发
onUpdated(() => {
  console.log('组件已更新')
})
</script>
```

## 参考
- [Vue.js v-once 指令](https://vuejs.org/api/built-in-directives.html#v-once)
- [Vue.js v-memo 指令](https://vuejs.org/api/built-in-directives.html#v-memo)
- [Vue.js 性能 - 更新优化](https://vuejs.org/guide/best-practices/performance.html#update-optimizations)
