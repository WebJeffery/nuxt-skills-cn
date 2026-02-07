---
title: 在渲染函数中渲染列表时始终包含 key 属性
impact: HIGH
impactDescription: 缺少 key 导致低效的重新渲染和动态列表中的状态错误
type: best-practice
tags: [vue3, render-function, v-for, performance, key]
---

# 在渲染函数中渲染列表时始终包含 key 属性

**影响：高** - 使用 `h()` 或 JSX 渲染列表时省略 `key` 属性会导致 Vue 使用低效的"就地补丁"策略，当列表项具有内部状态或重新排序时，会导致性能问题和状态错误。

在使用 `.map()` 在渲染函数中渲染列表时，始终在每个项目上包含唯一的 `key` 属性。这是在模板中使用 `v-for` 和 `:key` 的渲染函数等效项。

## 任务清单

- [ ] 在渲染函数中映射数组时始终提供 `key` 属性
- [ ] 使用唯一、稳定的标识符（如 `id`）作为键，而不是数组索引
- [ ] 确保键是原始值（字符串或数字）
- [ ] 永远不要在同一列表中的不同项目上使用相同的键

**错误：**
```javascript
import { h } from 'vue'

export default {
  setup() {
    const items = ref([
      { id: 1, name: '苹果' },
      { id: 2, name: '香蕉' }
    ])

    return () => h('ul',
      // 错误：没有键 - 导致低效的补丁
      items.value.map(item =>
        h('li', item.name)
      ))
  }
}
```

```jsx
// 错误：当列表可以重新排序时使用数组索引作为键
export default {
  setup() {
    const todos = ref([...])

    return () => (
      <ul>
        {todos.value.map((todo, index) => (
          <TodoItem
            key={index}  // 错误：列表重新排序时索引会更改
            todo={todo}
          />
        ))}
      </ul>
    )
  }
}
```

**正确：**
```javascript
import { h } from 'vue'

export default {
  setup() {
    const items = ref([
      { id: 1, name: '苹果' },
      { id: 2, name: '香蕉' }
    ])

    return () => h('ul',
      // 正确：唯一 id 作为键
      items.value.map(item =>
        h('li', { key: item.id }, item.name)
      ))
  }
}
```

```jsx
// 正确：使用稳定的唯一标识符作为键
export default {
  setup() {
    const todos = ref([
      { id: 'a1', text: '学习 Vue' },
      { id: 'b2', text: '构建应用' }
    ])

    return () => (
      <ul>
        {todos.value.map(todo => (
          <TodoItem
            key={todo.id} // 正确：稳定的唯一标识符
            todo={todo}
          />
        ))}
      </ul>
    )
  }
}
```

```javascript
import { h } from 'vue'

export default {
  setup() {
    const users = ref([])

    return () => h('div', [
      h('h2', '用户列表'),
      h('ul', users.value.map(user =>
        h('li', { key: user.email }, [ // email 是唯一的
          h('span', user.name),
          h('span', ` (${user.email})`)
        ])
      ))
    ])
  }
}
```

## 何时索引键是可接受的

使用数组索引作为键仅在以下情况下是可接受的：
1. 列表是静态的，永远不会重新排序
2. 项目永远不会从中间插入或删除
3. 项目没有内部组件状态

```javascript
// 索引在这里是可以的：永不更改的静态列表
const staticLabels = ['姓名', '电子邮件', '电话']

return () => h('tr',
  staticLabels.map((label, index) =>
    h('th', { key: index }, label)
  )
)
```

## 为什么键很重要

没有键，Vue 使用"就地补丁"策略：
1. 它就地重用 DOM 元素
2. 更新元素内容以匹配新数据
3. 当组件具有内部状态或过渡时，这会破坏

使用正确的键：
1. Vue 跟踪每个项目的身份
2. 元素被正确移动、创建或销毁
3. 组件状态在重新排序期间保留

## 参考
- [Vue.js 渲染函数 - v-for](https://vuejs.org/guide/extras/render-function.html#v-for)
