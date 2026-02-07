---
title: 在 Vite 中使用 import.meta.glob 进行动态组件注册
impact: MEDIUM
impactDescription: Webpack 的 require.context 在 Vite 项目中不起作用
type: gotcha
tags: [vue3, component-registration, vite, dynamic-import, migration, webpack]
---

# 在 Vite 中使用 import.meta.glob 进行动态组件注册

**影响: 中等** - 当从 Webpack 迁移到 Vite 或启动新的 Vite 项目时，用于动态注册组件的 `require.context` 模式将不起作用。Vite 使用 `import.meta.glob` 代替。使用错误的方法会导致构建错误或运行时失败。

## 任务清单

- [ ] 在 Vite 项目中将 `require.context` 替换为 `import.meta.glob`
- [ ] 从 Vue CLI 迁移到 Vite 时更新组件注册模式
- [ ] 需要时使用 `{ eager: true }` 进行同步加载
- [ ] 使用 `defineAsyncComponent` 适当处理异步组件

**错误（Webpack 模式 - 在 Vite 中不起作用）:**
```javascript
// main.js - 在 Vite 中错误
import { createApp } from 'vue'
import App from './App.vue'

const app = createApp(App)

// 这个 Webpack 特定的 API 在 Vite 中不存在
const requireComponent = require.context(
  './components/base',
  false,
  /Base[A-Z]\w+\.vue$/
)

requireComponent.keys().forEach(fileName => {
  const componentConfig = requireComponent(fileName)
  const componentName = fileName
    .split('/')
    .pop()
    .replace(/\.\w+$/, '')

  app.component(componentName, componentConfig.default || componentConfig)
})

app.mount('#app')
```

**正确（Vite 模式）:**
```javascript
// main.js - 在 Vite 中正确
import { createApp } from 'vue'
import App from './App.vue'

const app = createApp(App)

// Vite 的 glob 导入 - eager 加载用于同步注册
const modules = import.meta.glob('./components/base/Base*.vue', { eager: true })

for (const path in modules) {
  // 从路径中提取组件名称: './components/base/BaseButton.vue' -> 'BaseButton'
  const componentName = path.split('/').pop().replace('.vue', '')
  app.component(componentName, modules[path].default)
}

app.mount('#app')
```

## 使用异步组件进行懒加载

```javascript
// main.js - 懒加载变体
import { createApp, defineAsyncComponent } from 'vue'
import App from './App.vue'

const app = createApp(App)

// 没有 { eager: true }，返回返回 Promise 的函数
const modules = import.meta.glob('./components/base/Base*.vue')

for (const path in modules) {
  const componentName = path.split('/').pop().replace('.vue', '')
  // 用 defineAsyncComponent 包装进行懒加载
  app.component(componentName, defineAsyncComponent(modules[path]))
}

app.mount('#app')
```

## Glob 模式示例

```javascript
// 目录中的所有 .vue 文件（非递归）
import.meta.glob('./components/*.vue', { eager: true })

// 递归的所有 .vue 文件
import.meta.glob('./components/**/*.vue', { eager: true })

// 特定命名模式
import.meta.glob('./components/Base*.vue', { eager: true })

// 多个模式
import.meta.glob([
  './components/Base*.vue',
  './components/App*.vue'
], { eager: true })

// 排除模式
import.meta.glob('./components/**/*.vue', {
  eager: true,
  ignore: ['**/*.test.vue', '**/*.spec.vue']
})
```

## TypeScript 支持

```typescript
// main.ts - 带有适当的类型
import { createApp, Component } from 'vue'
import App from './App.vue'

const app = createApp(App)

const modules = import.meta.glob<{ default: Component }>(
  './components/base/Base*.vue',
  { eager: true }
)

for (const path in modules) {
  const componentName = path.split('/').pop()!.replace('.vue', '')
  app.component(componentName, modules[path].default)
}

app.mount('#app')
```

## 迁移清单（Webpack 到 Vite）

| Webpack | Vite |
|---------|------|
| `require.context(dir, recursive, regex)` | `import.meta.glob(pattern, options)` |
| 默认同步 | 使用 `{ eager: true }` 进行同步 |
| `.keys()` 返回数组 | 返回以路径为键的对象 |
| 直接返回模块 | 通过 `.default` 访问 ES 模块 |

## 参考
- [Vite - Glob 导入](https://vitejs.dev/guide/features.html#glob-import)
- [Vue.js 组件注册](https://vuejs.org/guide/components/registration.html)
