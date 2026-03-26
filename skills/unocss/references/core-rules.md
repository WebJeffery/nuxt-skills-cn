---
name: unocss-rules
description: 在 UnoCSS 中生成 CSS 工具类的静态和动态规则
---

# UnoCSS 规则

规则定义工具类及其生成的 CSS。UnoCSS 通过预设提供了许多内置规则，并允许自定义规则。

## 静态规则

从类名到 CSS 属性的简单映射：

```ts
rules: [
  ['m-1', { margin: '0.25rem' }],
  ['font-bold', { 'font-weight': 700 }],
]
```

用法：`<div class="m-1">` 生成 `.m-1 { margin: 0.25rem; }`

**注意：** 使用带连字符的 CSS 属性语法（例如，`font-weight` 而不是 `fontWeight`）。用引号括起带连字符的属性。

## 动态规则

使用 RegExp 匹配器和函数体来实现灵活的工具类：

```ts
rules: [
  // 匹配 m-1、m-2、m-100 等
  [/^m-(\d+)$/, ([, d]) => ({ margin: `${d / 4}rem` })],
  
  // 访问主题和上下文
  [/^p-(\d+)$/, (match, ctx) => ({ padding: `${match[1] / 4}rem` })],
]
```

该函数接收：
1. RegExp 匹配结果（解构以获取捕获组）
2. 包含 `theme`、`symbols` 等的上下文对象

## CSS 回退值

返回二维数组以实现 CSS 属性回退（浏览器兼容性）：

```ts
rules: [
  [/^h-(\d+)dvh$/, ([_, d]) => [
    ['height', `${d}vh`],
    ['height', `${d}dvh`],
  ]],
]
```

生成：`.h-100dvh { height: 100vh; height: 100dvh; }`

## 特殊符号

使用 `@unocss/core` 中的符号控制 CSS 输出：

```ts
import { symbols } from '@unocss/core'

rules: [
  ['grid', {
    [symbols.parent]: '@supports (display: grid)',
    display: 'grid',
  }],
]
```

### 可用符号

| 符号 | 描述 |
|--------|-------------|
| `symbols.parent` | 父包装器（例如，`@supports`、`@media`） |
| `symbols.selector` | 修改选择器的函数 |
| `symbols.layer` | 设置 UnoCSS 图层 |
| `symbols.variants` | 变体处理程序数组 |
| `symbols.shortcutsNoMerge` | 在快捷方式中禁用合并 |
| `symbols.noMerge` | 禁用规则合并 |
| `symbols.sort` | 覆盖排序顺序 |
| `symbols.body` | 完全控制 CSS 主体 |

## 多选择器规则

使用生成器函数生成多个 CSS 规则：

```ts
rules: [
  [/^button-(.*)$/, function* ([, color], { symbols }) {
    yield { background: color }
    yield {
      [symbols.selector]: selector => `${selector}:hover`,
      background: `color-mix(in srgb, ${color} 90%, black)`
    }
  }],
]
```

生成 `.button-red { background: red; }` 和 `.button-red:hover { ... }`

## 完全控制的规则

返回字符串以进行完全的 CSS 控制（高级）：

```ts
import { defineConfig, toEscapedSelector as e } from 'unocss'

rules: [
  [/^custom-(.+)$/, ([, name], { rawSelector, theme }) => {
    const selector = e(rawSelector)
    return `
${selector} { font-size: ${theme.fontSize.sm}; }
${selector}::after { content: 'after'; }
@media (min-width: ${theme.breakpoints.sm}) {
  ${selector} { font-size: ${theme.fontSize.lg}; }
}
`
  }],
]
```

**警告：** 完全控制的规则不适用于 `hover:` 等变体。

## Symbols.body 用于变体支持

使用 `symbols.body` 保持变体支持并使用自定义 CSS：

```ts
rules: [
  ['custom-red', {
    [symbols.body]: `
      font-size: 1rem;
      &::after { content: 'after'; }
      & > .bar { color: red; }
    `,
    [symbols.selector]: selector => `:is(${selector})`,
  }]
]
```

## 规则排序

后面的规则具有更高的优先级。动态规则输出在组内按字母顺序排序。

## 规则合并

UnoCSS 合并具有相同 CSS 主体的规则：

```html
<div class="m-2 hover:m2">
```

生成：
```css
.hover\:m2:hover, .m-2 { margin: 0.5rem; }
```

使用 `symbols.noMerge` 禁用。

<!--
源参考：
- https://unocss.dev/config/rules
-->
