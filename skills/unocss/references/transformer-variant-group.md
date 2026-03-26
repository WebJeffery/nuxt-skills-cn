---
name: transformer-variant-group
description: 用于分组具有共同前缀的工具类的简写
---

# Transformer Variant Group

启用简写语法，用于分组具有共同前缀的工具类。

## 安装

```ts
import { defineConfig, transformerVariantGroup } from 'unocss'

export default defineConfig({
  transformers: [
    transformerVariantGroup(),
  ],
})
```

## 用法

使用括号在一个变体前缀下分组多个工具类：

```html
<!-- 转换之前 -->
<div class="hover:(bg-gray-400 font-medium) font-(light mono)" />

<!-- 转换之后 -->
<div class="hover:bg-gray-400 hover:font-medium font-light font-mono" />
```

## 示例

### 悬停状态

```html
<button class="hover:(bg-blue-600 text-white scale-105)">
  Hover me
</button>
```

扩展为：`hover:bg-blue-600 hover:text-white hover:scale-105`

### 暗色模式

```html
<div class="dark:(bg-gray-800 text-white)">
  Dark content
</div>
```

扩展为：`dark:bg-gray-800 dark:text-white`

### 响应式

```html
<div class="md:(flex items-center gap-4)">
  Responsive flex
</div>
```

扩展为：`md:flex md:items-center md:gap-4`

### 嵌套组

```html
<div class="lg:hover:(bg-blue-500 text-white)">
  Large screen hover
</div>
```

扩展为：`lg:hover:bg-blue-500 lg:hover:text-white`

### 多个前缀

```html
<div class="text-(sm gray-600) font-(medium mono)">
  Styled text
</div>
```

扩展为：`text-sm text-gray-600 font-medium font-mono`

## 关键点

- 使用括号 `()` 分组工具类
- 前缀应用于组内的所有工具类
- 可以与任何变体结合（hover、dark、responsive 等）
- 支持嵌套
- 在 class 属性和其他提取源中工作

<!--
源参考：
- https://unocss.dev/transformers/variant-group
-->
