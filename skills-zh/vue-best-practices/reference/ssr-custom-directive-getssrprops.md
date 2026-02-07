---
title: 在 SSR 中为自定义指令实现 getSSRProps
impact: MEDIUM
impactDescription: 没有 SSR 处理的自定义指令会导致水合不匹配或功能缺失
type: best-practice
tags: [vue3, ssr, directives, custom-directive, server-side-rendering, nuxt]
---

# 在 SSR 中为自定义指令实现 getSSRProps

**影响：中** - 自定义指令只能在客户端访问 DOM。在 SSR 期间，指令的 `mounted` 和 `updated` 钩子永远不会运行。如果你的指令设置属性或修改元素，你必须实现 `getSSRProps` 以返回用于服务器渲染的等效属性。

如果没有 `getSSRProps`，服务器渲染的 HTML 将不会包含指令的效果，当客户端应用指令时会导致水合不匹配。

## 任务清单

- [ ] 向修改元素属性的指令添加 `getSSRProps` 钩子
- [ ] 返回一个包含要在服务器上渲染的 HTML 属性的对象
- [ ] 在 SSR 和仅客户端上下文中测试指令行为
- [ ] 对于复杂的 SSR 情况，考虑使用组件而不是指令

**不正确 - 仅客户端指令：**
```javascript
// 错误：没有 SSR 处理 - 服务器上缺少指令效果
const vTooltip = {
  mounted(el, binding) {
    el.setAttribute('data-tooltip', binding.value)
    el.setAttribute('aria-label', binding.value)
    el.classList.add('has-tooltip')
  }
}
```

服务器渲染：
```html
<!-- 缺少 data-tooltip、aria-label 和 has-tooltip 类 -->
<button>Hover me</button>
```

水合后的客户端：
```html
<!-- 指令应用，但导致不匹配 -->
<button data-tooltip="Help text" aria-label="Help text" class="has-tooltip">
  Hover me
</button>
```

**正确 - 带有 getSSRProps：**
```javascript
// 正确：SSR 兼容的指令
const vTooltip = {
  // 客户端实现
  mounted(el, binding) {
    el.setAttribute('data-tooltip', binding.value)
    el.setAttribute('aria-label', binding.value)
    el.classList.add('has-tooltip')
  },

  // SSR 实现 - 返回要渲染的属性
  getSSRProps(binding) {
    return {
      'data-tooltip': binding.value,
      'aria-label': binding.value,
      class: 'has-tooltip'
    }
  }
}
```

服务器现在渲染：
```html
<button data-tooltip="Help text" aria-label="Help text" class="has-tooltip">
  Hover me
</button>
```

## 完整的 SSR 指令示例

```javascript
// directives/vFocus.js
export const vFocus = {
  // 客户端：实际聚焦元素
  mounted(el, binding) {
    if (binding.value !== false) {
      el.focus()
    }
  },

  // SSR：添加 autofocus 属性，以便浏览器在加载时聚焦
  getSSRProps(binding) {
    if (binding.value !== false) {
      return { autofocus: true }
    }
    return {}
  }
}
```

```vue
<template>
  <input v-focus type="text" placeholder="Auto-focused input" />
</template>

<script setup>
import { vFocus } from '@/directives/vFocus'
</script>
```

## 带有动态 ID 的指令

```javascript
// 正确：生成一致的 ID
const vId = {
  mounted(el, binding) {
    el.id = binding.value || `el-${binding.instance?.$.uid}`
  },

  getSSRProps(binding, vnode) {
    // 使用相同的 ID 生成逻辑
    return {
      id: binding.value || `el-${vnode.component?.uid || 'ssr'}`
    }
  }
}
```

## 处理复杂指令

对于不仅仅设置属性的指令，请考虑：

```javascript
// 仅在客户端有意义的指令（例如，拖放）
const vDraggable = {
  mounted(el, binding) {
    // 复杂的客户端逻辑
    initDragAndDrop(el, binding.value)
  },

  unmounted(el) {
    destroyDragAndDrop(el)
  },

  // SSR：仅将元素标记为可拖动，用于样式/语义
  getSSRProps(binding) {
    return {
      draggable: 'true',
      'data-draggable': '',
      role: 'listitem'
    }
  }
}
```

## 无法具有 SSR 等效项的指令

某些指令没有有意义的服务器端表示：

```javascript
// 跟踪鼠标位置的指令 - 没有 SSR 等效项
const vMousePosition = {
  mounted(el, binding) {
    el.addEventListener('mousemove', (e) => {
      binding.value?.(e.clientX, e.clientY)
    })
  },

  // 在服务器上渲染没有意义
  getSSRProps() {
    return {} // 空对象 - 没有属性
  }
}
```

## Nuxt.js 指令注册

```javascript
// plugins/directives.ts
export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.vueApp.directive('tooltip', {
    mounted(el, binding) {
      el.setAttribute('data-tooltip', binding.value)
    },
    getSSRProps(binding) {
      return { 'data-tooltip': binding.value }
    }
  })
})
```

## 测试 SSR 指令

```javascript
import { renderToString } from 'vue/server-renderer'
import { createSSRApp, h } from 'vue'
import { vTooltip } from './directives/vTooltip'

test('vTooltip 在 SSR 期间渲染属性', async () => {
  const app = createSSRApp({
    directives: { tooltip: vTooltip },
    template: '<button v-tooltip="\'Help text\'">Click</button>'
  })

  const html = await renderToString(app)

  expect(html).toContain('data-tooltip="Help text"')
  expect(html).toContain('aria-label="Help text"')
})
```

## 参考
- [Vue.js 自定义指令 - SSR](https://vuejs.org/guide/reusability/custom-directives.html#custom-directive-api)
- [Vue.js SSR - 自定义指令](https://vuejs.org/guide/scaling-up/ssr.html#custom-directives)
