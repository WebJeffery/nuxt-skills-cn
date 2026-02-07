---
title: 作用域 CSS 不能直接样式化插槽内容
impact: HIGH
impactDescription: 插槽内容接收父组件的作用域，而不是子组件的，导致样式意外失败
type: gotcha
tags: [vue3, sfc, scoped-css, slots, deep-selector]
---

# 作用域 CSS 不能直接样式化插槽内容

**影响：高** - 当父组件通过插槽传递内容时，该内容接收父组件的作用域样式属性，而不是子组件的。这意味着子组件不能使用常规的作用域 CSS 来样式化插槽内容。

## 任务清单

- [ ] 在包装组件中使用 `:deep()` 选择器来样式化插槽内容
- [ ] 或者，使用 `:slotted()` 伪选择器来目标定位插槽元素
- [ ] 对于复杂的插槽样式，考虑使用 CSS 模块或非作用域样式
- [ ] 当存在样式假设时，记录预期的插槽内容结构

**有问题的代码：**
```vue
<!-- Card.vue (子组件) -->
<template>
  <div class="card">
    <div class="card-body">
      <slot />
    </div>
  </div>
</template>

<style scoped>
.card-body {
  padding: 1rem;
}

/* BAD: Won't apply to slot content! */
.card-body h2 {
  color: #333;
  margin-bottom: 0.5rem;
}

.card-body p {
  color: #666;
}
</style>
```

```vue
<!-- Parent.vue -->
<template>
  <Card>
    <!-- This h2 and p won't be styled by Card's scoped CSS -->
    <h2>Card Title</h2>
    <p>Card description text.</p>
  </Card>
</template>
```

**Correct Code:**
```vue
<!-- Card.vue - Using :slotted() -->
<template>
  <div class="card">
    <div class="card-body">
      <slot />
    </div>
  </div>
</template>

<style scoped>
.card-body {
  padding: 1rem;
}

/* GOOD: :slotted() targets slot content */
:slotted(h2) {
  color: #333;
  margin-bottom: 0.5rem;
}

:slotted(p) {
  color: #666;
}
</style>
```

## Using :deep() Alternative

```vue
<!-- Card.vue - Using :deep() -->
<style scoped>
.card-body {
  padding: 1rem;
}

/* :deep() also works for slot content */
.card-body :deep(h2) {
  color: #333;
}

.card-body :deep(p) {
  color: #666;
}
</style>
```

## Why This Happens

Slot content is compiled in the parent component's scope:

```vue
<!-- Parent template compiles to: -->
<Card>
  <h2 data-v-parent123>Card Title</h2>
  <p data-v-parent123>Card description</p>
</Card>

<!-- Card template compiles to: -->
<div class="card" data-v-card456>
  <div class="card-body" data-v-card456>
    <slot />  <!-- Content inserted WITHOUT data-v-card456 -->
  </div>
</div>
```

The `<h2>` has `data-v-parent123`, but Card's scoped CSS expects `data-v-card456`.

## :slotted() vs :deep() for Slots

Both work, but have subtle differences:

```vue
<style scoped>
/* :slotted() - Specifically for slot content */
/* Only targets direct slotted elements */
:slotted(h2) {
  color: blue;
}

/* :deep() - More general deep selector */
/* Can target nested elements within slot content */
.card-body :deep(h2) {
  color: blue;
}

/* For nested elements in slot content, must use :deep() */
:slotted(.wrapper h2) { }  /* Won't work for nested h2 */
.card-body :deep(.wrapper h2) { }  /* Works for nested */
</style>
```

## Combining with Named Slots

```vue
<template>
  <div class="card">
    <header class="card-header">
      <slot name="header" />
    </header>
    <div class="card-body">
      <slot />
    </div>
    <footer class="card-footer">
      <slot name="footer" />
    </footer>
  </div>
</template>

<style scoped>
/* Style specific slot content */
.card-header :slotted(h1),
.card-header :slotted(h2) {
  margin: 0;
  font-size: 1.25rem;
}

.card-body :slotted(p) {
  margin-bottom: 1rem;
}

.card-footer :slotted(button) {
  margin-right: 0.5rem;
}
</style>
```

## Performance Tip: Use Classes

Element selectors with `:slotted()` can be slower:

```vue
<style scoped>
/* SLOWER: Element selector */
:slotted(p) {
  color: gray;
}

/* FASTER: Class selector */
:slotted(.card-text) {
  color: gray;
}
</style>
```

## When to Use Unscoped Styles

For complex slot styling, unscoped styles may be cleaner:

```vue
<template>
  <article class="article-card">
    <slot />
  </article>
</template>

<style>
/* Unscoped with unique prefix for complex content styling */
.article-card h1,
.article-card h2,
.article-card h3 {
  font-family: Georgia, serif;
  line-height: 1.2;
}

.article-card p {
  line-height: 1.6;
}

.article-card img {
  max-width: 100%;
}

.article-card blockquote {
  border-left: 3px solid #ccc;
  padding-left: 1rem;
}
</style>
```

## Reference
- [Vue.js Scoped CSS - Slotted Selectors](https://vuejs.org/api/sfc-css-features.html#slotted-selectors)
- [Vue.js Deep Selectors](https://vuejs.org/api/sfc-css-features.html#deep-selectors)
