---
title: 在渲染函数中将插槽作为函数传递，而不是直接子元素
impact: HIGH
impactDescription: 错误传递插槽内容导致插槽不渲染或被视为属性
type: gotcha
tags: [vue3, render-function, slots, children, vnode]
---

# 在渲染函数中将插槽作为函数传递，而不是直接子元素

**影响：高** - 使用 `h()` 创建组件 vnode 时，子元素必须作为插槽函数传递，而不是作为直接子元素。直接传递子元素可能导致它们被解释为属性或无法渲染。

## 任务清单

- [ ] 将插槽内容作为函数传递：`{ default: () => [...] }`
- [ ] 当仅传递插槽时使用 `null` 作为属性以避免误解
- [ ] 对于仅默认插槽，可以直接传递单个函数
- [ ] 对于命名插槽，使用具有插槽函数属性的对象

**错误：**
```js
import { h } from 'vue'
import MyComponent from './MyComponent.vue'

// 错误：子元素数组可能被误解
h(MyComponent, [
  h('span', '插槽内容')  // 可能不会按预期渲染
])

// 错误：命名插槽作为直接属性
h(MyComponent, {
  header: h('h1', '标题'),  // 这是一个属性，不是插槽！
  default: h('p', '内容')  // 这也是一个属性
})
```

**正确：**
```js
import { h } from 'vue'
import MyComponent from './MyComponent.vue'

// 正确：默认插槽作为函数
h(MyComponent, null, {
  default: () => h('span', '插槽内容')
})

// 正确：单个默认插槽简写
h(MyComponent, null, () => h('span', '插槽内容'))

// 正确：命名插槽作为函数
h(MyComponent, null, {
  header: () => h('h1', '标题'),
  default: () => h('p', '内容'),
  footer: () => [
    h('span', '页脚项 1'),
    h('span', '页脚项 2')
  ]
})

// 正确：带有属性和插槽
h(MyComponent, { size: 'large' }, {
  default: () => '按钮文本'
})
```

## 为什么是函数？

Vue 3 中的插槽是函数，用于延迟求值：

```js
// 插槽由子组件在需要时调用
// 这使得：
// 1. 作用域插槽（传回数据）
// 2. 条件渲染（如果未使用则不调用插槽）
// 3. 正确的响应式跟踪

h(MyList, { items }, {
  // 作用域插槽 - 从子组件接收数据
  item: ({ item, index }) => h('li', `${index}: ${item.name}`)
})
```

## null 属性陷阱

当仅传递插槽时，始终使用 `null` 作为属性：

```js
// 错误：插槽对象被解释为属性！
h(MyComponent, {
  default: () => '你好'
})
// MyComponent 接收：props.default = () => '你好'

// 正确：null 表示"没有属性，下一个参数是插槽"
h(MyComponent, null, {
  default: () => '你好'
})
// MyComponent 正确接收插槽
```

## 从父组件转发插槽

```js
export default {
  setup(props, { slots }) {
    return () => h(ChildComponent, null, {
      // 从父组件转发所有插槽
      ...slots,

      // 或转发特定插槽
      default: slots.default,
      header: slots.header
    })
  }
}
```

## 渲染函数中的作用域插槽

```js
// 父组件：通过插槽属性从子组件接收数据
h(DataTable, { data: items }, {
  row: (slotProps) => h('tr', [
    h('td', slotProps.item.name),
    h('td', slotProps.item.value)
  ])
})

// 子组件（DataTable）：使用数据调用插槽
export default {
  props: ['data'],
  setup(props, { slots }) {
    return () => h('table', [
      h('tbody',
        props.data.map(item =>
          // 将数据传递给插槽函数
          slots.row?.({ item })
        )
      )
    ])
  }
}
```

## 常见模式

```js
// 包装组件转发插槽
h('div', { class: 'wrapper' }, [
  h(InnerComponent, null, slots)
])

// 条件插槽渲染
h('div', [
  slots.header?.(),  // 可选链接 - 仅在提供插槽时渲染
  h('main', slots.default?.()),
  slots.footer?.()
])

// 带有后备内容的插槽
h('div', [
  slots.default?.() ?? h('p', '未提供插槽时的默认内容')
])
```

## 参考
- [Vue.js 渲染函数 - 传递插槽](https://vuejs.org/guide/extras/render-function.html#passing-slots)
- [Vue.js 渲染函数 - 子元素](https://vuejs.org/guide/extras/render-function.html#children)
