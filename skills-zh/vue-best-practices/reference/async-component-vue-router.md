# 不要将 defineAsyncComponent 与 Vue Router 一起使用

## 规则

配置 Vue Router 路由组件时，切勿使用 `defineAsyncComponent`。Vue Router 有自己的使用动态导入的延迟加载机制。

## 为什么这很重要

Vue Router 的延迟加载是专门为路由级代码分割设计的。对路由使用 `defineAsyncComponent` 会增加不必要的开销，并可能导致导航守卫、加载状态和路由过渡出现意外行为。

## 坏代码

```javascript
import { defineAsyncComponent } from 'vue'
import { createRouter, createWebHistory } from 'vue-router'

const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: '/dashboard',
      // 错误: 不要在这里使用 defineAsyncComponent
      component: defineAsyncComponent(() =>
        import('./views/Dashboard.vue')
      )
    },
    {
      path: '/profile',
      // 错误: 这也不会按预期工作
      component: defineAsyncComponent({
        loader: () => import('./views/Profile.vue'),
        loadingComponent: LoadingSpinner
      })
    }
  ]
})
```

## 好代码

```javascript
import { createRouter, createWebHistory } from 'vue-router'

const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: '/dashboard',
      // 正确: 直接使用动态导入
      component: () => import('./views/Dashboard.vue')
    },
    {
      path: '/profile',
      // 正确: 带有 import 的简单箭头函数
      component: () => import('./views/Profile.vue')
    }
  ]
})
```

## 使用 Vue Router 处理加载状态

对于路由级加载状态，使用 Vue Router 的导航守卫或全局加载指示器：

```vue
<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const isLoading = ref(false)

router.beforeEach(() => {
  isLoading.value = true
})

router.afterEach(() => {
  isLoading.value = false
})
</script>

<template>
  <LoadingBar v-if="isLoading" />
  <RouterView />
</template>
```

## 何时使用 defineAsyncComponent

对以下情况使用 `defineAsyncComponent`：
- 在页面内有条件加载的组件
- 不总是需要的重型组件
- 按需加载的模态对话框或面板

对以下情况使用 Vue Router 的延迟加载：
- 路由级组件（视图/页面）
- 在路由定义中配置的任何组件

## 关键点

1. Vue Router 和 `defineAsyncComponent` 是单独的延迟加载机制
2. 路由组件应使用直接动态导入: `() => import('./View.vue')`
3. 使用导航守卫进行路由级加载指示器
4. `defineAsyncComponent` 用于页面内的组件级延迟加载

## 参考

- [Vue Router 延迟加载路由](https://router.vuejs.org/guide/advanced/lazy-loading.html)
- [Vue.js 异步组件文档](https://vuejs.org/guide/components/async)
