---
title: mount() 返回组件实例，而不是应用实例
impact: MEDIUM
impactDescription: 使用 mount() 返回值进行应用配置会静默失败
type: capability
tags: [vue3, createApp, mount, api]
---

# mount() 返回组件实例，而不是应用实例

**影响: 中等** - `.mount()` 方法返回根组件实例，而不是应用实例。尝试在 mount() 之后链接应用配置方法将失败或产生意外行为。

这是一个微妙的 API 细节，会捕获那些假设 mount() 返回应用以继续链接的开发人员。

## 任务清单

- [ ] 永远不要在 mount() 之后链接应用配置方法
- [ ] 如果需要两个实例，分别存储它们
- [ ] 使用组件实例访问根组件状态或方法
- [ ] 使用应用实例进行配置、插件和全局注册

**不正确：**
```javascript
import { createApp } from 'vue'
import App from './App.vue'

// 错误：假设 mount 返回应用实例
const app = createApp(App).mount('#app')

// 这会失败！app 实际上是根组件实例
app.use(router)  // TypeError: app.use is not a function
app.config.errorHandler = fn  // app.config is undefined
```

```javascript
// 错误：试图在一行中保存两者
const { app, component } = createApp(App).mount('#app')  // 不能这样工作
```

**正确：**
```javascript
import { createApp } from 'vue'
import App from './App.vue'

// 分别存储应用实例
const app = createApp(App)

// 配置应用
app.use(router)
app.config.errorHandler = (err) => console.error(err)

// 如果需要，存储组件实例
const rootComponent = app.mount('#app')

// 现在你可以访问两者：
// - app: 应用实例（用于配置、插件）
// - rootComponent: 根组件实例（用于状态、方法）
```

```javascript
// 如果你只需要配置和挂载应用（最常见的情况）：
createApp(App)
  .use(router)
  .use(pinia)
  .mount('#app')  // 返回值（组件实例）被丢弃 - 这没关系
```

## 当你需要根组件实例时

```javascript
const app = createApp(App)
const vm = app.mount('#app')

// 访问根组件暴露的状态/方法
console.log(vm.someExposedProperty)
vm.someExposedMethod()

// 在 Vue 3 中使用 <script setup>，使用 defineExpose 来暴露：
// <script setup>
// import { ref } from 'vue'
// const count = ref(0)
// defineExpose({ count })
// </script>
```

## 参考
- [Vue.js - 挂载应用](https://vuejs.org/guide/essentials/application.html#mounting-the-app)
- [Vue.js 应用 API - mount()](https://vuejs.org/api/application.html#app-mount)
