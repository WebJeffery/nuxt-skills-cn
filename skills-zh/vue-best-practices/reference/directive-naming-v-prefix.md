---
title: 本地指令使用 v- 前缀命名约定
impact: LOW
impactDescription: 正确的命名使 script setup 能够自动识别指令
type: best-practice
tags: [vue3, directives, naming, script-setup, conventions]
---

# 本地指令使用 v- 前缀命名约定

**影响：低** - 在 `<script setup>` 中，任何以 `v` 前缀开头的 camelCase 变量都可以自动用作自定义指令。此约定无需显式配置即可实现无缝的本地指令注册。

遵循此命名模式可确保 Vue 正确识别并注册您的本地指令。

## 任务清单

- [ ] 使用 `v` 前缀和 camelCase 命名本地指令变量（例如 `vFocus`、`vHighlight`）
- [ ] 在模板中使用不带 `v` 前缀小写的指令（例如 `v-focus`、`v-highlight`）
- [ ] 对于多词指令，在 script 中使用 camelCase，在模板中使用 kebab-case

**错误示例：**
```vue
<script setup>
// 错误：没有 v 前缀 - 不会被识别为指令
const focus = {
  mounted: (el) => el.focus()
}

// 错误：错误的大小写
const VFocus = {
  mounted: (el) => el.focus()
}

// 错误：在 script 中使用 kebab-case
const 'v-focus' = {  // 语法错误
  mounted: (el) => el.focus()
}
</script>

<template>
  <!-- 这些不会工作 -->
  <input focus />
  <input v-Focus />
</template>
```

**正确示例：**
```vue
<script setup>
// 正确：v 前缀和 camelCase
const vFocus = {
  mounted: (el) => el.focus()
}

const vHighlight = {
  mounted: (el) => {
    el.classList.add('is-highlight')
  }
}

// 正确：多词指令
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

// 正确：带 v 前缀的函数简写
const vColor = (el, binding) => {
  el.style.color = binding.value
}
</script>

<template>
  <!-- 在模板中使用 kebab-case -->
  <input v-focus />
  <p v-highlight>高亮文本</p>
  <div v-click-outside="closeMenu">下拉菜单</div>
  <span v-color="'red'">彩色文本</span>
</template>
```

## 模板大小写规则

在模板中，指令应使用 kebab-case：

```vue
<script setup>
const vMyLongDirectiveName = (el) => { /* ... */ }
const vAutoFocusInput = (el) => el.focus()
const vLazyLoadImage = { /* ... */ }
</script>

<template>
  <!-- script 中的 camelCase -> 模板中的 kebab-case -->
  <div v-my-long-directive-name></div>
  <input v-auto-focus-input />
  <img v-lazy-load-image />
</template>
```

## Options API 注册

没有 `<script setup>` 时，指令需要显式注册：

```javascript
// 使用 Options API 进行本地注册
export default {
  directives: {
    // 键是指令名称（不带 v- 前缀）
    focus: {
      mounted: (el) => el.focus()
    },
    highlight: {
      mounted: (el) => el.classList.add('is-highlight')
    },
    // 多词使用 camelCase 键
    clickOutside: {
      mounted(el, binding) { /* ... */ },
      unmounted(el) { /* ... */ }
    }
  }
}
```

## 全局注册

对于全局指令，在 app 实例上注册：

```javascript
// main.js
import { createApp } from 'vue'
import App from './App.vue'

const app = createApp(App)

// 全局指令 - 名称不带 v- 前缀
app.directive('focus', {
  mounted: (el) => el.focus()
})

// 多词指令
app.directive('click-outside', {
  mounted(el, binding) { /* ... */ },
  unmounted(el) { /* ... */ }
})

// 函数简写
app.directive('color', (el, binding) => {
  el.style.color = binding.value
})

app.mount('#app')
```

## 导入指令

导入指令时，重命名以添加 v 前缀：

```javascript
// directives/focus.js
export const focus = {
  mounted: (el) => el.focus()
}

// 在组件中
<script setup>
import { focus as vFocus } from '@/directives/focus'
// 现在可以在模板中用作 v-focus
</script>

// 或已导出带 v 前缀
// directives/focus.js
export const vFocus = {
  mounted: (el) => el.focus()
}

// 在组件中
<script setup>
import { vFocus } from '@/directives/focus'
// 可直接用作 v-focus
</script>
```

## 参考
- [Vue.js 自定义指令 - 简介](https://vuejs.org/guide/reusability/custom-directives#introduction)
