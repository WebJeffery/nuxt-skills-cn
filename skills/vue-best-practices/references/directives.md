---
title: 指令最佳实践
impact: MEDIUM
impactDescription: 自定义指令很强大但容易误用;遵循模式可防止泄漏、无效使用和不清晰的抽象
type: best-practice
tags: [vue3, directives, custom-directives, composition, typescript]
---

# 指令最佳实践

**影响: MEDIUM** - 指令用于低级 DOM 访问。谨慎使用它们,保持它们副作用安全,并在您需要有状态或可重用的 UI 行为时偏好组件或 composables。

## 任务列表

- 仅在需要直接 DOM 访问时使用指令
- 不要变异指令参数或绑定对象
- 在 `unmounted` 中清理计时器、侦听器和观察者
- 在 `<script setup>` 中使用 `v-` 前缀注册指令
- 在 TypeScript 项目中,类型化指令值并增强模板指令类型
- 偏好组件或 composables 进行复杂行为

## 将指令参数视为只读

指令绑定不是响应式存储。不要写入它们。

```ts
const vFocus = {
  mounted(el, binding) {
    // binding.value 是只读的
    el.focus()
  }
}
```

## 避免在组件上使用指令

指令应用于 DOM 元素。在组件上使用时,它们附加到根元素,如果根更改可能会中断。

**错误:**
```vue
<MyInput v-focus />
```

**正确:**
```vue
<!-- MyInput.vue -->
<script setup>
const vFocus = (el) => el.focus()
</script>

<template>
  <input v-focus />
</template>
```

## 在 `unmounted` 中清理副作用

任何计时器、侦听器或观察者都必须移除以避免泄漏。

```ts
const vResize = {
  mounted(el) {
    const observer = new ResizeObserver(() => {})
    observer.observe(el)
    el._observer = observer
  },
  unmounted(el) {
    el._observer?.disconnect()
  }
}
```

## 对单钩子指令偏好函数简写

如果您只需要 `mounted`/`updated`,请使用函数形式。

```ts
const vAutofocus = (el) => el.focus()
```

## 使用 `v-` 前缀和脚本设置注册

```vue
<script setup>
const vFocus = (el) => el.focus()
</script>

<template>
  <input v-focus />
</template>
```

## 在 TypeScript 项目中类型化自定义指令

使用 `Directive<Element, ValueType>` 以便 `binding.value` 被类型化,并增强 Vue 的模板类型以便在 SFC 模板中识别指令。

**错误:**
```ts
// 未类型化的指令值且没有模板类型增强
export const vHighlight = {
  mounted(el, binding) {
    el.style.backgroundColor = binding.value
  }
}
```

**正确:**
```ts
import type { Directive } from 'vue'

type HighlightValue = string

export const vHighlight = {
  mounted(el, binding) {
    el.style.backgroundColor = binding.value
  }
} satisfies Directive<HTMLElement, HighlightValue>

declare module 'vue' {
  interface ComponentCustomProperties {
    vHighlight: typeof vHighlight
  }
}
```

## 使用 `getSSRProps` 处理 SSR

指令钩子如 `mounted` 和 `updated` 在 SSR 期间不运行。如果指令设置影响渲染 HTML 的属性/类,通过 `getSSRProps` 提供 SSR 等效项以避免水合不匹配。

**错误:**
```ts
const vTooltip = {
  mounted(el, binding) {
    el.setAttribute('data-tooltip', binding.value)
    el.classList.add('has-tooltip')
  }
}
```

**正确:**
```ts
const vTooltip = {
  mounted(el, binding) {
    el.setAttribute('data-tooltip', binding.value)
    el.classList.add('has-tooltip')
  },
  getSSRProps(binding) {
    return {
      'data-tooltip': binding.value,
      class: 'has-tooltip'
    }
  }
}
```

## 尽可能偏好声明性模板

如果标准属性或绑定有效,请使用它而不是指令。

## 在指令和组件之间做决定

对 DOM 级行为使用指令。当行为影响结构、状态或渲染时使用组件。
