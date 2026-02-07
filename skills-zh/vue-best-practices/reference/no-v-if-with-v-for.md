---
title: 永远不要在同一元素上使用 v-if 和 v-for
impact: HIGH
impactDescription: 导致令人困惑的优先级问题和 Vue 2 到 3 迁移错误
type: capability
tags: [vue3, v-if, v-for, conditional-rendering, list-rendering, eslint]
---

# 永远不要在同一元素上使用 v-if 和 v-for

**影响：高** - 在同一元素上使用 `v-if` 和 `v-for` 会创建 Vue 2 和 Vue 3 之间不同的模糊优先级。在 Vue 2 中，`v-for` 具有更高的优先级；在 Vue 3 中，`v-if` 具有更高的优先级。这种破坏性更改会在迁移期间导致微妙的错误，并使代码意图不清楚。

ESLint 规则 `vue/no-use-v-if-with-v-for` 强制执行此最佳实践。

## 任务清单

- [ ] 永远不要将 v-if 和 v-for 放在同一元素上
- [ ] 对于过滤列表项：使用过滤数组的计算属性
- [ ] 对于隐藏整个列表：用 `<template v-if>` 包裹 v-for
- [ ] 启用 eslint-plugin-vue 规则 `vue/no-use-v-if-with-v-for`

**错误：**
```html
<!-- 错误：v-if 和 v-for 在同一元素上 - 模糊的优先级 -->
<template>
  <!-- 意图：仅显示活动用户 -->
  <li v-for="user in users" v-if="user.isActive" :key="user.id">
    {{ user.name }}
  </li>
</template>
```

```html
<!-- 错误：有条件地隐藏整个列表 -->
<template>
  <li v-for="user in users" v-if="shouldShowList" :key="user.id">
    {{ user.name }}
  </li>
</template>
```

```html
<!-- 错误：Vue 3 优先级问题 -->
<template>
  <!-- 在 Vue 3 中，v-if 首先运行，所以 'user' 未定义！-->
  <li v-for="user in users" v-if="user.isActive" :key="user.id">
    {{ user.name }}
  </li>
  <!-- 错误：无法读取未定义的属性 'isActive' -->
</template>
```

**正确：**
```html
<!-- 正确：使用计算属性过滤 -->
<template>
  <li v-for="user in activeUsers" :key="user.id">
    {{ user.name }}
  </li>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps(['users'])

const activeUsers = computed(() =>
  props.users.filter(user => user.isActive)
)
</script>
```

```html
<!-- 正确：用 <template v-if> 包裹以进行条件列表 -->
<template>
  <template v-if="shouldShowList">
    <li v-for="user in users" :key="user.id">
      {{ user.name }}
    </li>
  </template>
</template>
```

```html
<!-- 正确：循环内的 v-if（每项条件）-->
<template>
  <ul>
    <template v-for="user in users" :key="user.id">
      <li v-if="user.isActive">
        {{ user.name }}
      </li>
    </template>
  </ul>
</template>
```

## Vue 2 vs Vue 3 优先级更改

```javascript
// Vue 2：v-for 首先评估
// <li v-for="user in users" v-if="user.isActive">
// 等同于：users.forEach(user => { if (user.isActive) render(user) })

// Vue 3：v-if 首先评估
// <li v-for="user in users" v-if="user.isActive">
// 等同于：if (user.isActive) users.forEach(user => render(user))
// 问题：当 v-if 运行时 'user' 还不存在！
```

## 为什么计算属性更好

```javascript
// 通过计算过滤的好处：
// 1. 清晰的关注点分离（逻辑 vs 模板）
// 2. 缓存 - 仅在依赖项更改时重新计算
// 3. 可重用 - 可以在组件的其他地方使用
// 4. 可测试 - 可以对过滤逻辑进行单元测试
// 5. 关于意图没有歧义

const activeUsers = computed(() =>
  users.value.filter(u => u.isActive)
)

// 可以添加更复杂的过滤
const filteredUsers = computed(() =>
  users.value
    .filter(u => u.isActive)
    .filter(u => u.role === selectedRole.value)
    .sort((a, b) => a.name.localeCompare(b.name))
)
```

## 参考
- [Vue.js 风格指南 - 避免在 v-for 中使用 v-if](https://vuejs.org/style-guide/rules-essential.html#avoid-v-if-with-v-for)
- [Vue 3 迁移指南 - v-if vs v-for 优先级](https://v3-migration.vuejs.org/breaking-changes/v-if-v-for)
- [ESLint Plugin Vue - no-use-v-if-with-v-for](https://eslint.vuejs.org/rules/no-use-v-if-with-v-for)
