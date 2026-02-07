---
title: 对简单指令使用函数简写
impact: LOW
impactDescription: 函数简写减少了具有相同 mounted/updated 行为的指令的样板代码
type: best-practice
tags: [vue3, directives, shorthand, code-style]
---

# 对简单指令使用函数简写

**影响：低** - 自定义指令通常对 `mounted` 和 `updated` 具有相同的行为，不需要其他钩子。在这种情况下，您可以将指令定义为函数而不是对象，从而减少样板代码并提高可读性。

该函数将同时为 `mounted` 和 `updated` 生命周期钩子调用。

## 任务清单

- [ ] 当 mounted 和 updated 行为相同时使用函数简写
- [ ] 当需要 beforeMount、beforeUpdate 或 unmounted 钩子时使用对象语法
- [ ] 当 mounted 和 updated 具有不同的逻辑时使用对象语法

**冗长（当不需要时）：**
```javascript
// 冗长：当行为相同时使用完整对象
const vColor = {
  mounted(el, binding) {
    el.style.color = binding.value
  },
  updated(el, binding) {
    el.style.color = binding.value  // 与 mounted 相同
  }
}

const vHighlight = {
  mounted(el, binding) {
    el.style.backgroundColor = binding.value || 'yellow'
  },
  updated(el, binding) {
    el.style.backgroundColor = binding.value || 'yellow'  // 重复
  }
}

// 全局注册 - 冗长
app.directive('pin', {
  mounted(el, binding) {
    el.style.position = 'fixed'
    el.style.top = binding.value + 'px'
  },
  updated(el, binding) {
    el.style.position = 'fixed'
    el.style.top = binding.value + 'px'
  }
})
```

**简洁（函数简写）：**
```javascript
// 简洁：函数简写
const vColor = (el, binding) => {
  el.style.color = binding.value
}

const vHighlight = (el, binding) => {
  el.style.backgroundColor = binding.value || 'yellow'
}

// 全局注册 - 简洁
app.directive('pin', (el, binding) => {
  el.style.position = 'fixed'
  el.style.top = binding.value + 'px'
})
```

## 使用 script setup

```vue
<script setup>
// 本地指令的函数简写
const vFocus = (el) => {
  el.focus()
}

const vColor = (el, binding) => {
  el.style.color = binding.value
}

const vPin = (el, binding) => {
  el.style.position = binding.modifiers.absolute ? 'absolute' : 'fixed'
  const position = binding.arg || 'top'
  el.style[position] = binding.value + 'px'
}
</script>

<template>
  <input v-focus />
  <p v-color="'red'">彩色文本</p>
  <div v-pin:left.absolute="100">定位元素</div>
</template>
```

## 何时使用对象语法

在以下情况下使用完整对象语法：

### 1. 您需要清理（unmounted 钩子）
```javascript
// 需要对象语法进行清理
const vClickOutside = {
  mounted(el, binding) {
    el._handler = (e) => {
      if (!el.contains(e.target)) binding.value(e)
    }
    document.addEventListener('click', el._handler)
  },
  unmounted(el) {
    document.removeEventListener('click', el._handler)
  }
}
```

### 2. mounted 和 updated 的逻辑不同
```javascript
// 需要对象语法实现不同的行为
const vLazyLoad = {
  mounted(el, binding) {
    // 初始设置 - 创建观察器
    el._observer = new IntersectionObserver(entries => {
      if (entries[0].isIntersecting) {
        el.src = binding.value
        el._observer.disconnect()
      }
    })
    el._observer.observe(el)
  },
  updated(el, binding, vnode, prevVnode) {
    // 仅在值实际更改时更新
    if (binding.value !== binding.oldValue) {
      el.src = binding.value
    }
  },
  unmounted(el) {
    el._observer?.disconnect()
  }
}
```

### 3. 您需要 beforeMount 或 beforeUpdate
```javascript
// 需要对象语法实现早期生命周期钩子
const vAnimate = {
  beforeMount(el) {
    el.style.opacity = '0'
  },
  mounted(el) {
    requestAnimationFrame(() => {
      el.style.transition = 'opacity 0.3s'
      el.style.opacity = '1'
    })
  },
  beforeUpdate(el) {
    el.style.opacity = '0.5'
  },
  updated(el) {
    el.style.opacity = '1'
  }
}
```

## 使用函数简写的对象字面量值

函数简写与对象字面量值配合良好：

```javascript
const vDemo = (el, binding) => {
  console.log(binding.value.color)  // => "white"
  console.log(binding.value.text)   // => "hello!"

  el.style.color = binding.value.color
  el.textContent = binding.value.text
}
```

```vue
<template>
  <div v-demo="{ color: 'white', text: 'hello!' }"></div>
</template>
```

## 参考
- [Vue.js 自定义指令 - 函数简写](https://vuejs.org/guide/reusability/custom-directives#function-shorthand)
