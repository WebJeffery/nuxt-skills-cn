---
title: 为生产应用使用 Vue Router 库
impact: LOW
impactDescription: 简单哈希路由缺乏生产 SPA 所需的基本功能;Vue Router 提供导航守卫、懒加载和适当的历史管理
type: best-practice
tags: [vue3, vue-router, spa, production, architecture]
---

# 为生产应用使用 Vue Router 库

**影响: LOW** - 虽然你可以使用哈希更改和动态组件实现基本路由,但对于任何生产单页应用,应使用官方 Vue Router 库。它提供了手动实现既繁琐又容易出错的基本功能,如导航守卫、嵌套路由、懒加载和适当的浏览器历史集成。

## 任务清单

- [ ] 为生产 SPA 安装 Vue Router
- [ ] 仅将简单路由用于学习或微型原型
- [ ] 利用内置功能:守卫、懒加载、元字段
- [ ] 考虑基于路由的状态和数据加载模式

## 何时简单路由可接受

```vue
<!-- 仅用于:学习、原型或 2-3 页的微应用 -->
<script setup>
import { ref, computed } from 'vue'
import Home from './Home.vue'
import About from './About.vue'

const routes = { '/': Home, '/about': About }
const currentPath = ref(window.location.hash.slice(1) || '/')

window.addEventListener('hashchange', () => {
  currentPath.value = window.location.hash.slice(1) || '/'
})

const currentView = computed(() => routes[currentPath.value])
</script>

<template>
  <nav>
    <a href="#/">Home</a>
    <a href="#/about">About</a>
  </nav>
  <component :is="currentView" />
</template>
```

## 为什么生产环境需要 Vue Router

### 你必须手动实现的功能

| 功能 | 简单路由 | Vue Router |
|---------|---------------|------------|
| 导航守卫 | 手动,容易出错 | 内置,可组合 |
| 嵌套路由 | 实现复杂 | 原生支持 |
| 路由参数 | 手动解析 | 自动提取 |
| 懒加载 | 使用动态导入 DIY | 内置代码分割 |
| 历史模式(干净的 URL) | 需要服务器配置 + 手动 | 内置 |
| 滚动行为 | 手动 | 可配置 |
| 路由过渡 | DIY | 与 Transition 集成 |
| 活动链接样式 | 手动类切换 | `router-link-active` 类 |
| 编程式导航 | `location.hash = ...` | `router.push()`, `router.replace()` |
| 路由元字段 | 不适用 | 内置 |

## 使用 Vue Router 的生产设置

```javascript
// router/index.js
import { createRouter, createWebHistory } from 'vue-router'

const routes = [
  {
    path: '/',
    name: 'Home',
    component: () => import('@/views/Home.vue'),  // 懒加载
    meta: { requiresAuth: false }
  },
  {
    path: '/dashboard',
    name: 'Dashboard',
    component: () => import('@/views/Dashboard.vue'),
    meta: { requiresAuth: true },
    children: [
      {
        path: 'settings',
        name: 'Settings',
        component: () => import('@/views/Settings.vue')
      }
    ]
  },
  {
    path: '/users/:id',
    name: 'UserProfile',
    component: () => import('@/views/UserProfile.vue'),
    props: true  // 将参数作为 props 传递
  },
  {
    path: '/:pathMatch(.*)*',
    name: 'NotFound',
    component: () => import('@/views/NotFound.vue')
  }
]

const router = createRouter({
  history: createWebHistory(),
  routes,
  scrollBehavior(to, from, savedPosition) {
    return savedPosition || { top: 0 }
  }
})

// 全局导航守卫
router.beforeEach((to, from) => {
  if (to.meta.requiresAuth && !isAuthenticated()) {
    return { name: 'Login', query: { redirect: to.fullPath } }
  }
})

export default router
```

```javascript
// main.js
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'

createApp(App)
  .use(router)
  .mount('#app')
```

```vue
<!-- App.vue -->
<template>
  <nav>
    <router-link to="/">Home</router-link>
    <router-link to="/dashboard">Dashboard</router-link>
  </nav>

  <router-view v-slot="{ Component }">
    <transition name="fade" mode="out-in">
      <component :is="Component" />
    </transition>
  </router-view>
</template>
```

## 现代 Vue Router 功能(2025+)

```javascript
// 数据加载 API(Vue Router 4.2+)
const routes = [
  {
    path: '/users/:id',
    component: UserProfile,
    // 在路由级别加载数据
    loader: async (route) => {
      return { user: await fetchUser(route.params.id) }
    }
  }
]

// 视图转换 API 集成
const router = createRouter({
  // 启用原生浏览器视图转换
  // 需要浏览器支持(Chrome 111+)
})
```

## 关键点

1. **为任何超越原型的应用使用 Vue Router** - 这些功能是必不可少的
2. **简单路由用于学习** - 理解概念,然后使用库
3. **懒加载对包大小至关重要** - Vue Router 使其变得微不足道
4. **导航守卫防止安全问题** - 手动实现很难正确
5. **历史模式需要 Vue Router** - 干净的 URL 需要适当处理
6. **新功能不断推出** - 数据加载 API,视图转换

## 参考
- [Vue.js 路由指南](https://vuejs.org/guide/scaling-up/routing.html)
- [Vue Router 文档](https://router.vuejs.org/)
- [Vue Router 入门](https://router.vuejs.org/guide/)
