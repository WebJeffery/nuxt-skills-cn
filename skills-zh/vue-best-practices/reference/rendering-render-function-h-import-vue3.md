---
title: 在 Vue 3 渲染函数中全局导入 h
impact: HIGH
impactDescription: Vue 3 需要显式导入 h；使用 Vue 2 模式会导致运行时错误
type: gotcha
tags: [vue3, render-function, migration, h, vnode, breaking-change]
---

# 在 Vue 3 渲染函数中全局导入 h

**影响：高** - 在 Vue 2 中，`h` 函数（createElement）作为参数传递给渲染函数。在 Vue 3 中，必须从 'vue' 显式导入 `h`。使用 Vue 2 模式会导致运行时错误。

## 任务清单

- [ ] 在使用渲染函数的文件顶部从 'vue' 导入 `h`
- [ ] 从渲染函数签名中移除 `h` 参数
- [ ] 从 Vue 2 迁移时更新所有渲染函数

**错误（Vue 2 模式 - 在 Vue 3 中中断）：**
```js
// 错误：Vue 2 模式 - 在 Vue 3 中不传递 h 作为参数
export default {
  render(h) {  // h 在 Vue 3 中未定义！
    return h('div', [
      h('span', 'Hello')
    ])
  }
}

// 错误：使用来自 Vue 2 的 createElement 别名
export default {
  render(createElement) {  // 也未定义
    return createElement('div', 'Hello')
  }
}
```

**正确（Vue 3 模式）：**
```js
// 正确：从 vue 导入 h
import { h } from 'vue'

export default {
  render() {
    return h('div', [
      h('span', 'Hello')
    ])
  }
}
```

## 使用组合式 API

```js
import { h, ref } from 'vue'

export default {
  setup() {
    const count = ref(0)

    // 从 setup 返回渲染函数
    return () => h('div', [
      h('button', { onClick: () => count.value++ }, `计数: ${count.value}`)
    ])
  }
}
```

## 使用 script setup（不推荐）

```vue
<script setup>
import { h, ref } from 'vue'

const count = ref(0)

// 不能从 script setup 返回渲染函数
// 必须使用单独的 render 选项或模板
</script>

<!-- script setup 通常使用模板，而不是渲染函数 -->
<template>
  <div>
    <button @click="count++">计数: {{ count }}</button>
  </div>
</template>
```

如果你需要带有 `<script setup>` 的渲染函数，请使用 `render` 选项：

```vue
<script>
import { h, ref } from 'vue'

export default {
  setup() {
    const count = ref(0)
    return () => h('button', { onClick: () => count.value++ }, count.value)
  }
}
</script>
```

## 组件解析更改

在 Vue 3 中，你还必须显式解析组件：

**错误：**
```js
// Vue 2：可以对注册的组件使用字符串名称
render(h) {
  return h('my-component', { props: { value: 1 } })
}
```

**正确：**
```js
import { h, resolveComponent } from 'vue'

export default {
  render() {
    // 必须按名称解析组件
    const MyComponent = resolveComponent('my-component')
    return h(MyComponent, { value: 1 })
  }
}

// 或直接导入组件（首选）
import { h } from 'vue'
import MyComponent from './MyComponent.vue'

export default {
  render() {
    return h(MyComponent, { value: 1 })
  }
}
```

## 为什么更改了

Vue 3 的 `h` 可以全局导入以：
1. 启用 tree-shaking（未使用的功能可以被移除）
2. 支持更好的 TypeScript 推断
3. 允许在组件上下文之外使用

## 参考
- [Vue 3 迁移指南 - 渲染函数 API](https://v3-migration.vuejs.org/breaking-changes/render-function-api.html)
- [Vue.js 渲染函数和 JSX](https://vuejs.org/guide/extras/render-function.html)
