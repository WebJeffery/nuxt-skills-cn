---
title: 对无状态展示性 UI 使用函数式组件
impact: LOW
impactDescription: 函数式组件减少简单无状态组件的开销
type: best-practice
tags: [vue3, render-function, functional-component, performance]
---

# 对无状态展示性 UI 使用函数式组件

**影响：低** - 函数式组件是返回 vnodes 的普通函数，没有组件实例开销。它们非常适合不需要内部状态、生命周期钩子或响应式的简单展示组件。

Vue 3 中的函数式组件定义为接收 `props` 和 `context` 对象（包含 `slots`、`emit` 和 `attrs`）的普通函数。它们比有状态组件的开销略小。

## 任务清单

- [ ] 对无状态 UI（如图标、徽章或简单布局）考虑使用函数式组件
- [ ] 在函数上定义 `props` 和 `emits` 作为静态属性
- [ ] 从上下文参数访问插槽、emit 和 attrs
- [ ] 手动传播 attrs 时使用 `inheritAttrs: false`

**基本函数式组件：**
```javascript
import { h } from 'vue'

// 简单的函数式组件
function MyButton(props, { slots }) {
  return h('button', { class: 'btn' }, slots.default?.())
}

// 使用 props 定义
MyButton.props = ['disabled', 'variant']

export default MyButton
```

**使用 TypeScript：**
```typescript
import { h } from 'vue'
import type { FunctionalComponent } from 'vue'

interface Props {
  message: string
  type?: 'info' | 'warning' | 'error'
}

const Alert: FunctionalComponent<Props> = (props, { slots }) => {
  return h('div', {
    class: ['alert', `alert-${props.type || 'info'}`]
  }, [
    h('span', props.message),
    slots.default?.()
  ])
}

Alert.props = {
  message: { type: String, required: true },
  type: String
}

export default Alert
```

**使用 Emits：**
```typescript
import { h } from 'vue'
import type { FunctionalComponent } from 'vue'

interface Props {
  value: string
}

interface Emits {
  (e: 'update', value: string): void
  (e: 'clear'): void
}

const SearchInput: FunctionalComponent<Props, Emits> = (props, { emit }) => {
  return h('div', { class: 'search-input' }, [
    h('input', {
      value: props.value,
      onInput: (e: Event) => emit('update', (e.target as HTMLInputElement).value)
    }),
    h('button', { onClick: () => emit('clear') }, '清除')
  ])
}

SearchInput.props = ['value']
SearchInput.emits = ['update', 'clear']

export default SearchInput
```

**禁用属性继承：**
```javascript
import { h } from 'vue'

function CustomInput(props, { attrs }) {
  return h('div', { class: 'input-wrapper' }, [
    h('input', { ...attrs, class: 'custom-input' })
  ])
}

CustomInput.inheritAttrs = false

export default CustomInput
```

## 何时使用函数式组件

**良好的候选者：**
- 图标和徽章
- 简单的包装器/布局组件
- 纯展示组件
- 仅格式化或显示数据的组件

```javascript
// 图标组件 - 良好用例
function Icon(props) {
  return h('svg', {
    class: `icon icon-${props.name}`,
    width: props.size || 24,
    height: props.size || 24
  }, [
    h('use', { href: `#icon-${props.name}` })
  ])
}
Icon.props = ['name', 'size']

// 徽章组件 - 良好用例
function Badge(props, { slots }) {
  return h('span', {
    class: ['badge', `badge-${props.variant || 'default'}`]
  }, slots.default?.())
}
Badge.props = ['variant']
```

**不推荐用于：**
- 需要响应式状态的组件（使用 `ref`、`reactive`）
- 需要生命周期钩子的组件
- 具有复杂逻辑的组件
- 需要使用 composables 的组件

## 函数式与有状态比较

```javascript
// 函数式 - 无响应式，无生命周期
function StaticLabel(props) {
  return h('span', { class: 'label' }, props.text)
}
StaticLabel.props = ['text']

// 有状态 - 当你需要状态或生命周期时
export default {
  setup(props) {
    const count = ref(0)

    onMounted(() => {
      console.log('已挂载！')
    })

    return () => h('button', {
      onClick: () => count.value++
    }, count.value)
  }
}
```

## 参考
- [Vue.js 函数式组件](https://vuejs.org/guide/extras/render-function.html#functional-components)
