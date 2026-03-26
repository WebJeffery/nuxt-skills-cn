---
name: reka-ui
description: 使用 Reka UI（无头 Vue 组件）构建时使用 - 提供组件 API、可访问性模式、组合（asChild）、受控/不受控状态、虚拟化和样式集成。以前称为 Radix Vue。
license: MIT
---

# Reka UI

无样式、可访问的 Vue 3 组件原语。符合 WAI-ARIA 标准。以前称为 Radix Vue。

**当前版本：** v2.8.0（2026 年 1 月）

## 何时使用

- 从头开始构建无头/无样式组件
- 需要符合 WAI-ARIA 标准的组件
- 使用 Nuxt UI、shadcn-vue 或其他基于 Reka 的库
- 实现可访问的表单、对话框、菜单、弹出框

**对于 Vue 模式：** 使用 `vue` 技能

## 可用指南

| 文件                                                     | 主题                                                              |
| -------------------------------------------------------- | ------------------------------------------------------------------- |
| **[references/components.md](references/components.md)** | 按类别分类的组件索引（表单、日期、覆盖层、菜单、数据等） |
| **components/\*.md**                                     | 每个组件的详细信息（dialog.md、select.md 等）                  |

**指南**（参见 [reka-ui.com](https://reka-ui.com)）：样式、动画、组合、SSR、命名空间、日期、i18n、受控状态、注入上下文、虚拟化、迁移

## 加载文件

**根据您的任务考虑加载这些参考文件：**

- [ ] [references/components.md](references/components.md) - 如果正在按类别浏览组件索引或搜索特定组件

**不要一次加载所有文件。** 仅加载与当前任务相关的内容。

**对于基于 Reka UI 构建的样式化 Nuxt 组件：** 使用 **nuxt-ui** 技能

## 关键概念

| 概念                 | 描述                                                           |
| ----------------------- | --------------------------------------------------------------------- |
| `asChild`               | 作为子元素而不是包装器渲染，合并属性/行为    |
| 受控/不受控 | 使用 `v-model` 进行受控，使用 `default*` 属性进行不受控       |
| 部分                   | 组件拆分为 Root、Trigger、Content、Portal 等            |
| `forceMount`            | 将元素保留在 DOM 中以用于动画库                           |
| 虚拟化          | 使用虚拟滚动优化大型列表（Combobox、Listbox、Tree） |
| 上下文注入       | 从子组件访问组件上下文                        |

## 安装

```ts
// nuxt.config.ts（自动导入所有组件）
export default defineNuxtConfig({
  modules: ['reka-ui/nuxt']
})
```

```ts
import { RekaResolver } from 'reka-ui/resolver'
// vite.config.ts（带自动导入解析器）
import Components from 'unplugin-vue-components/vite'

export default defineConfig({
  plugins: [
    vue(),
    Components({ resolvers: [RekaResolver()] })
  ]
})
```

## 基本模式

```vue
<!-- 带受控状态的对话框 -->
<script setup>
import { DialogRoot, DialogTrigger, DialogPortal, DialogOverlay, DialogContent, DialogTitle, DialogDescription, DialogClose } from 'reka-ui'
const open = ref(false)
</script>

<template>
  <DialogRoot v-model:open="open">
    <DialogTrigger>打开</DialogTrigger>
    <DialogPortal>
      <DialogOverlay class="fixed inset-0 bg-black/50" />
      <DialogContent class="fixed left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2 bg-white p-6 rounded">
        <DialogTitle>标题</DialogTitle>
        <DialogDescription>描述</DialogDescription>
        <DialogClose>关闭</DialogClose>
      </DialogContent>
    </DialogPortal>
  </DialogRoot>
</template>
```

```vue
<!-- 带不受控默认值的选择 -->
<SelectRoot default-value="apple">
  <SelectTrigger>
    <SelectValue placeholder="选择水果" />
  </SelectTrigger>
  <SelectPortal>
    <SelectContent>
      <SelectViewport>
        <SelectItem value="apple"><SelectItemText>苹果</SelectItemText></SelectItem>
        <SelectItem value="banana"><SelectItemText>香蕉</SelectItemText></SelectItem>
      </SelectViewport>
    </SelectContent>
  </SelectPortal>
</SelectRoot>
```

```vue
<!-- asChild 用于自定义触发器元素 -->
<DialogTrigger as-child>
  <button class="my-custom-button">打开</button>
</DialogTrigger>
```

## 最近更新（v2.6.0-v2.8.0）

- **新组件**：Rating（v2.8.0）
- **ScrollArea**：添加了 "glimpse" 滚动条模式（v2.8.0）
- **PopperContent**：添加了 `hideShiftedArrow` 属性（v2.8.0）
- **TimeField**：添加了 `stepSnapping` 支持（v2.8.0）
- **重大更改**：日期组件的 `weekStartsOn` 现在与语言环境无关（v2.8.0）
- **虚拟化**：`estimateSize` 接受 Listbox/Tree 的函数（v2.7.0）
- **组合式函数**：暴露了 `useLocale`、`useDirection`（v2.6.0）
- **Select**：Content 上的 `disableOutsidePointerEvents` 属性（v2.7.0）
- **Toast**：`disableSwipe` 属性（v2.6.0）

## 资源

- [Reka UI 文档](https://reka-ui.com)
- [GitHub](https://github.com/unovue/reka-ui)
- [Nuxt UI](https://ui.nuxt.com)（样式化的 Reka 组件）
- [shadcn-vue](https://www.shadcn-vue.com)（样式化的 Reka 组件）

---

_Token 效率：基础约 350 个 token，components.md 索引约 100 个 token，每个组件约 50-150 个 token_
