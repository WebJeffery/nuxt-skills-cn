---
title: 在渲染函数中使用 withDirectives 处理自定义指令
impact: LOW
impactDescription: 自定义指令需要使用 withDirectives 辅助函数进行特殊处理
type: best-practice
tags: [vue3, render-function, directives, custom-directive]
---

# 在渲染函数中使用 withDirectives 处理自定义指令

**影响：低** - 要在渲染函数中将自定义指令应用于 vnodes，请使用 `withDirectives` 辅助函数。尝试通过 props 或其他方式应用指令将不起作用。

`withDirectives` 函数包装 vnode 并应用具有其值、参数和修饰符的指令。

## 任务清单

- [ ] 使用自定义指令时从 'vue' 导入 `withDirectives`
- [ ] 导入或定义指令对象
- [ ] 将指令作为数组传递：`[directive, value, argument, modifiers]`
- [ ] 可以一次应用多个指令

**错误：**
```javascript
import { h } from 'vue'

const vFocus = { mounted: (el) => el.focus() }

export default {
  setup() {
    return () => {
      // 错误：指令不能作为 props 工作
      return h('input', {
        'v-focus': true  // 这不起作用
      })
    }
  }
}
```

**正确：**
```javascript
import { h, withDirectives } from 'vue'

// 自定义指令
const vFocus = {
  mounted: (el) => el.focus()
}

export default {
  setup() {
    return () => {
      // 正确：使用 withDirectives
      return withDirectives(
        h('input'),
        [[vFocus]]  // 指令元组的数组
      )
    }
  }
}
```

## 带有值、参数和修饰符的指令

指令元组格式：`[directive, value, argument, modifiers]`

```javascript
import { h, withDirectives, resolveDirective } from 'vue'

// 指令定义
// 在模板中的用法：<div v-pin:top.animate="200">
const vPin = {
  mounted(el, binding) {
    console.log(binding.value)    // 200
    console.log(binding.arg)      // 'top'
    console.log(binding.modifiers) // { animate: true }

    el.style.position = 'fixed'
    el.style[binding.arg] = binding.value + 'px'
  }
}

export default {
  setup() {
    return () => withDirectives(
      h('div', 'Pinned content'),
      [
        // [directive, value, argument, modifiers]
        [vPin, 200, 'top', { animate: true }]
      ]
    )
  }
}
```

## 多个指令

```javascript
import { h, withDirectives } from 'vue'

const vFocus = { mounted: (el) => el.focus() }
const vTooltip = {
  mounted(el, { value }) {
    el.title = value
  }
}

export default {
  setup() {
    return () => withDirectives(
      h('input', { placeholder: 'Enter text' }),
      [
        [vFocus],
        [vTooltip, 'This is a tooltip']
      ]
    )
  }
}
```

## 使用注册的指令

对于全局或本地注册的指令，使用 `resolveDirective`：

```javascript
import { h, withDirectives, resolveDirective } from 'vue'

// 假设 v-focus 和 v-tooltip 全局注册
export default {
  setup() {
    return () => {
      const focus = resolveDirective('focus')
      const tooltip = resolveDirective('tooltip')

      return withDirectives(
        h('input'),
        [
          [focus],
          [tooltip, 'Helpful tip']
        ]
      )
    }
  }
}
```

## 实际示例：点击外部指令

```javascript
import { h, withDirectives, ref } from 'vue'

const vClickOutside = {
  mounted(el, binding) {
    el._clickOutside = (event) => {
      if (!el.contains(event.target)) {
        binding.value(event)
      }
    }
    document.addEventListener('click', el._clickOutside)
  },
  unmounted(el) {
    document.removeEventListener('click', el._clickOutside)
  }
}

export default {
  setup() {
    const isOpen = ref(true)
    const closeDropdown = () => { isOpen.value = false }

    return () => isOpen.value
      ? withDirectives(
          h('div', { class: 'dropdown' }, 'Dropdown content'),
          [[vClickOutside, closeDropdown]]
        )
      : null
  }
}
```

## JSX 中的指令

JSX 中的指令也需要 `withDirectives`：

```jsx
import { withDirectives } from 'vue'

const vFocus = { mounted: (el) => el.focus() }

export default {
  setup() {
    return () => withDirectives(
      <input placeholder="Search..." />,
      [[vFocus]]
    )
  }
}
```

## 参考
- [Vue.js Render Functions - Custom Directives](https://vuejs.org/guide/extras/render-function.html#custom-directives)
- [Vue.js Custom Directives](https://vuejs.org/guide/reusability/custom-directives.html)
