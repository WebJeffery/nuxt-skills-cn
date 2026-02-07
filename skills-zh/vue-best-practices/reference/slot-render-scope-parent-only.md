---
title: 插槽内容只能访问父组件作用域
impact: HIGH
impactDescription: 尝试在插槽内容中访问子组件数据会导致未定义值或错误
type: gotcha
tags: [vue3, slots, scope, reactivity, common-mistake]
---

# 插槽内容只能访问父组件作用域

**影响：高** - 插槽内容在父组件的作用域中编译，无法访问在子组件中定义的数据。这遵循 JavaScript 的词法作用域规则，是一个常见的混淆来源。

当你为插槽提供内容时，该内容在父模板中定义，只能访问父组件中可用的数据。子组件的内部状态不可访问，除非通过作用域插槽显式传递。

## 任务清单

- [ ] 记住插槽内容在父作用域中编译
- [ ] 永远不要尝试在插槽内容中直接访问子组件数据
- [ ] 当需要向父组件暴露子数据时使用作用域插槽
- [ ] 检查所有模板表达式是否引用当前组件中可用的数据

**错误：**
```vue
<!-- Parent.vue -->
<script setup>
import SubmitButton from './SubmitButton.vue'
</script>

<template>
  <!-- 错误：尝试访问子组件的 buttonText - 这将是未定义的 -->
  <SubmitButton>{{ buttonText }}</SubmitButton>

  <!-- 错误：尝试访问子组件的 isLoading 状态 -->
  <SubmitButton>
    <span v-if="isLoading">加载中...</span>
    <span v-else>提交</span>
  </SubmitButton>
</template>
```

```vue
<!-- SubmitButton.vue (子组件) -->
<script setup>
import { ref } from 'vue'

const buttonText = ref('点击我')  // 在父组件的插槽内容中不可访问
const isLoading = ref(false)        // 在父组件的插槽内容中不可访问
</script>

<template>
  <button>
    <slot></slot>
  </button>
</template>
```

**正确 - 使用作用域插槽：**
```vue
<!-- SubmitButton.vue (子组件) - 通过插槽属性暴露数据 -->
<script setup>
import { ref } from 'vue'

const buttonText = ref('点击我')
const isLoading = ref(false)
</script>

<template>
  <button>
    <!-- 将子数据作为插槽属性传递 -->
    <slot :text="buttonText" :loading="isLoading"></slot>
  </button>
</template>
```

```vue
<!-- Parent.vue -->
<script setup>
import SubmitButton from './SubmitButton.vue'
</script>

<template>
  <!-- 正确：通过作用域插槽接收子数据 -->
  <SubmitButton v-slot="{ text, loading }">
    <span v-if="loading">加载中...</span>
    <span v-else>{{ text }}</span>
  </SubmitButton>
</template>
```

**正确 - 使用父组件数据：**
```vue
<!-- Parent.vue -->
<script setup>
import { ref } from 'vue'
import SubmitButton from './SubmitButton.vue'

// 在编译插槽内容的父组件中定义数据
const message = ref('提交表单')
const isSubmitting = ref(false)
</script>

<template>
  <!-- 正确：在插槽内容中使用父组件自己的数据 -->
  <SubmitButton>
    <span v-if="isSubmitting">处理中...</span>
    <span v-else>{{ message }}</span>
  </SubmitButton>
</template>
```

## 函数类比

将插槽视为函数参数：

```javascript
// 插槽内容就像在父作用域中定义的回调函数
function Parent() {
  const parentData = '你好'

  // 这个回调函数只能看到 parentData，看不到 childData
  Child((slotProps) => {
    return parentData + (slotProps?.text || '')
  })
}

function Child(slotCallback) {
  const childData = '世界'  // 对回调函数不可见

  // 必须通过插槽属性显式传递数据
  return slotCallback({ text: childData })
}
```

## 参考
- [Vue.js 插槽 - 渲染作用域](https://vuejs.org/guide/components/slots.html#render-scope)
