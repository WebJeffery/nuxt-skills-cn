---
title: Boolean Props 默认为 false，而非 undefined
impact: MEDIUM
impactDescription: TypeScript 期望可选 boolean 为 undefined，但 Vue 将其默认为 false，导致类型混淆
type: gotcha
tags: [vue3, typescript, props, boolean, defineProps]
---

# Boolean Props 默认为 false，而非 undefined

**影响：中等** - 使用基于类型的 `defineProps` 时，可选的 boolean props（用 `?` 标记）的行为与 TypeScript 期望的不同。Vue 对 boolean props 有特殊处理：缺失的 boolean prop 默认为 `false`，而不是 `undefined`。当 TypeScript 认为类型是 `boolean | undefined` 时，这可能导致混淆。

## 任务清单

- [ ] 理解 Vue 的 boolean 强制转换使缺失的 boolean 变为 `false`
- [ ] 使用 `withDefaults()` 明确 boolean 默认值
- [ ] 如果 `undefined` 是有意义的状态，考虑使用非 boolean 类型
- [ ] 为您的团队记录此 Vue 特定行为

## 陷阱

```vue
<script setup lang="ts">
interface Props {
  disabled?: boolean  // TypeScript 看到的是：boolean | undefined
}

const props = defineProps<Props>()

// TypeScript 认为 props.disabled 可能是 undefined
if (props.disabled === undefined) {
  console.log('这永远不会运行！')
  // Vue 的 boolean 强制转换意味着 disabled 是 false，不是 undefined
}
</script>

<template>
  <!-- 不使用 prop 时 -->
  <MyComponent />
  <!-- disabled 是 false，而不是 undefined -->
</template>
```

## 为什么会发生这种情况

Vue 有继承自 HTML boolean 属性的特殊"boolean 强制转换"行为：

```vue
<!-- 所有这些都使 disabled = true -->
<MyComponent disabled />
<MyComponent :disabled="true" />
<MyComponent disabled="" />

<!-- 这使 disabled = false（不是 undefined） -->
<MyComponent />

<!-- 显式 false -->
<MyComponent :disabled="false" />
```

这是设计上的，以匹配 HTML 的工作方式：
```html
<!-- HTML：存在意味着 true，不存在意味着 false -->
<button disabled>无法点击</button>
<button>可以点击</button>
```

## 解决方案

### 解决方案 1：使用 withDefaults 明确意图

使您的意图清晰：

```vue
<script setup lang="ts">
interface Props {
  disabled?: boolean
}

// 明确记录默认值
const props = withDefaults(defineProps<Props>(), {
  disabled: false  // 现在很清楚这默认为 false
})
</script>
```

### 解决方案 2：使用三态类型

如果您实际上需要区分"未设置"和"显式 false"：

```vue
<script setup lang="ts">
interface Props {
  // 使用联合类型而不是可选 boolean
  state?: 'enabled' | 'disabled' | undefined

  // 或显式使用 undefined
  toggleState?: boolean | undefined
}

const props = withDefaults(defineProps<Props>(), {
  state: undefined,  // 实际上可以是 undefined
  toggleState: undefined
})

// 现在您可以检查 undefined
if (props.state === undefined) {
  // 使用父组件的状态
} else if (props.state === 'disabled') {
  // 显式禁用
}
</script>
```

### 解决方案 3：使用 null 表示"未设置"

```vue
<script setup lang="ts">
interface Props {
  // null = 未设置，false = 显式关闭，true = 显式开启
  selected: boolean | null
}

const props = withDefaults(defineProps<Props>(), {
  selected: null
})

// 三种不同的状态
if (props.selected === null) {
  console.log('未指定选择')
} else if (props.selected) {
  console.log('已选择')
} else {
  console.log('显式未选择')
}
</script>
```

## Boolean 强制转换顺序

当 Boolean 和 String 都有效时，Vue 也有特殊行为：

```typescript
// 运行时声明中的顺序很重要！
defineProps({
  // Boolean 优先：空字符串变为 true
  disabled: [Boolean, String]
})

// <MyComponent disabled /> → disabled = true
// <MyComponent disabled="" /> → disabled = true
```

```typescript
defineProps({
  // String 优先：空字符串保持为 string
  disabled: [String, Boolean]
})

// <MyComponent disabled /> → disabled = ''
// <MyComponent disabled="" /> → disabled = ''
```

使用基于类型的声明时，Boolean 对于缺失的 props 始终优先。

## 常见 Bug 模式

```vue
<!-- Parent.vue -->
<script setup lang="ts">
const userPreferences = ref({
  darkMode: undefined as boolean | undefined
})

// 获取偏好设置...
onMounted(async () => {
  userPreferences.value = await fetchPreferences()
})
</script>

<template>
  <!-- Bug：undefined 变为 false，而不是"继承系统偏好" -->
  <ThemeToggle :darkMode="userPreferences.darkMode" />
</template>
```

**修复：**

```vue
<script setup lang="ts">
const userPreferences = ref<{
  darkMode: boolean | null
}>({
  darkMode: null  // 使用 null 表示"尚未加载"
})
</script>

<template>
  <!-- 现在 ThemeToggle 可以区分 null 和 false -->
  <ThemeToggle :darkMode="userPreferences.darkMode" />
</template>
```

## TypeScript 类型准确性

Vue 类型系统处理了这个问题，但可能会令人困惑：

```typescript
interface Props {
  disabled?: boolean
}

const props = defineProps<Props>()

// 编译时：boolean | undefined
// 运行时：boolean（由于 Vue 的 boolean 强制转换，永远不会是 undefined）

// TypeScript 在这里技术上是"错误"的，但 withDefaults 用法
// 或显式 false 默认值可以帮助对齐期望
```

## 参考
- [Vue.js Props - Boolean Casting](https://vuejs.org/guide/components/props.html#boolean-casting)
- [GitHub Issue: Boolean props default to false](https://github.com/vuejs/core/issues/8576)
- [TypeScript Vue 3 Props](https://madewithlove.com/blog/typescript-vue-3-and-strongly-typed-props/)
