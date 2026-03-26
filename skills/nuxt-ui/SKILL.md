---
name: nuxt-ui
description: 使用 @nuxt/ui v4 组件构建样式化 UI 时使用 - 创建带验证的表单、实现带排序的数据表、构建模态对话框和覆盖层、配置 Tailwind Variants 主题。原始组件模式使用 vue 技能，无头原语使用 reka-ui。
license: MIT
---

# Nuxt UI v4

基于 Reka UI（无头）+ Tailwind CSS v4 + Tailwind Variants 构建的 Vue 3 和 Nuxt 4+ 组件库。

**当前稳定版本：** v4.4.0（2026 年 1 月）

## 何时使用

- 安装/配置 @nuxt/ui
- 使用 UI 组件（Button、Card、Table、Form 等）
- 自定义主题（颜色、变体、CSS 变量）
- 构建带验证的表单
- 使用覆盖层（Modal、Toast、CommandPalette）
- 使用组合式函数（useToast、useOverlay）

**对于 Vue 组件模式：** 使用 `vue` 技能
**对于 Nuxt 路由/服务器：** 使用 `nuxt` 技能

## 可用指南

| 文件                                                         | 主题                                                                           |
| ------------------------------------------------------------ | -------------------------------------------------------------------------------- |
| **[references/installation.md](references/installation.md)** | Nuxt/Vue 设置、pnpm 注意事项、UApp 包装器、模块选项、前缀、tree-shaking |
| **[references/theming.md](references/theming.md)**           | 语义颜色、CSS 变量、app.config.ts、Tailwind Variants                 |
| **[references/components.md](references/components.md)**     | 按类别分类的组件索引（125+ 组件）                                    |
| **components/\*.md**                                         | 每个组件的详细信息（button.md、modal.md 等）                                |
| **[references/forms.md](references/forms.md)**               | 表单组件、验证（Zod/Valibot）、useFormField                          |
| **[references/overlays.md](references/overlays.md)**         | Toast、Modal、Slideover、Drawer、CommandPalette                                  |
| **[references/composables.md](references/composables.md)**   | useToast、useOverlay、defineShortcuts、useScrollspy                              |

## 加载文件

**根据您的任务考虑加载这些参考文件：**

- [ ] [references/installation.md](references/installation.md) - 如果正在安装或配置 @nuxt/ui
- [ ] [references/theming.md](references/theming.md) - 如果正在自定义主题、颜色或 Tailwind Variants
- [ ] [references/components.md](references/components.md) - 如果正在浏览组件索引或按类别查找组件
- [ ] [references/forms.md](references/forms.md) - 如果正在构建带验证（Zod/Valibot）的表单
- [ ] [references/overlays.md](references/overlays.md) - 如果正在使用 Toast、Modal、Slideover、Drawer 或 CommandPalette
- [ ] [references/composables.md](references/composables.md) - 如果正在使用 useToast、useOverlay 或其他组合式函数

**不要一次加载所有文件。** 仅加载与当前任务相关的内容。

## 关键概念

| 概念           | 描述                                                |
| ----------------- | ---------------------------------------------------------- |
| UApp              | Toast、Tooltip、覆盖层所需的包装器组件    |
| Tailwind Variants | 带有插槽、变体、compoundVariants 的类型安全样式   |
| 语义颜色   | primary、secondary、success、error、warning、info、neutral |
| Reka UI           | 无头组件原语（内置可访问性）     |

> 对于无头组件原语（API 详细信息、可访问性模式、asChild）：阅读 **reka-ui** 技能

## 快速参考

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

```vue
<!-- app.vue - 需要 UApp 包装器 -->
<template>
  <UApp>
    <NuxtPage />
  </UApp>
</template>
```

## 资源

- [Nuxt UI 文档](https://ui.nuxt.com)
- [组件参考](https://ui.nuxt.com/components)
- [主题自定义](https://ui.nuxt.com/getting-started/theme)

---

_Token 效率：主技能约 300 个 token，每个子文件约 800-1200 个 token_
