---
title: 在渲染函数中使用 withModifiers 处理事件修饰符
impact: MEDIUM
impactDescription: 手动修饰符实现容易出错；使用 withModifiers 辅助函数
type: best-practice
tags: [vue3, render-function, events, modifiers]
---

# 在渲染函数中使用 withModifiers 处理事件修饰符

**影响：中** - 使用渲染函数时，`.stop`、`.prevent` 或 `.self` 等事件修饰符需要特殊处理。使用 Vue 的 `withModifiers` 辅助函数，而不是手动实现修饰符逻辑，这容易出错。

在模板中，你可以使用像 `@click.stop.prevent` 这样的修饰符。在渲染函数中，你必须对简单修饰符使用 camelCase 拼接，或对更复杂的修饰符使用 `withModifiers` 辅助函数。

## 任务清单

- [ ] 对 capture、once 和 passive 修饰符使用 camelCase 拼接
- [ ] 对 stop、prevent、self 和 key 修饰符使用 `withModifiers()` 辅助函数
- [ ] 需要时从 'vue' 导入 `withModifiers`
- [ ] 通过将它们作为数组传递来组合多个修饰符

**错误：**
```javascript
import { h } from 'vue'

export default {
  setup() {
    const handleClick = (e) => {
      // 错误：手动修饰符实现容易出错
      e.stopPropagation()
      e.preventDefault()
      // ... 实际处理程序逻辑
    }

    return () => h('button', { onClick: handleClick }, 'Click')
  }
}
```

```javascript
// 错误：在复杂场景中忘记处理修饰符
const handleDivClick = (e) => {
  // 预期：仅在点击 div 本身时触发，而不是子元素
  // 这个手动检查很容易出错
  if (e.target !== e.currentTarget) return
  // ...
}
```

**正确：**
```javascript
import { h, withModifiers } from 'vue'

export default {
  setup() {
    const handleClick = () => {
      console.log('clicked!')
    }

    return () => h('button',
      {
        // 正确：对 stop 和 prevent 使用 withModifiers
        onClick: withModifiers(handleClick, ['stop', 'prevent'])
      },
      'Click'
    )
  }
}
```

## CamelCase 修饰符（不需要辅助函数）

对于 `capture`、`once` 和 `passive` 修饰符，使用 camelCase 拼接：

```javascript
import { h } from 'vue'

export default {
  setup() {
    const handler = () => console.log('event!')

    return () => h('div', {
      // @click.capture -> onClickCapture
      onClickCapture: handler,

      // @keyup.once -> onKeyupOnce
      onKeyupOnce: handler,

      // @scroll.passive -> onScrollPassive
      onScrollPassive: handler,

      // @mouseover.once.capture -> onMouseoverOnceCapture
      onMouseoverOnceCapture: handler
    })
  }
}
```

## withModifiers 示例

```javascript
import { h, withModifiers } from 'vue'

export default {
  setup() {
    const handleClick = () => console.log('clicked')
    const handleSubmit = () => console.log('submitted')

    return () => h('div', [
      // .stop 修饰符
      h('button', {
        onClick: withModifiers(handleClick, ['stop'])
      }, 'Stop Propagation'),

      // .prevent 修饰符
      h('form', {
        onSubmit: withModifiers(handleSubmit, ['prevent'])
      }, [
        h('button', { type: 'submit' }, 'Submit')
      ]),

      // .self 修饰符 - 仅当 event.target 是元素本身时触发
      h('div', {
        onClick: withModifiers(handleClick, ['self']),
        style: { padding: '20px', background: '#eee' }
      }, [
        h('button', 'Click me (won\'t trigger parent)')
      ]),

      // 多个修饰符
      h('a', {
        href: '/path',
        onClick: withModifiers(handleClick, ['stop', 'prevent'])
      }, 'Link')
    ])
  }
}
```

## 键修饰符

对于键盘事件，使用 `withKeys` 进行键修饰符：

```javascript
import { h, withKeys } from 'vue'

export default {
  setup() {
    const handleEnter = () => console.log('Enter pressed')
    const handleEscape = () => console.log('Escape pressed')

    return () => h('input', {
      // @keyup.enter
      onKeyup: withKeys(handleEnter, ['enter']),

      // 多个键
      onKeydown: withKeys(handleEscape, ['escape', 'esc'])
    })
  }
}
```

## JSX 等效形式

```jsx
import { withModifiers, withKeys } from 'vue'

export default {
  setup() {
    const handleClick = () => console.log('clicked')

    return () => (
      <div>
        <button onClick={withModifiers(handleClick, ['stop'])}>
          Stop
        </button>

        <div onClick={withModifiers(handleClick, ['self'])}>
          <span>Child content</span>
        </div>

        <input onKeyup={withKeys(() => {}, ['enter'])} />
      </div>
    )
  }
}
```

## 参考
- [Vue.js Render Functions - Event Modifiers](https://vuejs.org/guide/extras/render-function.html#event-modifiers)
