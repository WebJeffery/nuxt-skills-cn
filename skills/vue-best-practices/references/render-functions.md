---
title: 渲染函数最佳实践
impact: MEDIUM
impactDescription: 渲染函数提供强大的编程能力,但误用会导致难以维护的代码
type: best-practice
tags: [vue3, render-functions, h, jsx, vnodes]
---

# 渲染函数最佳实践

**影响: MEDIUM** - 渲染函数提供强大的编程能力,但误用会导致难以维护的代码。仅在模板不够用时使用它们。

## 任务列表

- 仅在模板不够用时使用渲染函数
- 使用 `h` 函数创建虚拟节点
- 对复杂渲染逻辑使用 JSX
- 正确处理事件和属性
- 为渲染函数提供 TypeScript 类型
- 避免在渲染函数中进行副作用操作

## 使用 h 函数

**正确:**
```javascript
import { h } from 'vue'

export default {
  render() {
    return h('div', { class: 'container' }, [
      h('h1', '标题'),
      h('p', '内容')
    ])
  }
}
```

## 使用 JSX

**正确:**
```javascript
export default {
  render() {
    return (
      <div class="container">
        <h1>标题</h1>
        <p>内容</p>
      </div>
    )
  }
}
```

## 处理事件

**正确:**
```javascript
export default {
  render() {
    return h('button', {
      onClick: () => {
        console.log('点击')
      }
    }, '点击我')
  }
}
```

## 处理插槽

**正确:**
```javascript
export default {
  render() {
    return h('div', [
      this.$slots.default?.(),
      this.$slots.header?.()
    ])
  }
}
```

## TypeScript 支持

```typescript
import { h, VNode } from 'vue'

interface Props {
  title: string
  content: string
}

export default defineComponent({
  props: {
    title: String,
    content: String
  },
  render(props): VNode {
    return h('div', [
      h('h1', props.title),
      h('p', props.content)
    ])
  }
})
```

## 最佳实践

1. **何时使用渲染函数:**
   - 需要动态组件生成
   - 需要复杂的条件渲染逻辑
   - 需要优化性能的特定场景

2. **避免在渲染函数中进行副作用:**
   - 不要在渲染函数中修改状态
   - 不要在渲染函数中发起网络请求
   - 不要在渲染函数中添加事件监听器

3. **保持渲染函数简洁:**
   - 将复杂逻辑提取到单独的函数中
   - 使用 composables 处理副作用
   - 使用计算属性处理派生状态

4. **考虑替代方案:**
   - 对于大多数场景,模板更清晰
   - 对于动态组件,使用 `<component :is="">`
   - 对于复杂逻辑,使用 composables
