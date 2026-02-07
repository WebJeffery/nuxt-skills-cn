---
title: 优先使用内置指令而非自定义指令
impact: MEDIUM
impactDescription: 自定义指令比内置指令效率低且不支持 SSR
type: best-practice
tags: [vue3, directives, performance, ssr, best-practices]
---

# 优先使用内置指令而非自定义指令

**影响：中等** - 自定义指令仅当需要通过直接 DOM 操作才能实现所需功能时才应使用。尽可能使用内置指令（如 `v-bind`、`v-show`、`v-if` 和 `v-on`）进行声明式模板编写，因为它们更高效且支持服务器渲染。

在创建自定义指令之前，请考虑是否可以使用 Vue 的内置响应式和模板功能实现相同的结果。

## 任务清单

- [ ] 在创建自定义指令之前，检查内置指令是否能解决问题
- [ ] 考虑组合式函数是否更合适
- [ ] 对于 SSR 应用程序，评估指令是否能在服务器上工作
- [ ] 仅对无法通过声明式方式完成的低级 DOM 操作使用自定义指令

**错误示例：**
```vue
<template>
  <!-- 错误：用自定义指令做 v-show 能做的事 -->
  <div v-visibility="isVisible">内容</div>

  <!-- 错误：用自定义指令做 class 绑定 -->
  <div v-add-class="{ active: isActive }">内容</div>

  <!-- 错误：用自定义指令做 style 绑定 -->
  <div v-set-color="textColor">内容</div>
</template>

<script setup>
import { ref } from 'vue'

const isVisible = ref(true)
const isActive = ref(false)
const textColor = ref('blue')

// 不必要的自定义指令
const vVisibility = {
  mounted(el, binding) {
    el.style.display = binding.value ? '' : 'none'
  },
  updated(el, binding) {
    el.style.display = binding.value ? '' : 'none'
  }
}

const vAddClass = {
  mounted(el, binding) {
    Object.entries(binding.value).forEach(([cls, active]) => {
      el.classList.toggle(cls, active)
    })
  },
  updated(el, binding) {
    Object.entries(binding.value).forEach(([cls, active]) => {
      el.classList.toggle(cls, active)
    })
  }
}

const vSetColor = (el, binding) => {
  el.style.color = binding.value
}
</script>
```

**正确示例：**
```vue
<template>
  <!-- 正确：使用内置 v-show -->
  <div v-show="isVisible">内容</div>

  <!-- 正确：使用内置 class 绑定 -->
  <div :class="{ active: isActive }">内容</div>

  <!-- 正确：使用内置 style 绑定 -->
  <div :style="{ color: textColor }">内容</div>
</template>

<script setup>
import { ref } from 'vue'

const isVisible = ref(true)
const isActive = ref(false)
const textColor = ref('blue')
// 不需要自定义指令！
</script>
```

## 何时适合使用自定义指令

当您需要以下功能时，自定义指令是合适的：

### 1. 直接访问 DOM API
```javascript
// 好：焦点管理需要 DOM API
const vFocus = {
  mounted(el) {
    el.focus()
  }
}

// 用法：在动态插入时工作，而不仅仅是页面加载时
// <input v-focus />
```

### 2. 第三方库集成
```javascript
// 好：与外部库集成
const vTippy = {
  mounted(el, binding) {
    el._tippy = tippy(el, {
      content: binding.value,
      ...binding.modifiers
    })
  },
  updated(el, binding) {
    el._tippy?.setContent(binding.value)
  },
  unmounted(el) {
    el._tippy?.destroy()
  }
}
```

### 3. Vue 范围之外的事件处理
```javascript
// 好：Vue 不提供的全局事件
const vClickOutside = {
  mounted(el, binding) {
    el._clickOutside = (e) => {
      if (!el.contains(e.target)) {
        binding.value(e)
      }
    }
    document.addEventListener('click', el._clickOutside)
  },
  unmounted(el) {
    document.removeEventListener('click', el._clickOutside)
  }
}
```

### 4. Intersection/Mutation/Resize 观察器
```javascript
// 好：IntersectionObserver 需要 DOM API
const vLazyLoad = {
  mounted(el, binding) {
    el._observer = new IntersectionObserver(([entry]) => {
      if (entry.isIntersecting) {
        el.src = binding.value
        el._observer.disconnect()
      }
    })
    el._observer.observe(el)
  },
  unmounted(el) {
    el._observer?.disconnect()
  }
}
```

## 考虑使用组合式函数代替

对于复杂逻辑，组合式函数可能比指令更好：

```javascript
// 组合式函数方法 - 更灵活且可测试
import { onMounted, onUnmounted, ref } from 'vue'

export function useClickOutside(elementRef, callback) {
  const handler = (e) => {
    if (elementRef.value && !elementRef.value.contains(e.target)) {
      callback(e)
    }
  }

  onMounted(() => document.addEventListener('click', handler))
  onUnmounted(() => document.removeEventListener('click', handler))
}

// 在组件中使用
const dropdownRef = ref(null)
useClickOutside(dropdownRef, () => {
  isOpen.value = false
})
```

## SSR 考虑事项

自定义指令不在服务器上运行，这可能导致水合问题：

```javascript
// 问题：此指令修改 DOM，导致水合不匹配
const vHydrationProblem = {
  mounted(el) {
    el.textContent = '仅客户端文本'
  }
}

// 解决方案：使用内置指令或确保服务器/客户端匹配
// 或显式处理水合：
const vSafeForSSR = {
  mounted(el, binding) {
    // 仅添加行为，不修改内容
    el.addEventListener('click', binding.value)
  },
  unmounted(el, binding) {
    el.removeEventListener('click', binding.value)
  }
}
```

## 参考
- [Vue.js 自定义指令 - 简介](https://vuejs.org/guide/reusability/custom-directives#introduction)
- [Vue.js 组合式函数](https://vuejs.org/guide/reusability/composables.html)
