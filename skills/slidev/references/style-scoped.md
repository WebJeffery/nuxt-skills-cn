---
name: scoped
description: 幻灯片作用域 CSS 样式
---

# 幻灯片作用域样式

定义仅应用于当前幻灯片的 CSS。

## 用法

```md
# 这是红色的

<style>
h1 {
  color: red;
}
</style>

---

# 其他幻灯片不受影响
```

## 默认作用域

幻灯片中的所有 `<style>` 标签都会自动作用域化。

由于作用域化，子组合器（`.a > .b`）无法按预期工作。

## 使用 UnoCSS 的嵌套 CSS

```md
# Slidev

> Hello **world**

<style>
blockquote {
  strong {
    --uno: 'text-teal-500 dark:text-teal-400';
  }
}
</style>
```

## 全局样式

对于全局样式，请在项目中使用 `styles/index.css`。
