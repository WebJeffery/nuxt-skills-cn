---
title: 使用模块增强类型化自定义指令
impact: LOW
impactDescription: 没有类型增强，模板中的自定义指令缺少 TypeScript 支持
type: best-practice
tags: [vue3, typescript, directives, module-augmentation, ComponentCustomProperties]
---

# 使用模块增强类型化自定义指令

**影响：低** - 要在模板中获得自定义指令的 TypeScript 支持，必须增强 Vue 的 `ComponentCustomProperties` 接口。没有这个，TypeScript 将无法识别您的自定义指令，也不会为它们的值提供类型检查。

## 任务清单

- [ ] 为您的指令创建类型定义文件
- [ ] 使用 `Directive<Element, Value>` 进行类型定义
- [ ] 使用带 `v` 前缀的名称增强 `ComponentCustomProperties`
- [ ] 使用 `satisfies` 关键字实现类型安全的指令定义

## 基本模式

```typescript
// directives/highlight.ts
import type { Directive, DirectiveBinding } from 'vue'

// 定义指令的值类型
export type HighlightValue = string | { color: string; intensity?: number }

// 创建类型化指令
export const vHighlight: Directive<HTMLElement, HighlightValue> = {
  mounted(el, binding) {
    const value = binding.value
    if (typeof value === 'string') {
      el.style.backgroundColor = value
    } else {
      el.style.backgroundColor = value.color
      el.style.opacity = String(value.intensity ?? 1)
    }
  },
  updated(el, binding) {
    // 更新使用相同的逻辑
  }
}

// 模板使用的类型增强
declare module 'vue' {
  interface ComponentCustomProperties {
    vHighlight: typeof vHighlight  // 使用 'v' 前缀！
  }
}
```

## 使用 satisfies 实现类型安全

```typescript
// directives/focus.ts
import type { Directive } from 'vue'

// 指令值是 boolean：如果为 true 则自动聚焦
export type FocusValue = boolean

export const vFocus = {
  mounted(el, binding) {
    if (binding.value) {
      el.focus()
    }
  },
  updated(el, binding) {
    if (binding.value && !binding.oldValue) {
      el.focus()
    }
  }
} satisfies Directive<HTMLInputElement, FocusValue>

declare module 'vue' {
  interface ComponentCustomProperties {
    vFocus: Directive<HTMLInputElement, FocusValue>
  }
}
```

## 带修饰符的完整示例

```typescript
// directives/tooltip.ts
import type { Directive, DirectiveBinding } from 'vue'

export interface TooltipValue {
  text: string
  delay?: number
}

export interface TooltipModifiers {
  top?: boolean
  bottom?: boolean
  left?: boolean
  right?: boolean
}

// 类型化完整的绑定
type TooltipBinding = DirectiveBinding<TooltipValue, TooltipModifiers>

export const vTooltip: Directive<HTMLElement, TooltipValue> = {
  mounted(el: HTMLElement, binding: TooltipBinding) {
    const { value, modifiers } = binding

    let position: string = 'bottom'
    if (modifiers.top) position = 'top'
    else if (modifiers.left) position = 'left'
    else if (modifiers.right) position = 'right'

    el.setAttribute('data-tooltip', value.text)
    el.setAttribute('data-tooltip-position', position)
    el.setAttribute('data-tooltip-delay', String(value.delay ?? 300))
  },

  updated(el, binding) {
    el.setAttribute('data-tooltip', binding.value.text)
  },

  unmounted(el) {
    // 清理
    el.removeAttribute('data-tooltip')
  }
}

declare module 'vue' {
  interface ComponentCustomProperties {
    vTooltip: typeof vTooltip
  }
}
```

## 注册和使用

```typescript
// main.ts
import { createApp } from 'vue'
import App from './App.vue'
import { vHighlight } from './directives/highlight'
import { vFocus } from './directives/focus'
import { vTooltip } from './directives/tooltip'

const app = createApp(App)

// 全局注册
app.directive('highlight', vHighlight)
app.directive('focus', vFocus)
app.directive('tooltip', vTooltip)

app.mount('#app')
```

```vue
<!-- Component.vue -->
<template>
  <!-- TypeScript 验证值类型 -->
  <div v-highlight="'yellow'">高亮文本</div>
  <div v-highlight="{ color: 'blue', intensity: 0.5 }">复杂高亮</div>

  <input v-focus="shouldFocus" />

  <button v-tooltip="{ text: '点击我', delay: 500 }">
    悬停显示提示
  </button>

  <!-- 带修饰符 -->
  <span v-tooltip.top="{ text: '上方' }">上方提示</span>
</template>
```

## 组织类型声明

对于有许多指令的项目，创建专用的类型文件：

```
src/
├── directives/
│   ├── index.ts           # 重新导出所有指令
│   ├── highlight.ts       # 指令实现
│   ├── focus.ts
│   └── tooltip.ts
├── types/
│   └── directives.d.ts    # 集中式类型增强
```

```typescript
// types/directives.d.ts
import type { Directive } from 'vue'
import type { HighlightValue } from '@/directives/highlight'
import type { FocusValue } from '@/directives/focus'
import type { TooltipValue } from '@/directives/tooltip'

declare module 'vue' {
  interface ComponentCustomProperties {
    vHighlight: Directive<HTMLElement, HighlightValue>
    vFocus: Directive<HTMLInputElement, FocusValue>
    vTooltip: Directive<HTMLElement, TooltipValue>
  }
}
```

## 确保 tsconfig.json 包含类型

```json
{
  "compilerOptions": {
    // ...
  },
  "include": [
    "src/**/*.ts",
    "src/**/*.vue",
    "src/types/**/*.d.ts"
  ]
}
```

## 函数简写指令

对于只需要 `mounted` 和 `updated` 的简单指令：

```typescript
// directives/color.ts
import type { Directive } from 'vue'

// 函数简写 - 在 mounted 和 updated 上运行
export const vColor: Directive<HTMLElement, string> = (el, binding) => {
  el.style.color = binding.value
}

declare module 'vue' {
  interface ComponentCustomProperties {
    vColor: typeof vColor
  }
}
```

## 带有 TypeScript 的本地注册

对于仅在特定组件中注册的指令：

```vue
<script setup lang="ts">
import type { Directive } from 'vue'

// 带内联类型的本地指令
const vLocalHighlight: Directive<HTMLElement, string> = {
  mounted(el, binding) {
    el.style.backgroundColor = binding.value
  }
}
</script>

<template>
  <!-- 无需全局类型增强即可工作 -->
  <div v-local-highlight="'yellow'">本地高亮</div>
</template>
```

注意：本地指令不需要模块增强，因为 TypeScript 可以从本地变量推断类型。

## 参考
- [Vue.js Custom Directives](https://vuejs.org/guide/reusability/custom-directives.html)
- [Vue.js TypeScript - Augmenting Global Properties](https://vuejs.org/guide/typescript/options-api.html#augmenting-global-properties)
