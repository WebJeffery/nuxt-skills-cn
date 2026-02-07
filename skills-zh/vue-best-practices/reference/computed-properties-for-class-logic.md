# 对复杂类逻辑使用计算属性

## 规则

当类绑定涉及多个条件或复杂逻辑时，将它们提取到计算属性中，而不是在模板中编写内联表达式。

## 为什么这很重要

- 内联类表达式在多个条件下很快变得不可读
- 计算属性被缓存，并且仅在依赖项更改时重新评估
- 计算属性中的逻辑更容易测试和调试
- 保持模板专注于结构，而不是逻辑

## 错误代码

```vue
<template>
  <!-- 难以阅读，容易出错 -->
  <div :class="{
    'btn': true,
    'btn-primary': type === 'primary' && !disabled,
    'btn-secondary': type === 'secondary' && !disabled,
    'btn-disabled': disabled,
    'btn-loading': isLoading,
    'btn-large': size === 'large',
    'btn-small': size === 'small'
  }">
    {{ label }}
  </div>

  <!-- 更糟糕：字符串连接 -->
  <div :class="'btn btn-' + type + (disabled ? ' btn-disabled' : '') + (isLoading ? ' btn-loading' : '')">
    {{ label }}
  </div>
</template>
```

## 正确代码

```vue
<script setup>
import { computed } from 'vue'

const props = defineProps({
  type: { type: String, default: 'primary' },
  size: { type: String, default: 'medium' },
  disabled: Boolean,
  isLoading: Boolean,
  label: String
})

const buttonClasses = computed(() => ({
  'btn': true,
  [`btn-${props.type}`]: !props.disabled,
  'btn-disabled': props.disabled,
  'btn-loading': props.isLoading,
  'btn-large': props.size === 'large',
  'btn-small': props.size === 'small'
}))
</script>

<template>
  <div :class="buttonClasses">
    {{ label }}
  </div>
</template>
```

## 样式绑定也是如此

同样的原则适用于样式绑定：

```vue
<script setup>
import { computed } from 'vue'

const props = defineProps({
  color: String,
  fontSize: Number,
  isHighlighted: Boolean
})

const textStyles = computed(() => ({
  color: props.color,
  fontSize: `${props.fontSize}px`,
  backgroundColor: props.isHighlighted ? 'yellow' : 'transparent',
  fontWeight: props.isHighlighted ? 'bold' : 'normal'
}))
</script>

<template>
  <span :style="textStyles">Styled text</span>
</template>
```

## 组合静态和动态类

使用数组语法将静态类与计算动态类组合：

```vue
<script setup>
import { computed } from 'vue'

const dynamicClasses = computed(() => ({
  'is-active': isActive.value,
  'is-disabled': isDisabled.value
}))
</script>

<template>
  <!-- 静态 'card' 类 + 动态类 -->
  <div :class="['card', dynamicClasses]">
    Content
  </div>
</template>
```

## 参考

- [类和样式绑定](https://vuejs.org/guide/essentials/class-and-style.html)
- [计算属性](https://vuejs.org/guide/essentials/computed.html)
