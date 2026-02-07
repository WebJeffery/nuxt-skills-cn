---
title: 为动态组件引用使用 shallowRef
impact: MEDIUM
impactDescription: 在 reactive() 或 ref() 中存储组件会触发 Vue 警告并可能导致性能问题
type: gotcha
tags: [typescript, shallowRef, dynamic-components, reactivity, performance]
---

# 为动态组件引用使用 shallowRef

**影响：中等** - 当在响应式状态中存储 Vue 组件以进行动态渲染时，使用 `ref()` 或 `reactive()` 会导致 Vue 警告和不必要的响应式开销。应改用 `shallowRef()`。

## 任务清单

- [ ] 使用 `shallowRef` 存储组件引用
- [ ] 在响应式对象中存储组件时使用 `markRaw`
- [ ] 避免用深度响应式包装组件定义
- [ ] 检查控制台是否有 "[Vue warn]: Vue received a Component that was made a reactive object" 警告

## 问题所在

Vue 组件是具有不应被响应式化的内部属性的对象。当您在 `ref()` 或 `reactive()` 中存储组件时，Vue 会深度遍历所有属性，这会导致：

1. 触发控制台警告
2. 创建不必要的响应式代理
3. 可能导致组件标识的细微 bug
4. 影响性能

**错误 - 使用 ref() 存储组件：**
```typescript
import { ref } from 'vue'
import ComponentA from './ComponentA.vue'
import ComponentB from './ComponentB.vue'

// 错误：Vue 会警告不要让组件成为响应式对象
const currentComponent = ref(ComponentA)

function switchComponent() {
  currentComponent.value = ComponentB
}
```

**控制台警告：**
```
[Vue warn]: Vue received a Component that was made a reactive object.
This can lead to unnecessary performance overhead and should be avoided
by marking the component with `markRaw` or using `shallowRef` instead of `ref`.
```

## 解决方案 1：使用 shallowRef

`shallowRef` 只使 `.value` 引用成为响应式，而不包括其内容：

```typescript
import type { Component } from 'vue'
import { shallowRef } from 'vue'
import ComponentA from './ComponentA.vue'
import ComponentB from './ComponentB.vue'

// 正确：shallowRef 不会深度代理组件
const currentComponent = shallowRef<Component>(ComponentA)

function switchComponent() {
  currentComponent.value = ComponentB
}
```

```vue
<template>
  <component :is="currentComponent" />
</template>
```

## 解决方案 2：在响应式对象中使用 markRaw

当组件是较大响应式对象的一部分时：

```typescript
import type { Component } from 'vue'
import { markRaw, reactive } from 'vue'
import TabHome from './TabHome.vue'
import TabProfile from './TabProfile.vue'
import TabSettings from './TabSettings.vue'

interface Tab {
  name: string
  component: Component
}

// 正确：markRaw 防止组件对象成为响应式
const tabs = reactive<Tab[]>([
  { name: 'Home', component: markRaw(TabHome) },
  { name: 'Profile', component: markRaw(TabProfile) },
  { name: 'Settings', component: markRaw(TabSettings) }
])

const activeTab = shallowRef<Tab>(tabs[0])
```

```vue
<template>
  <div class="tabs">
    <button
      v-for="tab in tabs"
      :key="tab.name"
      @click="activeTab = tab"
    >
      {{ tab.name }}
    </button>
  </div>
  <component :is="activeTab.component" />
</template>
```

## TypeScript 类型定义

为动态组件提供适当的 TypeScript 支持：

```typescript
import type { Component, DefineComponent } from 'vue'
import { shallowRef } from 'vue'

// 通用组件类型
const currentComponent = shallowRef<Component | null>(null)

// 或者更具体的带 props 的类型
interface MyComponentProps {
  title: string
}

const currentComponent = shallowRef<DefineComponent<MyComponentProps> | null>(null)
```

## 使用 shallowRef 进行动态导入

当使用动态导入进行代码分割时：

```typescript
import type { Component } from 'vue'
import { defineAsyncComponent, shallowRef } from 'vue'

const currentComponent = shallowRef<Component | null>(null)

async function loadComponent(name: string) {
  const component = defineAsyncComponent(
    () => import(`./components/${name}.vue`)
  )
  currentComponent.value = component
}
```

## 组件注册表模式

对于选项卡系统或向导式界面：

```typescript
import type { Component } from 'vue'
import { markRaw, shallowRef } from 'vue'

// 类型安全的组件注册表
const componentRegistry = {
  home: markRaw(defineAsyncComponent(() => import('./Home.vue'))),
  about: markRaw(defineAsyncComponent(() => import('./About.vue'))),
  contact: markRaw(defineAsyncComponent(() => import('./Contact.vue')))
} as const

type ComponentKey = keyof typeof componentRegistry

const currentView = shallowRef<ComponentKey>('home')

// 计算属性获取当前组件
const currentComponent = computed(() => componentRegistry[currentView.value])
```

```vue
<template>
  <component :is="currentComponent" />
</template>
```

## 何时使用每种方法

| 场景 | 解决方案 |
|----------|----------|
| 单个动态组件引用 | `shallowRef` |
| 响应式数组/对象中的组件 | 对组件使用 `markRaw` |
| 组件映射/注册表 | 对每个组件使用 `markRaw` |
| 异步组件 | `defineAsyncComponent` + `shallowRef` |

## 常见错误

### 错误 1：在 ref 中使用 computed

```typescript
// 错误：仍会触发警告
const components = ref([ComponentA, ComponentB])
const current = computed(() => components.value[index.value])

// 正确：对数组使用 shallowRef
const components = shallowRef([ComponentA, ComponentB])
```

### 错误 2：在映射中忘记使用 markRaw

```typescript
// 错误：映射中的组件成为响应式
const routes = reactive(new Map([
  ['home', HomeComponent],
  ['about', AboutComponent]
]))

// 正确：将每个组件标记为 raw
const routes = reactive(new Map([
  ['home', markRaw(HomeComponent)],
  ['about', markRaw(AboutComponent)]
]))
```

## 参考

- [Vue.js 深入响应式 - 减少大型不可变结构的响应式开销](https://vuejs.org/guide/extras/reactivity-in-depth.html#reducing-reactivity-overhead-for-large-immutable-structures)
- [Vue.js API - shallowRef](https://vuejs.org/api/reactivity-advanced.html#shallowref)
- [Vue.js API - markRaw](https://vuejs.org/api/reactivity-advanced.html#markraw)
