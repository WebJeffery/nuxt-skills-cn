---
title: 避免在组件上使用自定义指令
impact: HIGH
impactDescription: 多根组件上的自定义指令会被静默忽略，导致意外行为
type: gotcha
tags: [vue3, directives, components, multi-root, best-practices]
---

# 避免在组件上使用自定义指令

**影响：高** - 不建议在组件上使用自定义指令，这可能导致意外行为。当应用于多根组件时，指令将被忽略并抛出警告。与属性不同，指令不能通过 `v-bind="$attrs"` 传递给不同的元素。

自定义指令是为原生 HTML 元素上的直接 DOM 操作设计的，而不是 Vue 组件。

## 任务清单

- [ ] 仅将自定义指令应用于原生 HTML 元素，而不是组件
- [ ] 如果组件需要类似指令的行为，考虑将其作为组件 API 的一部分
- [ ] 对于组件，使用 props 和事件而不是指令
- [ ] 如果必须在组件上使用指令，请确保它具有单个根元素

**错误示例：**
```vue
<template>
  <!-- 错误：组件上的指令 - 可能被忽略 -->
  <MyComponent v-focus />

  <!-- 错误：多根组件 - 指令被忽略并警告 -->
  <MultiRootComponent v-highlight />
</template>

<script setup>
import MyComponent from './MyComponent.vue'
import MultiRootComponent from './MultiRootComponent.vue'

// MultiRootComponent.vue 有：
// <template>
//   <div>第一个根</div>
//   <div>第二个根</div>
// </template>
</script>
```

**正确示例：**
```vue
<template>
  <!-- 正确：原生 HTML 元素上的指令 -->
  <input v-focus />

  <!-- 正确：使用 props/events 实现组件行为 -->
  <MyComponent :should-focus="true" />

  <!-- 正确：如果需要指令，将组件包装在元素中 -->
  <div v-highlight>
    <MyComponent />
  </div>
</template>

<script setup>
import MyComponent from './MyComponent.vue'
</script>
```

## 何时组件上的指令有效

指令仅对具有**单个根元素**的组件有效工作。指令应用于根节点，类似于透传属性：

```vue
<!-- SingleRootComponent.vue -->
<template>
  <div>我是唯一的根</div>
</template>

<!-- Parent.vue -->
<template>
  <!-- 这有效，因为 SingleRootComponent 有一个根 -->
  <SingleRootComponent v-my-directive />
</template>
```

然而，这仍然不推荐，因为：
1. 它很脆弱 - 重构为多根会静默地破坏指令
2. 不清楚哪个元素接收指令
3. 组件作者可能不期望外部 DOM 操作

## 更好的模式

### 选项 1：组件 Prop
```vue
<!-- FocusableInput.vue -->
<template>
  <input ref="inputRef" v-bind="$attrs" />
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'

const props = defineProps({
  autofocus: Boolean
})

const inputRef = ref(null)

onMounted(() => {
  if (props.autofocus) {
    inputRef.value?.focus()
  }
})
</script>

<!-- 用法 -->
<FocusableInput autofocus />
```

### 选项 2：暴露方法
```vue
<!-- FocusableInput.vue -->
<template>
  <input ref="inputRef" />
</template>

<script setup>
import { ref } from 'vue'

const inputRef = ref(null)

const focus = () => inputRef.value?.focus()

defineExpose({ focus })
</script>

<!-- Parent.vue -->
<template>
  <FocusableInput ref="myInput" />
</template>

<script setup>
import { ref, onMounted } from 'vue'

const myInput = ref(null)

onMounted(() => {
  myInput.value?.focus()
})
</script>
```

## 参考
- [Vue.js 自定义指令 - 在组件上使用](https://vuejs.org/guide/reusability/custom-directives#usage-on-components)
