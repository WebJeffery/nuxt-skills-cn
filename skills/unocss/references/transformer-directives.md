---
name: transformer-directives
description: CSS 指令 @apply、@screen、theme() 和 icon()
---

# Transformer Directives

在 CSS 中启用 `@apply`、`@screen`、`theme()` 和 `icon()` 指令。

## 安装

```ts
import { defineConfig, transformerDirectives } from 'unocss'

export default defineConfig({
  transformers: [
    transformerDirectives(),
  ],
})
```

## @apply

在 CSS 中应用工具类：

```css
.custom-btn {
  @apply py-2 px-4 font-semibold rounded-lg;
}

/* 带变体 - 使用引号 */
.custom-btn {
  @apply 'hover:bg-blue-600 focus:ring-2';
}
```

### CSS 自定义属性替代方案

对于原生 CSS 兼容性：

```css
.custom-div {
  --at-apply: text-center my-0 font-medium;
}
```

支持的别名：`--at-apply`、`--uno-apply`、`--uno`

配置别名：

```ts
transformerDirectives({
  applyVariable: ['--at-apply', '--uno-apply', '--uno'],
  // 或禁用：applyVariable: false
})
```

## @screen

创建断点媒体查询：

```css
.grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
}

@screen sm {
  .grid {
    grid-template-columns: repeat(3, 1fr);
  }
}

@screen lg {
  .grid {
    grid-template-columns: repeat(4, 1fr);
  }
}
```

### 断点变体

```css
/* 小于断点 */
@screen lt-sm {
  .item { display: none; }
}

/* 仅在特定断点 */
@screen at-md {
  .item { width: 50%; }
}
```

## theme()

在 CSS 中访问主题值：

```css
.btn-blue {
  background-color: theme('colors.blue.500');
  padding: theme('spacing.4');
  border-radius: theme('borderRadius.lg');
}
```

点符号路径到您的主题配置。

## icon()

将图标工具类转换为 SVG（需要 preset-icons）：

```css
.icon-sun {
  background-image: icon('i-carbon-sun');
}

/* 使用自定义颜色 */
.icon-moon {
  background-image: icon('i-carbon-moon', '#fff');
}

/* 使用主题颜色 */
.icon-alert {
  background-image: icon('i-carbon-warning', 'theme("colors.red.500")');
}
```

## 完整示例

```css
.card {
  @apply rounded-lg shadow-md p-4;
  background-color: theme('colors.white');
}

.card-header {
  @apply 'font-bold text-lg border-b';
  padding-bottom: theme('spacing.2');
}

@screen md {
  .card {
    @apply flex gap-4;
  }
}

.card-icon {
  background-image: icon('i-carbon-document');
  @apply w-6 h-6;
}
```

<!--
源参考：
- https://unocss.dev/transformers/directives
-->
