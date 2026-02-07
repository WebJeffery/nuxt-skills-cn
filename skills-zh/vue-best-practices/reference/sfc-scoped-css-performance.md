---
title: 在作用域 CSS 中使用类选择器以提高性能
impact: MEDIUM
impactDescription: 作用域 CSS 中的元素选择器较慢，因为浏览器必须同时检查元素类型和数据属性
type: efficiency
tags: [vue3, sfc, scoped-css, performance, css-selectors]
---

# 在作用域 CSS 中使用类选择器以提高性能

**影响：中等** - 当 Vue 编译作用域 CSS 时，它会向每个规则添加属性选择器。元素选择器与属性选择器组合（例如 `p[data-v-xxx]`）比带有属性的类选择器（例如 `.text[data-v-xxx]`）对浏览器来说要慢得多。

## 任务清单

- [ ] 在作用域样式中优先使用类选择器而不是元素选择器
- [ ] 避免在作用域 CSS 中使用深度嵌套的元素选择器
- [ ] 使用 BEM 或类似的命名约定以确保唯一的类名
- [ ] 对于繁重的样式化，考虑使用 CSS 模块或实用程序优先的方法

**有问题的代码：**
```vue
<template>
  <article>
    <header>
      <h1>{{ title }}</h1>
      <p>{{ subtitle }}</p>
    </header>
    <section>
      <p>{{ content }}</p>
      <ul>
        <li v-for="item in items" :key="item">{{ item }}</li>
      </ul>
    </section>
  </article>
</template>

<style scoped>
/* 错误：作用域时元素选择器较慢 */
article {
  max-width: 800px;
}

header {
  margin-bottom: 2rem;
}

h1 {
  font-size: 2rem;
}

p {
  line-height: 1.6;
}

ul {
  padding-left: 1.5rem;
}

li {
  margin-bottom: 0.5rem;
}
</style>
```

**正确的代码：**
```vue
<template>
  <article class="article">
    <header class="article-header">
      <h1 class="article-title">{{ title }}</h1>
      <p class="article-subtitle">{{ subtitle }}</p>
    </header>
    <section class="article-content">
      <p class="article-text">{{ content }}</p>
      <ul class="article-list">
        <li v-for="item in items" :key="item" class="article-list-item">
          {{ item }}
        </li>
      </ul>
    </section>
  </article>
</template>

<style scoped>
/* 正确：类选择器更快 */
.article {
  max-width: 800px;
}

.article-header {
  margin-bottom: 2rem;
}

.article-title {
  font-size: 2rem;
}

.article-subtitle,
.article-text {
  line-height: 1.6;
}

.article-list {
  padding-left: 1.5rem;
}

.article-list-item {
  margin-bottom: 0.5rem;
}
</style>
```

## 作用域 CSS 如何编译

Vue 通过添加数据属性来转换作用域 CSS：

```css
/* 你编写的 */
p { color: red; }
.text { color: blue; }

/* Vue 生成的 */
p[data-v-7ba5bd90] { color: red; }
.text[data-v-7ba5bd90] { color: blue; }
```

浏览器 CSS 匹配 `p[data-v-xxx]`：
1. 查找所有 `<p>` 元素（元素查找）
2. 检查每个元素的 `data-v-xxx` 属性（属性检查）

浏览器 CSS 匹配 `.text[data-v-xxx]`：
1. 查找所有具有类 `text` 的元素（类查找 - 已优化）
2. 检查每个元素的 `data-v-xxx` 属性

类查找在现代浏览器中高度优化，使得选项 2 更快。

## 何时性能很重要

此优化在以下情况下最重要：
- 渲染大型列表（100+ 项）
- 复杂的组件树
- 动画繁重的界面
- CPU 有限的移动设备

```vue
<template>
  <!-- 1000 个项目 - 性能差异很明显 -->
  <ul class="list">
    <li v-for="item in items" :key="item.id" class="list-item">
      <span class="item-name">{{ item.name }}</span>
      <span class="item-price">{{ item.price }}</span>
    </li>
  </ul>
</template>

<style scoped>
/* 用于大型列表的快速选择器 */
.list { ... }
.list-item { ... }
.item-name { ... }
.item-price { ... }
</style>
```

## 可接受的元素选择器

对于元素很少的小型组件，元素选择器是可以的：

```vue
<template>
  <button class="btn">
    <span>{{ label }}</span>
  </button>
</template>

<style scoped>
/* 对于小型、简单的组件是可以的 */
.btn {
  padding: 0.5rem 1rem;
}

span {
  font-weight: bold;
}
</style>
```

## CSS 模块替代方案

对于性能关键的组件，CSS 模块完全避免属性选择器：

```vue
<template>
  <p :class="$style.text">内容</p>
</template>

<style module>
/* 生成唯一的类名，没有属性选择器 */
.text {
  color: red;
}
/* 编译为类似：.text_abc123 { color: red; } */
</style>
```

## 参考
- [Vue Loader 作用域 CSS](https://vue-loader.vuejs.org/guide/scoped-css.html#child-component-root-elements)
- [CSS 选择器性能](https://csswizardry.com/2011/09/writing-efficient-css-selectors/)
