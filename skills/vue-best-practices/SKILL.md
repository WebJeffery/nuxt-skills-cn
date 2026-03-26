---
name: vue-best-practices
description: 必须用于 Vue.js 任务。强烈推荐使用 Composition API 和 `<script setup>` 以及 TypeScript 作为标准方法。涵盖 Vue 3、SSR、Volar、vue-tsc。加载用于任何 Vue、.vue 文件、Vue Router、Pinia 或 Vite 与 Vue 的工作。除非项目明确要求 Options API,否则始终使用 Composition API。
---

# Vue 最佳实践工作流

将此技能作为指令集使用。除非用户明确要求不同的顺序,否则按顺序遵循工作流。

## 核心原则
- **保持状态可预测:** 单一数据源,派生其他所有内容。
- **使数据流明确:** 大多数情况下使用 Props 向下、Events 向上。
- **偏好小型、专注的组件:** 更容易测试、重用和维护。
- **避免不必要的重新渲染:** 明智地使用计算属性和侦听器。
- **可读性很重要:** 编写清晰、自文档化的代码。

## 1) 在编码前确认架构(必需)

- 默认技术栈:Vue 3 + Composition API + `<script setup lang="ts">`。
- 如果项目明确使用 Options API,请加载 `vue-options-api-best-practices` 技能(如果可用)。
- 如果项目明确使用 JSX,请加载 `vue-jsx-best-practices` 技能(如果可用)。

### 1.1 必读核心参考(必需)

- 在实现任何 Vue 任务之前,确保阅读并应用这些核心参考:
  - `references/reactivity.md`
  - `references/sfc.md`
  - `references/component-data-flow.md`
  - `references/composables.md`
- 在整个任务期间保持这些参考处于活跃工作上下文中,而不仅仅是在出现特定问题时。

### 1.2 在编码前规划组件边界(必需)

在实现任何非平凡功能之前,创建一个简短的组件映射。

- 用一句话定义每个组件的单一职责。
- 默认将入口/根和路由级视图组件作为组合表面。
- 将功能 UI 和功能逻辑移出入口/根/视图组件,除非任务是有意设计的小型单文件演示。
- 在映射中为每个子组件定义 props/emits 契约。
- 添加多个组件时,偏好功能文件夹布局(`components/<feature>/...`, `composables/use<Feature>.ts`)。

## 2) 应用必要的 Vue 基础(必需)

这些是必要的、必须知道的基础知识。在每个 Vue 任务中使用已在 `1.1` 节中加载的核心参考应用所有这些知识。

### 响应式

- 必读参考来自 `1.1`:[reactivity](references/reactivity.md)
- 保持源状态最小(`ref`/`reactive`),用 `computed` 派生尽可能多的内容。
- 如有需要,使用侦听器处理副作用。
- 避免在模板中重新计算昂贵的逻辑。

### SFC 结构和模板安全

- 必读参考来自 `1.1`:[sfc](references/sfc.md)
- 保持 SFC 部分按此顺序:`<script>` → `<template>` → `<style>`。
- 保持 SFC 职责专注;拆分大型组件。
- 保持模板声明性;将分支/派生移至脚本。
- 应用 Vue 模板安全规则(`v-html`、列表渲染、条件渲染选择)。

### 保持组件专注

当组件具有**多个明确职责**时(例如数据编排 + UI,或多个独立的 UI 部分),拆分组件。

- 偏好**较小的组件 + composables** 而不是一个"巨型组件"
- 将**UI 部分**移至子组件(props 进,events 出)。
- 将**状态/副作用**移至 composables(`useXxx()`)。

应用客观拆分触发器。如果**任何**条件为真,则拆分组件:

- 它拥有编排/状态和多个部分的大量展示标记。
- 它有 3 个或更多不同的 UI 部分(例如:表单、筛选器、列表、页脚/状态)。
- 模板块重复或可能变得可重用(项目行、卡片、列表条目)。

入口/根和路由视图规则:

