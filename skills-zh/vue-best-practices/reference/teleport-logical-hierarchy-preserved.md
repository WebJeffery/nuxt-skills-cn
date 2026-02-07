---
title: Teleport 保留逻辑组件层次结构
impact: LOW
impactDescription: 理解 Teleport 只改变 DOM 结构，而不是 Vue 的组件树
type: best-practice
tags: [vue3, teleport, component-hierarchy, props, events]
---

# Teleport 保留逻辑组件层次结构

**影响: LOW** - 一个常见的误解是 Teleport 破坏了 Vue 组件关系。实际上，`<Teleport>` 只改变渲染的 DOM 结构，而不是逻辑层次结构。Props、事件、provide/inject 和 Vue Devtools 都像组件没有被传送一样工作。

## 任务清单

- [ ] 继续正常使用 props 和事件与传送组件
- [ ] 跨传送边界使用 provide/inject
- [ ] 检查 Vue Devtools 中的组件位置（显示逻辑父元素，而不是 DOM 位置）

**关键概念:**
```vue
<!-- ParentComponent.vue -->
<template>
  <div class="parent">
    <Teleport to="body">
      <!-- ChildComponent 在逻辑上仍然是 ParentComponent 的子元素 -->
      <!-- 即使它在 <body> 中渲染 -->
      <ChildComponent
        :message="parentMessage"
        @update="handleUpdate"
      />
    </Teleport>
  </div>
</template>

<script setup>
import { provide, ref } from 'vue'

const parentMessage = ref('来自父元素的问候')

// Provide 仍然跨传送工作
provide('theme', 'dark')

function handleUpdate(value) {
  // 事件通过逻辑层次结构冒泡，而不是 DOM
  console.log('从传送子元素接收到:', value)
}
</script>
```

```vue
<!-- ChildComponent.vue -->
<template>
  <div class="modal">
    <p>{{ message }}</p>
    <p>主题: {{ theme }}</p>
    <button @click="$emit('update', '新值')">更新</button>
  </div>
</template>

<script setup>
import { inject } from 'vue'

// Props 正常工作
defineProps(['message'])

// Inject 跨传送边界工作
const theme = inject('theme')

// Emit 正常工作
defineEmits(['update'])
</script>
```

## Teleport 改变与保留的内容

| 方面 | 改变? | 说明 |
|--------|----------|-------|
| DOM 位置 | 是 | 内容移动到 `to` 目标 |
| CSS 继承 | 是 | 样式从新的 DOM 父元素继承 |
| Props | 否 | 完全像没有 Teleport 一样工作 |
| 事件 (emit) | 否 | 通过逻辑层次结构冒泡 |
| Provide/Inject | 否 | 跨传送边界工作 |
| Vue Devtools | 否 | 显示逻辑组件树 |
| 插槽 | 否 | 正常工作 |
| 模板引用 | 否 | 父元素可以引用传送内容 |

## 实际示例: 带有表单的模态框

```vue
<!-- ModalForm.vue -->
<template>
  <Teleport to="body">
    <div v-if="visible" class="modal-overlay">
      <form @submit.prevent="handleSubmit" class="modal-form">
        <!-- 插槽内容接收父元素的作用域 -->
        <slot :formData="formData" />

        <button type="submit">提交</button>
        <button type="button" @click="$emit('close')">取消</button>
      </form>
    </div>
  </Teleport>
</template>

<script setup>
import { reactive } from 'vue'

defineProps(['visible'])
const emit = defineEmits(['close', 'submit'])

const formData = reactive({})

function handleSubmit() {
  emit('submit', formData)
}
</script>
```

```vue
<!-- ParentPage.vue -->
<template>
  <button @click="showModal = true">添加用户</button>

  <!-- 事件和插槽按预期工作，尽管有传送 -->
  <ModalForm
    :visible="showModal"
    @close="showModal = false"
    @submit="handleFormSubmit"
  >
    <template #default="{ formData }">
      <input v-model="formData.name" placeholder="名称" />
      <input v-model="formData.email" placeholder="电子邮件" />
    </template>
  </ModalForm>
</template>

<script setup>
import { ref } from 'vue'

const showModal = ref(false)

function handleFormSubmit(data) {
  console.log('表单已提交:', data)
  showModal.value = false
}
</script>
```

## Vue Devtools 行为

在 Vue Devtools 中，传送的组件出现在其逻辑父元素下:

```
App
└── ParentPage
    └── ModalForm        <-- 显示在这里（逻辑父元素）
        └── 表单内容
```

而不是在其 DOM 位置下:
```
// 这不是它在 Devtools 中的显示方式
body
└── ModalForm  <-- 不显示在这里
```

## 参考
- [Vue.js Teleport - 组件层次结构](https://vuejs.org/guide/built-ins/teleport.html#basic-usage)
