---
title: 首选 Props 和 Emit 而不是组件 Refs
impact: MEDIUM
impactDescription: 组件 refs 创建紧密耦合并破坏组件抽象
type: best-practice
tags: [vue3, component-refs, props, emit, component-design, architecture]
---

# 首选 Props 和 Emit 而不是组件 Refs

**影响：中** - 使用模板 refs 访问子组件内部会在父组件和子组件之间创建紧密耦合。这使得组件更难维护、重构和重用。Props 和 emit 提供了更清晰的基于约定的 API，保留组件封装。

组件 refs 应该保留给无法以声明方式表达的命令式操作（focus、scroll、动画）。

## 任务清单

- [ ] 使用 props 将数据传递给子组件
- [ ] 使用 emit 向父组件传达事件
- [ ] 仅对命令式 DOM 操作使用组件 refs
- [ ] 如果使用 refs，通过 defineExpose 公开最小、记录的 API
- [ ] 考虑是否可以以声明方式表达交互

**错误：**
```vue
<!-- ParentComponent.vue -->
<script setup>
import { ref, onMounted } from 'vue'
import UserForm from './UserForm.vue'

const formRef = ref(null)

// 错误：深入子组件的内部
function submitForm() {
  // 紧密耦合 - 父组件知道子组件的内部结构
  if (formRef.value.isValid) {
    const data = formRef.value.formData
    formRef.value.setSubmitting(true)
    api.submit(data).then(() => {
      formRef.value.setSubmitting(false)
      formRef.value.reset()
    })
  }
}

// 错误：父组件管理子组件的状态
function prefillForm(userData) {
  formRef.value.formData.name = userData.name
  formRef.value.formData.email = userData.email
}
</script>

<template>
  <UserForm ref="formRef" />
  <button @click="submitForm">Submit</button>
</template>
```

```vue
<!-- UserForm.vue - 暴露太多 -->
<script setup>
import { ref, reactive } from 'vue'

const formData = reactive({ name: '', email: '' })
const isValid = ref(false)
const isSubmitting = ref(false)

function setSubmitting(value) {
  isSubmitting.value = value
}

function reset() {
  formData.name = ''
  formData.email = ''
}

// 错误：暴露内部状态细节
defineExpose({
  formData,
  isValid,
  isSubmitting,
  setSubmitting,
  reset
})
</script>
```

**正确：**
```vue
<!-- ParentComponent.vue -->
<script setup>
import { ref } from 'vue'
import UserForm from './UserForm.vue'

const initialData = ref({ name: '', email: '' })
const isSubmitting = ref(false)

// 正确：子组件通过事件通信
function handleSubmit(formData) {
  isSubmitting.value = true
  api.submit(formData).finally(() => {
    isSubmitting.value = false
  })
}

function handleValidChange(isValid) {
  console.log('表单有效性：', isValid)
}
</script>

<template>
  <!-- 正确：Props 向下，事件向上 -->
  <UserForm
    :initial-data="initialData"
    :submitting="isSubmitting"
    @submit="handleSubmit"
    @valid-change="handleValidChange"
  />
</template>
```

```vue
<!-- UserForm.vue - 干净的 props/emit 接口 -->
<script setup>
import { reactive, computed, watch } from 'vue'

const props = defineProps({
  initialData: { type: Object, default: () => ({}) },
  submitting: { type: Boolean, default: false }
})

const emit = defineEmits(['submit', 'valid-change'])

const formData = reactive({ ...props.initialData })

const isValid = computed(() => {
  return formData.name.length > 0 && formData.email.includes('@')
})

watch(isValid, (valid) => {
  emit('valid-change', valid)
})

function handleSubmit() {
  if (isValid.value) {
    emit('submit', { ...formData })
  }
}
</script>

<template>
  <form @submit.prevent="handleSubmit">
    <input v-model="formData.name" :disabled="submitting" />
    <input v-model="formData.email" :disabled="submitting" />
    <button type="submit" :disabled="!isValid || submitting">
      {{ submitting ? '提交中...' : '提交' }}
    </button>
  </form>
</template>
```

## 何时组件 Refs 是合适的

```vue
<!-- 正确：用于命令式 DOM 操作的 Refs -->
<script setup>
import { ref } from 'vue'
import CustomInput from './CustomInput.vue'

const inputRef = ref(null)

// 命令式 focus 操作 - refs 的良好使用
function focusInput() {
  inputRef.value?.focus()
}
</script>

<template>
  <CustomInput ref="inputRef" v-model="text" />
  <button @click="focusInput">聚焦输入</button>
</template>
```

```vue
<!-- CustomInput.vue - 最小命令式 API -->
<script setup>
import { ref } from 'vue'

const inputEl = ref(null)

// 仅暴露命令式方法
defineExpose({
  focus: () => inputEl.value?.focus(),
  blur: () => inputEl.value?.blur(),
  select: () => inputEl.value?.select()
})
</script>

<template>
  <input ref="inputEl" v-bind="$attrs" />
</template>
```

## 总结

| 用例 | 方法 |
|----------|----------|
| 将数据传递给子组件 | Props |
| 子组件通知父组件 | 发出事件 |
| 双向绑定 | v-model (props + emit) |
| 聚焦、滚动、动画 | 带有最小暴露的组件 ref |
| 访问子组件内部状态 | 重构以使用 props/emit |

## 参考
- [Vue.js 组件基础 - Props](https://vuejs.org/guide/components/props.html)
- [Vue.js 组件事件](https://vuejs.org/guide/components/events.html)
- [Vue.js 模板 Refs - 组件上的 Ref](https://vuejs.org/guide/essentials/template-refs.html#ref-on-component)
