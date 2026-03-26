---
name: unocss
description: UnoCSS 即时原子化 CSS 引擎，Tailwind CSS 的超集。用于配置 UnoCSS、编写工具规则、快捷方式或使用 Wind、Icons、Attributify 等预设时。
---

UnoCSS 是一个即时原子化 CSS 引擎，旨在灵活和可扩展。核心是非固执的 - 所有 CSS 工具类都通过预设提供。它是 Tailwind CSS 的超集，因此您可以重用您的 Tailwind 知识来进行基本语法使用。

**重要提示：** 在编写 UnoCSS 代码之前，代理应该检查项目根目录中的 `uno.config.*` 或 `unocss.config.*` 文件，以了解可用的预设、规则和快捷方式。如果项目设置不清楚，避免使用 attributify 模式和其他高级功能 - 坚持使用基本的 `class` 用法。

> 该技能基于 UnoCSS 66.x，生成于 2026-01-28。

## 核心

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| 配置 | 配置文件设置和所有配置选项 | [core-config](references/core-config.md) |
| 规则 | 用于生成 CSS 工具类的静态和动态规则 | [core-rules](references/core-rules.md) |
| 快捷方式 | 将多个规则组合成单个简写 | [core-shortcuts](references/core-shortcuts.md) |
| 主题 | 颜色、断点和设计令牌的主题系统 | [core-theme](references/core-theme.md) |
| 变体 | 将 hover:、dark:、responsive 等变体应用于规则 | [core-variants](references/core-variants.md) |
| 提取 | UnoCSS 如何从源代码中提取工具类 | [core-extracting](references/core-extracting.md) |
| 安全列表和阻止列表 | 强制包含或排除特定工具类 | [core-safelist](references/core-safelist.md) |
| 图层和预置 | CSS 图层排序和原始 CSS 注入 | [core-layers](references/core-layers.md) |

## 预设

### 主要预设

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| 预设 Wind3 | Tailwind CSS v3 / Windi CSS 兼容预设（最常用） | [preset-wind3](references/preset-wind3.md) |
| 预设 Wind4 | 具有现代 CSS 功能的 Tailwind CSS v4 兼容预设 | [preset-wind4](references/preset-wind4.md) |
| 预设 Mini | 具有基本工具类的最小预设，用于自定义构建 | [preset-mini](references/preset-mini.md) |

### 功能预设

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| 预设 Icons | 使用 Iconify 的纯 CSS 图标，支持任何图标集 | [preset-icons](references/preset-icons.md) |
| 预设 Attributify | 在 HTML 属性中组合工具类而不是 class | [preset-attributify](references/preset-attributify.md) |
| 预设 Typography | 用于排版默认值的散文类 | [preset-typography](references/preset-typography.md) |
| 预设 Web Fonts | 轻松集成 Google 字体和其他 Web 字体 | [preset-web-fonts](references/preset-web-fonts.md) |
| 预设 Tagify | 将工具类用作 HTML 标签名称 | [preset-tagify](references/preset-tagify.md) |
| 预设 Rem to Px | 将 rem 单位转换为 px 用于工具类 | [preset-rem-to-px](references/preset-rem-to-px.md) |

## 转换器

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| 变体组 | 使用公共前缀组合工具类的简写 | [transformer-variant-group](references/transformer-variant-group.md) |
| 指令 | CSS 指令：@apply、@screen、theme()、icon() | [transformer-directives](references/transformer-directives.md) |
| 编译类 | 将多个类编译为一个哈希类 | [transformer-compile-class](references/transformer-compile-class.md) |
| Attributify JSX | 在 JSX/TSX 中支持无值的 attributify | [transformer-attributify-jsx](references/transformer-attributify-jsx.md) |

## 集成

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| Vite 集成 | 使用 Vite 设置 UnoCSS 和特定框架的提示 | [integrations-vite](references/integrations-vite.md) |
| Nuxt 集成 | 用于 Nuxt 应用程序的 UnoCSS 模块 | [integrations-nuxt](references/integrations-nuxt.md) |
