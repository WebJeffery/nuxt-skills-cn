# 使用 camelCase 作为样式绑定属性

## 规则

在 Vue 中使用 `:style` 绑定时，为了与 JavaScript 对象语法和更好的 IDE 支持保持一致，优先使用 camelCase 属性名而不是 kebab-case。

## 为什么这很重要

- camelCase 是对象属性的标准 JavaScript 约定
- Kebab-case 属性需要引号，增加视觉噪音
- IDE 自动完成和类型检查与 camelCase 配合得更好
- 与在 JavaScript 中通过 `element.style` 访问 CSS 属性的方式一致
- Vue 的自动前缀处理两者都有效，但 camelCase 更简洁

## 不好的代码

```vue
<template>
  <!-- 需要引号，更难阅读 -->
  <div :style="{
    'font-size': fontSize + 'px',
    'background-color': bgColor,
    'margin-top': marginTop + 'px',
    'z-index': zIndex
  }">
    内容
  </div>
</template>
```

## 好的代码

```vue
<template>
  <!-- 简洁的 camelCase 语法 -->
  <div :style="{
    fontSize: fontSize + 'px',
    backgroundColor: bgColor,
    marginTop: marginTop + 'px',
    zIndex: zIndex
  }">
    内容
  </div>
</template>
```

## 使用计算属性

```vue
<script setup>
import { computed } from 'vue'

const props = defineProps({
  width: Number,
  height: Number,
  isVisible: Boolean
})

// 全程使用 camelCase
const containerStyle = computed(() => ({
  width: `${props.width}px`,
  height: `${props.height}px`,
  opacity: props.isVisible ? 1 : 0,
  transition: 'opacity 0.3s ease'
}))
</script>

<template>
  <div :style="containerStyle">
    内容
  </div>
</template>
```

## 带有多个值的自动前缀处理

Vue 自动检测并应用供应商前缀。对于需要多个值以实现跨浏览器支持的属性，使用数组:

```vue
<template>
  <!-- Vue 将使用浏览器支持的最后一个值 -->
  <div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }">
    Flexbox 内容
  </div>
</template>
```

## 组合静态和动态样式

`:style` 指令可以与常规 `style` 属性共存:

```vue
<template>
  <!-- 静态样式 + 动态样式合并 -->
  <div
    style="border: 1px solid gray"
    :style="{ backgroundColor: dynamicColor }"
  >
    内容
  </div>
</template>
```

## 合并多个样式对象

使用数组语法合并多个样式对象:

```vue
<script setup>
const baseStyles = {
  padding: '10px',
  borderRadius: '4px'
}

const themeStyles = computed(() => ({
  backgroundColor: isDark.value ? '#333' : '#fff',
  color: isDark.value ? '#fff' : '#333'
}))
</script>

<template>
  <!-- 后面的对象覆盖前面对象的冲突属性 -->
  <div :style="[baseStyles, themeStyles]">
    内容
  </div>
</template>
```

## 参考

- [类和样式绑定 - 绑定内联样式](https://vuejs.org/guide/essentials/class-and-style.html#binding-inline-styles)
