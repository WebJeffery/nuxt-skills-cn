---
title: 将 Provide/Inject 变更保留在提供者组件中
impact: MEDIUM
impactDescription: 允许注入者变更提供的状态会导致不可预测的数据流和困难的调试
type: best-practice
tags: [vue3, provide-inject, state-management, architecture, debugging]
---

# 将 Provide/Inject 变更保留在提供者组件中

**影响：中** - 使用响应式 provide/inject 值时，应尽可能将变更保留在提供者组件内。允许子组件直接变更注入的状态会导致对变更来源的混淆，使调试和维护变得困难。

## 任务清单

- [ ] 将对提供状态的所有变更保留在提供者组件中
- [ ] 为子组件修改提供更新函数以及响应式数据
- [ ] 使用 `readonly()` 防止注入者的意外变更
- [ ] 记录提供的值及其更新模式

## 问题：分散的变更

**错误 - 注入者直接变更提供的状态：**
```vue
<!-- Provider.vue -->
<script setup>
import { ref, provide } from 'vue'

const user = ref({ name: 'John', preferences: { theme: 'dark' } })
provide('user', user)
</script>
```

```vue
<!-- DeepChild.vue -->
<script setup>
import { inject } from 'vue'

const user = inject('user')

// 有问题：从树中的任何位置变更
function updateTheme(theme) {
  user.value.preferences.theme = theme // 这个更改来自哪里？
}
</script>
```

**问题：**
1. 难以跟踪状态更改的来源
2. 多个组件可能会不一致地更改相同的数据
3. 没有集中的验证或副作用
4. 在大型应用中调试变得噩梦

## 解决方案：提供更新函数

**正确 - 提供者控制所有变更：**
```vue
<!-- Provider.vue -->
<script setup>
import { ref, provide, readonly } from 'vue'

const user = ref({ name: 'John', preferences: { theme: 'dark' } })

// 带验证的变更函数
function updateUserPreferences(preferences) {
  // 集中验证
  if (preferences.theme && !['dark', 'light', 'system'].includes(preferences.theme)) {
    console.warn('无效的主题')
    return false
  }

  // 集中副作用
  Object.assign(user.value.preferences, preferences)
  localStorage.setItem('userPrefs', JSON.stringify(user.value.preferences))
  return true
}

function updateUserName(name) {
  if (!name || name.length < 2) {
    console.warn('名称必须至少 2 个字符')
    return false
  }
  user.value.name = name
  return true
}

// 提供只读数据 + 更新函数
provide('user', {
  data: readonly(user),
  updatePreferences: updateUserPreferences,
  updateName: updateUserName
})
</script>
```

```vue
<!-- DeepChild.vue -->
<script setup>
import { inject } from 'vue'

const { data: user, updatePreferences } = inject('user')

function changeTheme(theme) {
  // 清晰的意图：调用提供者的更新函数
  const success = updatePreferences({ theme })
  if (!success) {
    // 处理验证失败
  }
}
</script>

<template>
  <!-- data 是只读的，防止意外变更 -->
  <div>主题: {{ user.preferences.theme }}</div>
  <button @click="changeTheme('light')">浅色模式</button>
</template>
```

## 模式：使用只读保护的 Provide/Inject

使用 `readonly()` 在运行时强制执行模式：

```vue
<!-- Provider.vue -->
<script setup>
import { ref, provide, readonly } from 'vue'

const cart = ref([])

function addItem(item) {
  cart.value.push(item)
}

function removeItem(id) {
  cart.value = cart.value.filter(item => item.id !== id)
}

function clearCart() {
  cart.value = []
}

// 提供只读购物车 + 受控变更
provide('cart', {
  items: readonly(cart),
  addItem,
  removeItem,
  clearCart
})
</script>
```

```vue
<!-- CartDisplay.vue -->
<script setup>
import { inject } from 'vue'

const { items, removeItem } = inject('cart')

// items.push(newItem) 会在开发模式下触发警告
// 必须使用提供的 removeItem 函数
</script>

<template>
  <div v-for="item in items" :key="item.id">
    {{ item.name }}
    <button @click="removeItem(item.id)">删除</button>
  </div>
</template>
```

## 此模式的好处

1. **可追溯性**：所有变更都通过已知函数进行
2. **验证**：在提供者中集中验证
3. **副作用**：一致的副作用（日志记录、存储、API 调用）
4. **测试**：更容易单独测试变更逻辑
5. **调试**：在 Vue DevTools 中有清晰的变更来源

## 何时直接变更可能是可接受的

在极少数情况下，直接变更可能是可接受的：

- 小型组件树中的非常简单、本地状态
- 隔离到单个表单向导的表单状态
- 不影响应用逻辑的临时状态

即使如此，考虑使用 `readonly()` 和更新函数以保持一致性。

## 参考
- [Vue.js Provide/Inject - 使用响应式](https://vuejs.org/guide/components/provide-inject.html#working-with-reactivity)
- [Vue 3 中 Provide/Inject API 的完整指南](https://www.codemag.com/Article/2101091/The-Complete-Guide-to-Provide-Inject-API-in-Vue-3-Part-1)
