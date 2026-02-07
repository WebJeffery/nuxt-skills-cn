---
title: 不要依赖内部 VNode 属性
impact: MEDIUM
impactDescription: 使用未记录的 vnode 属性会导致代码在 Vue 更新时中断
type: gotcha
tags: [vue3, render-function, vnode, internal-api]
---

# 不要依赖内部 VNode 属性

**影响：中** - `VNode` 接口包含许多由 Vue 渲染系统使用的内部属性。依赖除记录的公共属性之外的任何属性，当 Vue 的内部实现更改时，会导致代码中断。

仅使用记录的 vnode 属性：`type`、`props`、`children` 和 `key`。所有其他属性都是内部实现细节，可能会在 Vue 版本之间不经通知地更改。

## 任务清单

- [ ] 仅访问记录的 vnode 属性：`type`、`props`、`children`、`key`
- [ ] 永远不要访问 `el`、`component`、`shapeFlag`、`patchFlag` 等属性
- [ ] 如果需要 DOM 元素访问，请改用模板 refs
- [ ] 将 vnodes 视为用于渲染的不透明数据结构，而不是用于检查

**错误：**
```javascript
import { h } from 'vue'

export default {
  setup(props, { slots }) {
    return () => {
      const slotContent = slots.default?.()

      // 错误：访问内部属性
      if (slotContent?.[0]?.el) {
        // el 是内部属性
        console.log(slotContent[0].el.tagName)
      }

      // 错误：使用 shapeFlag 内部属性
      if (slotContent?.[0]?.shapeFlag & 1) {
        // 这是内部实现
      }

      return h('div', slotContent)
    }
  }
}
```

```javascript
// 错误：通过 vnode 检查组件实例
const vnode = h(MyComponent)
console.log(vnode.component) // 内部属性
console.log(vnode.appContext) // 内部属性
```

**正确：**
```javascript
import { h } from 'vue'

export default {
  setup(props, { slots }) {
    return () => {
      const slotContent = slots.default?.()

      // 正确：仅使用记录的属性
      if (slotContent?.[0]) {
        const vnode = slotContent[0]
        console.log(vnode.type)     // 安全：元素类型或组件
        console.log(vnode.props)    // 安全：props 对象
        console.log(vnode.children) // 安全：children
        console.log(vnode.key)      // 安全：key prop
      }

      return h('div', slotContent)
    }
  }
}
```

```javascript
import { h, ref, onMounted } from 'vue'

export default {
  setup() {
    // 正确：使用模板 refs 进行 DOM 访问
    const divRef = ref(null)

    onMounted(() => {
      // 访问 DOM 元素的安全方式
      console.log(divRef.value.tagName)
    })

    return () => h('div', { ref: divRef }, 'Content')
  }
}
```

## 记录的 VNode 属性

| 属性 | 类型 | 描述 |
|----------|------|-------------|
| `type` | `string \| Component` | 元素标签名称或组件定义 |
| `props` | `object \| null` | 传递给 vnode 的 props |
| `children` | `any` | 子 vnodes、文本或 slots |
| `key` | `string \| number \| null` | 用于列表渲染的 key |

## 安全的 VNode 检查模式

```javascript
import { h, isVNode } from 'vue'

export default {
  setup(props, { slots }) {
    return () => {
      const children = slots.default?.() || []

      // 安全：检查某物是否为 vnode
      children.forEach(child => {
        if (isVNode(child)) {
          // 安全：检查 vnode 类型
          if (typeof child.type === 'string') {
            console.log('Element:', child.type)
          } else if (typeof child.type === 'object') {
            console.log('Component:', child.type.name)
          }

          // 安全：读取 props
          if (child.props?.class) {
            console.log('Has class:', child.props.class)
          }
        }
      })

      return h('div', children)
    }
  }
}
```

## 为什么这很重要

Vue 的内部 vnode 结构可能会因以下原因而更改：
- 性能优化
- 新功能实现
- 错误修复
- Tree-shaking 改进

依赖内部属性的代码在升级 Vue 版本时会静默中断或抛出错误。记录的属性是 Vue 公共 API 的一部分，保证保持稳定。

## 参考
- [Vue.js Render Function APIs](https://vuejs.org/api/render-function.html)
- [Vue.js Render Functions - The Virtual DOM](https://vuejs.org/guide/extras/render-function.html#the-virtual-dom)
