---
title: Tailwind CSS 动态类生成
impact: HIGH
impactDescription: 动态构造 Tailwind 类名会导致生产环境中样式缺失
type: gotcha
tags: [vue3, tailwind, css, build-tools, dynamic-classes]
---

# Tailwind CSS 动态类生成

**影响：高** - 永远不要使用字符串连接或模板字面量动态构造 Tailwind CSS 类名。Tailwind 的构建过程无法检测动态生成的类名，导致生产环境中样式缺失。

## 规则

- Tailwind 在构建时使用静态分析来确定包含哪些 CSS 类
- 动态构造的类名（例如，`bg-${color}-500`）对 Tailwind 的扫描器不可见
- 类在开发中使用 JIT 工作，但在生产构建中静默失败
- 这是"它在本地工作但在生产中不工作"错误的常见来源

## 不好的代码

```vue
<script setup>
const props = defineProps({
  color: String, // 'red', 'blue', 'green'
  size: String   // 'sm', 'md', 'lg'
})
</script>

<template>
  <!-- 错误: Tailwind 无法检测这些类 -->
  <div :class="`bg-${color}-500 text-${size}`">
    内容
  </div>

  <!-- 错误: 字符串连接 -->
  <div :class="'p-' + padding">
    内容
  </div>

  <!-- 错误: 数组中的模板字面量 -->
  <div :class="[`gap-x-${spacing}`]">
    内容
  </div>
</template>
```

## 好的代码

```vue
<script setup>
const props = defineProps({
  color: String,
  size: String
})

// 使用带有完整类名的映射对象
const colorClasses = {
  red: 'bg-red-500',
  blue: 'bg-blue-500',
  green: 'bg-green-500'
}

const sizeClasses = {
  sm: 'text-sm p-2',
  md: 'text-base p-4',
  lg: 'text-lg p-6'
}
</script>

<template>
  <!-- 正确: Tailwind 可以检测的完整类名 -->
  <div :class="[colorClasses[color], sizeClasses[size]]">
    内容
  </div>
</template>
```

## 使用条件对象

```vue
<script setup>
const props = defineProps({
  variant: String // 'primary', 'secondary', 'danger'
})
</script>

<template>
  <!-- 正确: 所有类名都是完整的字符串 -->
  <button :class="{
    'bg-blue-500 hover:bg-blue-600': variant === 'primary',
    'bg-gray-500 hover:bg-gray-600': variant === 'secondary',
    'bg-red-500 hover:bg-red-600': variant === 'danger'
  }">
    点击我
  </button>
</template>
```

## 为真正动态的类使用安全列表

如果必须使用动态类，请将它们添加到 Tailwind 的安全列表中:

```javascript
// tailwind.config.js
module.exports = {
  safelist: [
    'bg-red-500',
    'bg-blue-500',
    'bg-green-500',
    // 或使用模式（谨慎使用 - 增加包大小）
    {
      pattern: /bg-(red|blue|green)-(100|500|900)/
    }
  ]
}
```

## 替代方案: CSS 自定义属性

对于真正动态的值，使用 CSS 自定义属性:

```vue
<script setup>
const props = defineProps({
  customColor: String // 任何十六进制颜色
})
</script>

<template>
  <!-- 使用 CSS 变量实现真正动态的值 -->
  <div
    class="dynamic-bg"
    :style="{ '--dynamic-color': customColor }"
  >
    内容
  </div>
</template>

<style>
.dynamic-bg {
  background-color: var(--dynamic-color);
}
</style>
```

## 参考

- [Tailwind CSS 动态类名](https://tailwindcss.com/docs/content-configuration#dynamic-class-names)
- [Tailwind 安全列表](https://tailwindcss.com/docs/content-configuration#safelisting-classes)
