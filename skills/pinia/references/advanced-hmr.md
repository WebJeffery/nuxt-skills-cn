---
name: hot-module-replacement
description: 启用 HMR 以在开发期间保留 store 状态
---

# 热模块替换 (HMR)

Pinia 支持 HMR，可以在不重新加载页面的情况下编辑 store，保留现有状态。

## 设置

在每个 store 定义后添加此代码片段:

```ts
import { defineStore, acceptHMRUpdate } from 'pinia'

export const useAuth = defineStore('auth', {
  // store 选项...
})

if (import.meta.hot) {
  import.meta.hot.accept(acceptHMRUpdate(useAuth, import.meta.hot))
}
```

## Setup Store 示例

```ts
import { defineStore, acceptHMRUpdate } from 'pinia'

export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)
  const increment = () => count.value++
  return { count, increment }
})

if (import.meta.hot) {
  import.meta.hot.accept(acceptHMRUpdate(useCounterStore, import.meta.hot))
}
```

## 打包器支持

- **Vite:** 通过 `import.meta.hot` 官方支持
- **Webpack:** 使用 `import.meta.webpackHot`
- 任何实现 `import.meta.hot` 规范的打包器都应该工作

## Nuxt

使用 `@pinia/nuxt` 时，`acceptHMRUpdate` 被自动导入，但你仍然需要手动添加 HMR 代码片段。

## 优势

- 编辑 store 逻辑而不丢失状态
- 动态添加/删除 state、actions 和 getters
- 更快的开发迭代

<!--
源引用:
- https://pinia.vuejs.org/cookbook/hot-module-replacement.html
-->
