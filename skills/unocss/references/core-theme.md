---
name: unocss-theme
description: 颜色、断点和设计令牌的主题系统
---

# UnoCSS 主题

UnoCSS 支持类似于 Tailwind CSS / Windi CSS 的主题。`theme` 属性与默认主题深度合并。

## 基本用法

```ts
theme: {
  colors: {
    veryCool: '#0000ff', // class="text-very-cool"
    brand: {
      primary: 'hsl(var(--hue, 217) 78% 51%)', // class="bg-brand-primary"
      DEFAULT: '#942192' // class="bg-brand"
    },
  },
}
```

## 在规则中使用主题

在动态规则中访问主题值：

```ts
rules: [
  [/^text-(.*)$/, ([, c], { theme }) => {
    if (theme.colors[c])
      return { color: theme.colors[c] }
  }],
]
```

## 在变体中使用主题

```ts
variants: [
  {
    name: 'variant-name',
    match(matcher, { theme }) {
      // 访问 theme.breakpoints、theme.colors 等
    },
  },
]
```

## 在快捷方式中使用主题

```ts
shortcuts: [
  [/^badge-(.*)$/, ([, c], { theme }) => {
    if (Object.keys(theme.colors).includes(c))
      return `bg-${c}4:10 text-${c}5 rounded`
  }],
]
```

## 断点

**警告：** 自定义 `breakpoints` 对象**覆盖**默认值，而不是合并。

```ts
theme: {
  breakpoints: {
    sm: '320px',
    md: '640px',
  },
}
```

只有 `sm:` 和 `md:` 变体将可用。

### 继承默认断点

使用 `extendTheme` 与默认值合并：

```ts
extendTheme: (theme) => {
  return {
    ...theme,
    breakpoints: {
      ...theme.breakpoints,
      sm: '320px',
      md: '640px',
    },
  }
}
```

**注意：** `verticalBreakpoints` 对垂直布局的工作方式相同。

### 断点排序

断点按大小排序。使用一致的单位以避免错误：

```ts
theme: {
  breakpoints: {
    sm: '320px',
    // 不要混合单位 - 将 rem 转换为 px
    // md: '40rem', // 不好
    md: `${40 * 16}px`, // 好
    lg: '960px',
  },
}
```

## ExtendTheme

`extendTheme` 允许您修改合并的主题对象：

### 修改主题

```ts
extendTheme: (theme) => {
  theme.colors.veryCool = '#0000ff'
  theme.colors.brand = {
    primary: 'hsl(var(--hue, 217) 78% 51%)',
  }
}
```

### 替换主题

返回一个新对象以完全替换：

```ts
extendTheme: (theme) => {
  return {
    ...theme,
    colors: {
      ...theme.colors,
      veryCool: '#0000ff',
    },
  }
}
```

## 预设中的主题差异

### preset-wind3 vs preset-wind4

| preset-wind3 | preset-wind4 |
|--------------|--------------|
| `fontFamily` | `font` |
| `fontSize` | `text.fontSize` |
| `lineHeight` | `text.lineHeight` 或 `leading` |
| `letterSpacing` | `text.letterSpacing` 或 `tracking` |
| `borderRadius` | `radius` |
| `easing` | `ease` |
| `breakpoints` | `breakpoint` |
| `boxShadow` | `shadow` |
| `transitionProperty` | `property` |

## 常见主题键

- `colors` - 颜色调色板
- `breakpoints` - 响应式断点
- `fontFamily` - 字体堆栈
- `fontSize` - 文本大小
- `spacing` - 间距比例
- `borderRadius` - 边框半径值
- `boxShadow` - 阴影定义
- `animation` - 动画关键帧和计时

<!--
源参考：
- https://unocss.dev/config/theme
-->
