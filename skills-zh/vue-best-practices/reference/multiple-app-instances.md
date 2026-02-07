---
title: 使用多个应用实例进行部分页面控制
impact: MEDIUM
impactDescription: 仅控制部分时将单个应用挂载到整个页面会浪费资源并使 SSR 复杂化
type: efficiency
tags: [vue3, createApp, mount, ssr, progressive-enhancement, architecture]
---

# 使用多个应用实例进行部分页面控制

**影响: 中等** - 当 Vue 仅控制页面的特定部分（在服务器渲染的 HTML 或渐进增强中常见）时，将单个大型应用实例挂载到整个页面是低效的，并且可能使服务器端渲染集成复杂化。

Vue 的 `createApp` API 明确支持同一页面上的多个应用实例。每个实例都有自己的配置和全局资产的隔离作用域，使这种模式安全且推荐。

## 任务清单

- [ ] 评估 Vue 是控制整个页面还是仅控制特定部分
- [ ] 对于部分控制，为每个 Vue 管理的部分创建单独的应用实例
- [ ] 每个实例可以有自己的插件、组件和配置
- [ ] 如果实例需要通信，考虑通过外部存储共享状态

**不正确：**
```javascript
// 服务器渲染的页面，Vue 仅需要几个交互式小部件
// 错误：挂载到整个页面

// index.html (服务器渲染)
// <body id="app">
//   <header>...</header>        <!-- 静态 -->
//   <nav>...</nav>              <!-- 静态 -->
//   <div class="widget-1">...</div>  <!-- 需要 Vue -->
//   <main>...</main>            <!-- 静态 -->
//   <div class="widget-2">...</div>  <!-- 需要 Vue -->
//   <footer>...</footer>        <!-- 静态 -->
// </body>

import { createApp } from 'vue'
import BigApp from './BigApp.vue'

// 错误：Vue 现在控制整个页面，包括静态内容
createApp(BigApp).mount('#app')
```

**正确：**
```javascript
// 正确：将单独的实例挂载到特定元素

import { createApp } from 'vue'
import SearchWidget from './widgets/SearchWidget.vue'
import CartWidget from './widgets/CartWidget.vue'
import { createPinia } from 'pinia'

// 用于跨小部件状态的共享存储
const pinia = createPinia()

// 小部件 1: 搜索功能
const searchApp = createApp(SearchWidget)
searchApp.use(pinia)
searchApp.mount('.widget-search')

// 小部件 2: 购物车
const cartApp = createApp(CartWidget)
cartApp.use(pinia)  // 同一个 Pinia 实例 = 共享状态
cartApp.mount('.widget-cart')

// 页面的其余部分保持服务器渲染的静态 HTML
```

## 多个实例的好处

```javascript
// 1. 每个部分的隔离配置
const adminApp = createApp(AdminPanel)
adminApp.config.errorHandler = adminErrorHandler
adminApp.use(adminOnlyPlugin)
adminApp.mount('#admin-panel')

const publicApp = createApp(PublicWidget)
publicApp.config.errorHandler = publicErrorHandler
// 不同的插件、组件、配置
publicApp.mount('#public-widget')

// 2. 独立的生命周期
// 可以独立卸载/重新挂载部分
const app1 = createApp(Widget1).mount('#widget-1')
const app2 = createApp(Widget2).mount('#widget-2')

// 稍后，仅卸载一个小部件
// Vue 2 中使用 app1.$destroy()，Vue 3 中对应用实例使用 app.unmount()
```

## 实例之间的共享状态

```javascript
// 选项 1: 共享 Pinia 存储
const pinia = createPinia()

createApp(App1).use(pinia).mount('#app1')
createApp(App2).use(pinia).mount('#app2')
// 两个应用共享相同的 Pinia 存储

// 选项 2: 共享响应式状态模块
// sharedState.js
import { reactive } from 'vue'
export const sharedState = reactive({
  user: null,
  cart: []
})

// 两个应用都导入并直接使用 sharedState
```

## 参考
- [Vue.js - 多个应用实例](https://vuejs.org/guide/essentials/application.html#multiple-application-instances)
- [Vue.js 应用 API](https://vuejs.org/api/application.html)
