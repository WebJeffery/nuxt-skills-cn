---
name: unocss-variants
description: 将 hover:、dark:、responsive 等变体应用于规则
---

# UnoCSS 变体

变体对工具规则应用修改，如 `hover:`、`dark:` 或响应式前缀。

## 变体如何工作

当匹配 `hover:m-2` 时：

1. 从源代码中提取 `hover:m-2`
2. 发送到所有变体进行匹配
3. `hover:` 变体匹配并返回 `m-2`
4. 结果 `m-2` 继续到下一个变体
5. 最后匹配规则 `.m-2 { margin: 0.5rem; }`
6. 应用变体转换：`.hover\:m-2:hover { margin: 0.5rem; }`

## 创建自定义变体

```ts
variants: [
  // hover: 变体
  (matcher) => {
    if (!matcher.startsWith('hover:'))
      return matcher
    return {
      // 移除前缀，传递给下一个变体/规则
      matcher: matcher.slice(6),
      // 修改选择器
      selector: s => `${s}:hover`,
    }
  },
],
rules: [
  [/^m-(\d)$/, ([, d]) => ({ margin: `${d / 4}rem` })],
]
```

## 变体返回对象

- `matcher` - 向前传递的处理后的类名
- `selector` - 自定义 CSS 选择器的函数
- `parent` - 像 `@media`、`@supports` 这样的包装器
- `layer` - 指定输出图层
- `sort` - 控制排序

## 内置变体（preset-wind3）

### 伪类
- `hover:`、`focus:`、`active:`、`visited:`
- `first:`、`last:`、`odd:`、`even:`
- `disabled:`、`checked:`、`required:`
- `focus-within:`、`focus-visible:`

### 伪元素
- `before:`、`after:`
- `placeholder:`、`selection:`
- `marker:`、`file:`

### 响应式
- `sm:`、`md:`、`lg:`、`xl:`、`2xl:`
- `lt-sm:`（小于 sm）
- `at-lg:`（仅在 lg）

### 暗色模式
- `dark:` - 基于类的暗色模式（默认）
- `@dark:` - 媒体查询暗色模式

### 组合/同级
- `group-hover:`、`group-focus:`
- `peer-checked:`、`peer-focus:`

### 容器查询
- `@container`、`@sm:`、`@md:`

### 打印
- `print:`

### 支持
- `supports-[display:grid]:`

### Aria
- `aria-checked:`、`aria-disabled:`

### 数据属性
- `data-[state=open]:`

## 暗色模式配置

### 基于类（默认）
```ts
presetWind3({
  dark: 'class'
})
```

```html
<div class="dark:bg-gray-800">
```

生成：`.dark .dark\:bg-gray-800 { ... }`

### 媒体查询
```ts
presetWind3({
  dark: 'media'
})
```

生成：`@media (prefers-color-scheme: dark) { ... }`

### 选择性媒体查询

无论配置如何，使用 `@dark:`：

```html
<div class="@dark:bg-gray-800">
```

### 自定义选择器
```ts
presetWind3({
  dark: {
    light: '.light-mode',
    dark: '.dark-mode',
  }
})
```

## CSS @layer 变体

原生 CSS `@layer` 支持：

```html
<div class="layer-foo:p-4" />
```

生成：
```css
@layer foo {
  .layer-foo\:p-4 { padding: 1rem; }
}
```

## 与 Windi CSS 的断点差异

| Windi CSS | UnoCSS |
|-----------|--------|
| `<sm:p-1` | `lt-sm:p-1` |
| `@lg:p-1` | `at-lg:p-1` |
| `>xl:p-1` | `xl:p-1` |

## 媒体悬停（实验性）

解决触摸设备上的粘性悬停：

```html
<div class="@hover-text-red">
```

生成：
```css
@media (hover: hover) and (pointer: fine) {
  .\@hover-text-red:hover { color: rgb(248 113 113); }
}
```

<!--
源参考：
- https://unocss.dev/config/variants
- https://unocss.dev/presets/wind3
- https://unocss.dev/presets/mini
-->