- 保持入口/根和路由视图组件精简:应用外壳/布局、提供者连接和功能组合。
- 当这些功能包含独立部分时,不要将完整功能实现放在入口/根/视图组件中。
- 对于 CRUD/列表功能(todo、表格、目录、收件箱),至少拆分为:
  - 功能容器组件
  - 输入/表单组件
  - 列表(和/或项目)组件
  - 页脚/操作或筛选/状态组件
- 仅允许非常小的可丢弃演示使用单文件实现;如果选择,明确证明为什么不需要拆分。

### 组件数据流

- 必读参考来自 `1.1`:[component-data-flow](references/component-data-flow.md)
- 使用 props 向下、events 向上作为主要模型。
- 仅对真正的双向组件契约使用 `v-model`。
- 仅对深度树依赖或共享上下文使用 provide/inject。
- 使用 `defineProps`、`defineEmits` 和 `InjectionKey` 保持契约明确和类型化。

### Composables

- 必读参考来自 `1.1`:[composables](references/composables.md)
- 当逻辑被重用、有状态或副作用繁重时,将其提取到 composables 中。
- 保持 composable API 小巧、类型化和可预测。
- 将功能逻辑与展示组件分离。

## 3) 仅在需求需要时考虑可选功能

### 3.1 标准可选功能

默认不添加这些。仅在需求存在时加载匹配的参考。

- Slots:父级需要控制子级内容/布局 → [component-slots](references/component-slots.md)
- Fallthrough attributes:包装器/基础组件必须安全转发 attrs/events → [component-fallthrough-attrs](references/component-fallthrough-attrs.md)
- 内置组件 `<KeepAlive>` 用于有状态视图缓存 → [component-keep-alive](references/component-keep-alive.md)
- 内置组件 `<Teleport>` 用于覆盖/门户 → [component-teleport](references/component-teleport.md)
- 内置组件 `<Suspense>` 用于异步子树回退边界 → [component-suspense](references/component-suspense.md)
- 动画相关功能:选择与所需运动行为匹配的最简单方法。
  - 内置组件 `<Transition>` 用于进入/离开效果 → [transition](references/component-transition.md)
  - 内置组件 `<TransitionGroup>` 用于动画列表变更 → [transition-group](references/component-transition-group.md)
  - 基于类的动画用于非进入/离开效果 → [animation-class-based-technique](references/animation-class-based-technique.md)
  - 状态驱动的动画用于用户输入驱动的动画 → [animation-state-driven-technique](references/animation-state-driven-technique.md)

### 3.2 不常见的可选功能

仅在存在明确的产品或技术需求时使用这些。

- Directives:行为是 DOM 特定的,不适合 composable/组件 → [directives](references/directives.md)
- Async components:繁重/很少使用的 UI 应该延迟加载 → [component-async](references/component-async.md)
- Render functions 仅当模板无法表达需求时 → [render-functions](references/render-functions.md)
- Plugins 当行为必须在应用范围内安装时 → [plugins](references/plugins.md)
- State management patterns:应用范围的共享状态跨越功能边界 → [state-management](references/state-management.md)

## 4) 在行为正确后运行性能优化

性能工作是功能后的过程。在核心行为实现和验证之前不要优化。

- 大型列表渲染瓶颈 → [perf-virtualize-large-lists](references/perf-virtualize-large-lists.md)
- 静态子树不必要地重新渲染 → [perf-v-once-v-memo-directives](references/perf-v-once-v-memo-directives.md)
- 热列表路径中的过度抽象 → [perf-avoid-component-abstraction-in-lists](references/perf-avoid-component-abstraction-in-lists.md)
- 昂贵的更新触发过于频繁 → [updated-hook-performance](references/updated-hook-performance.md)

## 5) 完成前的最终自检

- 核心行为有效且符合要求。
- 所有必读参考都已阅读和应用。
- 响应式模型最小且可预测。
- SFC 结构和模板规则得到遵循。
- 组件专注且分解良好,需要时进行拆分。
- 入口/根和路由视图组件保持组合表面,除非有明确的小型演示例外。
- 组件拆分决策明确且可辩护(职责边界清晰)。
- 数据流契约明确且类型化。
- Composables 在重用/复杂性证明合理的地方使用。
- 如适用,将状态/副作用移至 composables
- 可选功能仅在需求需要时使用。
- 性能更改仅在功能完成后应用。
