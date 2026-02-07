---
title: 对组件名称使用 PascalCase
impact: LOW
impactDescription: 提高代码清晰度和 IDE 支持，但两种情况都有效
type: best-practice
tags: [vue3, component-registration, naming-conventions, pascalcase, ide-support]
---

# 对组件名称使用 PascalCase

**影响：低** - Vue 在模板中支持 PascalCase (`<MyComponent>`) 和 kebab-case (`<my-component>`)，但推荐使用 PascalCase。它提供更好的 IDE 支持，清楚地区分 Vue 组件和原生 HTML 元素，并避免与 Web 组件（自定义元素）混淆。

## 任务清单

- [ ] 以 PascalCase 命名组件文件（例如 `UserProfile.vue`）
- [ ] 在模板中引用组件时使用 PascalCase
- [ ] 仅在需要时使用 kebab-case（在 DOM 模板中）
- [ ] 在整个代码库中保持一致

**不太理想：**
```vue
<script setup>
import userProfile from './user-profile.vue'
</script>

<template>
  <!-- 有效但难以与 HTML 元素区分 -->
  <user-profile :user="currentUser" />
  <header>原生 HTML</header>
  <footer>原生 HTML</footer>
</template>
```

**推荐：**
```vue
<script setup>
import UserProfile from './UserProfile.vue'
</script>

<template>
  <!-- 清晰的视觉区分：组件 vs HTML 元素 -->
  <UserProfile :user="currentUser" />
  <header>原生 HTML</header>
  <footer>原生 HTML</footer>
</template>
```

## 为什么使用 PascalCase？

### 1. 视觉区分
```vue
<template>
  <!-- 立即清楚什么是组件 vs 原生 HTML -->
  <PageHeader />           <!-- 组件 -->
  <header>...</header>     <!-- 原生 HTML -->

  <NavigationMenu />       <!-- 组件 -->
  <nav>...</nav>           <!-- 原生 HTML -->

  <UserAvatar />           <!-- 组件 -->
  <img src="..." />        <!-- 原生 HTML -->
</template>
```

### 2. IDE 自动完成
PascalCase 名称是有效的 JavaScript 标识符，提供更好的 IDE 支持：
- 自动导入建议
- 转到定义
- 重构工具

### 3. 避免 Web 组件混淆
Web 组件（自定义元素）需要带连字符的 kebab-case。对 Vue 组件使用 PascalCase 可以避免任何混淆：
```vue
<template>
  <!-- Vue 组件 -->
  <MyButton @click="handle" />

  <!-- Web 组件（自定义元素） -->
  <my-custom-element></my-custom-element>
</template>
```

## 例外：在 DOM 模板中

当使用在 DOM 模板（没有构建步骤的 HTML 文件）时，你必须使用 kebab-case，因为 HTML 是不区分大小写的：

```html
<!-- index.html - 在 DOM 模板中 -->
<div id="app">
  <!-- PascalCase 在 HTML 文件中不起作用 -->
  <!-- <UserProfile></UserProfile> --> <!-- 错误 -->

  <!-- 必须使用 kebab-case -->
  <user-profile :user="currentUser"></user-profile>
</div>
```

## Vue 的自动解析

Vue 自动将 PascalCase 组件解析为两种大小写：
```vue
<script setup>
import MyComponent from './MyComponent.vue'
</script>

<template>
  <!-- 两者都有效，但 PascalCase 是首选 -->
  <MyComponent />
  <my-component />
</template>
```

## 参考
- [Vue.js 组件注册 - 组件名称大小写](https://vuejs.org/guide/components/registration.html#component-name-casing)
