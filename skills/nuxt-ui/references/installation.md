# 安装

## Nuxt 安装

```bash
pnpm add @nuxt/ui
```

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@nuxt/ui'],
  css: ['~/assets/css/main.css']
})
```

```css
/* assets/css/main.css */
@import 'tailwindcss';
@import '@nuxt/ui';
```

**关键：** 将应用包装在 UApp 中，以便 Toast、Tooltip 和覆盖层正常工作：

```vue
<!-- app.vue -->
<template>
  <UApp>
    <NuxtPage />
  </UApp>
</template>
```

### pnpm 注意事项

如果使用 pnpm，请执行以下任一操作：

1. 将 `shamefully-hoist=true` 添加到 `.npmrc`，或
2. 显式安装 tailwindcss：`pnpm add tailwindcss`

## Vue 安装（Vite）

```bash
pnpm add @nuxt/ui
```

```ts
import ui from '@nuxt/ui/vite'
import vue from '@vitejs/plugin-vue'
// vite.config.ts
import { defineConfig } from 'vite'

export default defineConfig({
  plugins: [vue(), ui()]
})
```

```ts
import ui from '@nuxt/ui/vue-plugin'
// main.ts
import { createApp } from 'vue'
import App from './App.vue'
import './assets/main.css'

const app = createApp(App)
app.use(ui)
app.mount('#app')
```

```css
/* assets/main.css */
@import 'tailwindcss';
@import '@nuxt/ui';
```

**关键：** 将 `isolate` 类添加到根元素以进行覆盖层堆叠：

```vue
<!-- App.vue -->
<template>
  <div class="isolate">
    <UApp>
      <RouterView />
    </UApp>
  </div>
</template>
```

### 自动导入

Vue 生成 `auto-imports.d.ts` 和 `components.d.ts`。添加到 `.gitignore`：

```gitignore
auto-imports.d.ts
components.d.ts
```

## 模块选项

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@nuxt/ui'],
  ui: {
    prefix: 'U', // 组件前缀（默认 'U'）
    fonts: true, // 启用 @nuxt/fonts
    colorMode: true, // 启用 @nuxtjs/color-mode
    theme: {
      colors: ['primary', 'secondary', 'success', 'info', 'warning', 'error', 'neutral'],
      transitions: true, // 组件上的 transition-colors
      defaultVariants: {
        color: 'primary',
        size: 'md'
      },
      prefix: '' // Tailwind CSS 前缀（v4.2+）- 确保前缀实用程序工作
    },
    mdc: false, // 强制使用 Prose 组件
    content: false, // 强制使用 UContent* 组件
    experimental: {
      componentDetection: false // Tree-shake 未使用的组件（v4.1+）- 仅为您实际使用的组件自动生成 CSS
    }
  }
})
```

## Vue Vite 选项

```ts
// vite.config.ts
ui({
  prefix: 'U',
  colorMode: true,
  inertia: true, // Inertia.js 支持
  theme: {
    colors: ['primary', 'secondary', 'success', 'info', 'warning', 'error', 'neutral'],
    transitions: true,
    defaultVariants: { color: 'primary', size: 'md' },
    prefix: ''
  },
  ui: {
    colors: { primary: 'green' }, // 运行时颜色配置
    button: { /* 主题覆盖 */ }
  }
})
```

## 自动安装的模块

Nuxt UI 自动安装：

- `@nuxt/icon` - 图标系统
- `@nuxt/fonts` - Web 字体（如果 `fonts: true`）
- `@nuxtjs/color-mode` - 深色模式（如果 `colorMode: true`）

## 常见问题

| 问题                     | 解决方案                                           |
| ------------------------- | -------------------------------------------------- |
| 找不到 Tailwind（pnpm） | 添加 `shamefully-hoist=true` 或安装 tailwindcss |
| 覆盖层未显示      | 将应用包装在 `<UApp>` 中                               |
| Vue 覆盖层损坏       | 将 `isolate` 类添加到根元素                |
| 图标未加载         | 检查是否安装了 @nuxt/icon                      |
| 深色模式不工作     | 确保配置中有 `colorMode: true`                 |

## 性能功能（v4.1+）

### 组件虚拟化

CommandPalette、InputMenu、SelectMenu、Table 和 Tree 中的大型数据集自动使用虚拟化以获得更好的性能。

### 实验性组件检测

启用 `experimental.componentDetection` 以仅为您实际使用的组件自动生成 CSS：

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  ui: {
    experimental: {
      componentDetection: true
    }
  }
})
```

**好处：** 更小的 CSS 包、更快的构建、减少未使用的样式。

### Tailwind CSS 前缀支持（v4.2+）

避免复杂应用中的样式冲突：

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  ui: {
    theme: {
      prefix: 'ui-' // 为所有 Tailwind 实用程序添加前缀
    }
  }
})
```

**结果：** 组件使用 `ui-bg-primary` 而不是 `bg-primary`。

## 最佳实践

| 做                         | 不要                     |
| -------------------------- | ------------------------- |
| 首先包装在 UApp 中         | 忘记 UApp 包装器       |
| 使用语义颜色        | 硬编码颜色值     |
| 正确导入 CSS       | 跳过 @nuxt/ui 导入      |
| 检查 pnpm hoisting        | 忽略 tailwindcss 错误 |
| 使用组件检测    | 运送未使用的组件 CSS |
| 在复杂应用中使用前缀 | 冒样式冲突风险      |
