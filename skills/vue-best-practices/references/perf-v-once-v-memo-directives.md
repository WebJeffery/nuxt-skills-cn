---
title: v-once 和 v-memo 指令最佳实践
impact: MEDIUM
impactDescription: v-once 和 v-memo 可以优化性能,但误用会导致过时的 UI 和难以调试的问题
type: best-practice
tags: [vue3, performance, v-once, v-memo, optimization]
---

# v-once 和 v-memo 指令最佳实践

**影响: MEDIUM** - `v-once` 和 `v-memo` 可以通过跳过更新来优化性能,但误用会导致过时的 UI 和难以调试的问题。

## 任务列表

- 使用 `v-once` 渲染永远不会改变的静态内容
- 使用 `v-memo` 跳过昂贵计算的更新
- 不要在响应式数据上使用 `v-once`
- 在 `v-memo` 中提供稳定的依赖项
- 仅在测量性能瓶颈后使用这些指令

## 使用 v-once 渲染静态内容

`v-once` 只渲染元素和组件一次,并跳过未来的更新。

**正确:**
```vue
<template>
  <div>
    <h1 v-once>{{ staticTitle }}</h1>
    <p>{{ dynamicContent }}</p>
  </div>
</template>
```

## 使用 v-memo 跳过昂贵计算的更新

`v-memo` 根据依赖项跳过虚拟 DOM 比较。

**正确:**
```vue
<template>
  <div v-for="item in items" :key="item.id" v-memo="[item.id, item.version]">
    <ExpensiveComponent :data="item" />
  </div>
</template>
```

## 错误示例

**错误:**
```vue
<template>
  <div v-once>
    <p>{{ reactiveData }}</p>
  </div>
</template>
```

**错误:**
```vue
<template>
  <div v-memo="[]">
    <p>{{ dynamicData }}</p>
  </div>
</template>
```

## 使用场景

### v-once 适用于:
- 静态标题和描述
- 图标和徽章
- 不会改变的配置信息

### v-memo 适用于:
- 大型列表中昂贵的子组件
- 计算成本高的渲染
- 依赖于特定标志的复杂条件渲染

## 注意事项

- `v-once` 会使元素及其子元素完全静态
- `v-memo` 只跳过虚拟 DOM 比较,不会阻止响应式更新
- 在使用这些指令之前先测量性能
- 过度使用可能导致代码难以维护
