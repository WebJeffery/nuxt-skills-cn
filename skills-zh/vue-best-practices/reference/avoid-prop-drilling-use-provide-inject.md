---
title: 避免属性钻取 - 对深层组件树使用 Provide/Inject
impact: MEDIUM
impactDescription: 通过多个层传递 props 会创建维护负担并在中间组件之间产生紧密耦合
type: best-practice
tags: [vue3, props, provide-inject, component-design, state-management, architecture]
---

# 避免属性钻取 - 对深层组件树使用 Provide/Inject

**影响：中** - 属性钻取发生在你通过多个组件层传递 props 只是为了到达深层嵌套的子组件时。这会产生紧密耦合，使重构变得困难，并使中间组件充满了它们不使用的 props。

Vue 的 provide/inject API 允许祖先组件与任何后代共享数据，无论嵌套深度如何。

## 任务清单

- [ ] 识别 props 通过 2+ 个中间组件未更改的情况
- [ ] 对深层嵌套后代需要的数据使用 provide/inject
- [ ] 对在不相关的组件树之间共享的全局状态使用 Pinia
- [ ] 将 props 保留用于直接父子关系
- [ ] 在提供者级别记录提供的值

## 问题：属性钻取

```vue
<!-- App.vue -->
<template>
  <MainLayout :user="user" :theme="theme" :locale="locale" />
</template>
```

```vue
<!-- MainLayout.vue - 不使用这些 props，只是传递它们 -->
<template>
  <Sidebar :user="user" :theme="theme" />
  <Content :user="user" :locale="locale" />
</template>
```

```vue
<!-- Sidebar.vue - 仍在钻取... -->
<template>
  <UserMenu :user="user" />
  <ThemeToggle :theme="theme" />
</template>
```

```vue
<!-- UserMenu.vue - 最后使用 user prop -->
<template>
  <div>{{ user.name }}</div>
</template>
```

**问题：**
1. `MainLayout` 和 `Sidebar` 充满了它们不使用的 props
2. 添加新的共享值需要更新链中的每个组件
3. 删除深层嵌套组件需要更新所有祖先
4. 难以跟踪数据来自哪里

## 解决方案：Provide/Inject

**正确 - 提供者（祖先）：**
```vue
<!-- App.vue -->
<script setup>
import { provide, ref, readonly } from 'vue'

const user = ref({ name: 'John', role: 'admin' })
const theme = ref('dark')
const locale = ref('en')

// 提供给所有后代
provide('user', readonly(user)) // readonly 防止变异
provide('theme', theme)
provide('locale', locale)

// 如果需要，提供更新函数
provide('updateTheme', (newTheme) => {
  theme.value = newTheme
})
</script>

<template>
  <MainLayout />
</template>
```

**正确 - 中间组件现在干净：**
```vue
<!-- MainLayout.vue - 不需要 props -->
<template>
  <Sidebar />
  <Content />
</template>
```

```vue
<!-- Sidebar.vue - 不需要 props -->
<template>
  <UserMenu />
  <ThemeToggle />
</template>
```

**正确 - 消费者（后代）：**
```vue
<!-- UserMenu.vue -->
<script setup>
import { inject } from 'vue'

// 从任何祖先注入
const user = inject('user')
</script>

<template>
  <div>{{ user.name }}</div>
</template>
```

```vue
<!-- ThemeToggle.vue -->
<script setup>
import { inject } from 'vue'

const theme = inject('theme')
const updateTheme = inject('updateTheme')

function toggleTheme() {
  updateTheme(theme.value === 'dark' ? 'light' : 'dark')
}
</script>

<template>
  <button @click="toggleTheme">
    当前：{{ theme }}
  </button>
</template>
```

## Provide/Inject 的最佳实践

### 1. 对大型应用使用 Symbol 键

使用符号避免字符串键冲突：

```js
// keys.js
export const UserKey = Symbol('user')
export const ThemeKey = Symbol('theme')
```

```vue
<script setup>
import { provide } from 'vue'
import { UserKey, ThemeKey } from './keys'

provide(UserKey, user)
provide(ThemeKey, theme)
</script>
```

```vue
<script setup>
import { inject } from 'vue'
import { UserKey } from './keys'

const user = inject(UserKey)
</script>
```

### 2. 提供默认值

处理没有祖先提供值的情况：

```vue
<script setup>
import { inject } from 'vue'

// 带有默认值
const theme = inject('theme', 'light')

// 带有对象工厂函数（避免共享引用）
const config = inject('config', () => ({ debug: false }), true)
</script>
```

### 3. 使用 readonly 确保数据安全

防止后代变异提供的数据：

```vue
<script setup>
import { provide, ref, readonly } from 'vue'

const user = ref({ name: 'John' })

// 后代可以读取但不能变异
provide('user', readonly(user))

// 提供单独的方法进行更新
provide('updateUser', (updates) => {
  Object.assign(user.value, updates)
})
</script>
```

### 4. 提供计算值以实现响应式

```vue
<script setup>
import { provide, computed } from 'vue'

const items = ref([1, 2, 3])

// 后代将响应式更新
provide('itemCount', computed(() => items.value.length))
</script>
```

## 何时使用什么

| 场景 | 解决方案 |
|----------|----------|
| 直接父子 | Props |
| 1-2 层深度 | Props（钻取是可接受的） |
| 深层嵌套，相同的组件树 | Provide/Inject |
| 不相关的组件树 | Pinia（状态管理） |
| 跨应用全局状态 | Pinia |
| 插件配置 | 从插件安装 Provide/Inject |

## Provide/Inject vs Pinia

**Provide/Inject：**
- 限定于组件子树
- 适用于组件库内部
- 没有 DevTools 支持
- 仅祖先-后代关系

**Pinia：**
- 全局，可在任何地方访问
- 优秀的 DevTools 集成
- 更适合应用程序状态
- 在不相关的组件之间工作

## 参考
- [Vue.js Provide/Inject](https://vuejs.org/guide/components/provide-inject.html)
- [Vue.js - 属性钻取](https://vuejs.org/guide/components/provide-inject.html#prop-drilling)
- [Pinia 文档](https://pinia.vuejs.org/)
