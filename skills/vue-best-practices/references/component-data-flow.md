---
title: 组件数据流最佳实践
impact: HIGH
impactDescription: 组件之间清晰的数据流可防止状态错误、过时的 UI 和脆弱的耦合
type: best-practice
tags: [vue3, props, emits, v-model, provide-inject, data-flow, typescript]
---

# 组件数据流最佳实践

**影响: HIGH** - Vue 组件在数据流明确时保持可靠:props 向下,events 向上,`v-model` 处理双向绑定,provide/inject 支持跨树依赖。模糊这些边界会导致过时状态、隐藏耦合和难以调试的 UI。

Vue.js 中数据流的主要原则是 **Props 向下 / Events 向上**。这是最可维护的默认设置,单向流扩展性良好。

## 任务列表

- 将 props 视为只读输入
- 使用 props/emit 进行组件通信;将 refs 保留给命令式操作
- 当需要命令式 API 时,使用模板 refs 类型化 refs
- 发出事件而不是直接修改父状态
- 在现代 Vue(3.4+)中使用 `defineModel` 进行 v-model
- 在子组件中故意处理 v-model 修饰符
- 使用符号作为 provide/inject 键以避免 props 钻取(超过 ~3 层)
- 在提供者中保持变异或暴露显式操作
- 在 TypeScript 项目中,偏好基于类型的 `defineProps`、`defineEmits` 和 `InjectionKey`

## Props: 单向数据向下

Props 是输入。不要在子组件中修改它们。

**错误:**
```vue
<script setup>
const props = defineProps({ count: Number })

function increment() {
  props.count++
}
</script>
```

**正确:**

如果状态需要更改,发出事件,使用 `v-model` 或创建本地副本。

## 偏好 props/emit 而不是组件 refs

**错误:**
```vue
<script setup>
import { ref } from 'vue'
import UserForm from './UserForm.vue'

const formRef = ref(null)

function submitForm() {
  if (formRef.value.isValid) {
    formRef.value.submit()
  }
}
</script>

<template>
  <UserForm ref="formRef" />
  <button @click="submitForm">提交</button>
</template>
```

**正确:**
```vue
<script setup>
import UserForm from './UserForm.vue'

function handleSubmit(formData) {
  api.submit(formData)
}
</script>

<template>
  <UserForm @submit="handleSubmit" />
</template>
```

## 当需要命令式访问时类型化组件 refs

默认偏好 props/emits。当父级必须调用暴露的子方法时,显式类型化 ref 并仅从子组件暴露预期的 API,使用 `defineExpose`。

**错误:**
```vue
<script setup lang="ts">
import { ref, onMounted } from 'vue'
import DialogPanel from './DialogPanel.vue'

const panelRef = ref(null)

onMounted(() => {
  panelRef.value.open()
})
</script>

<template>
  <DialogPanel ref="panelRef" />
</template>
```

**正确:**
```vue
<!-- DialogPanel.vue -->
<script setup lang="ts">
function open() {}

defineExpose({ open })
</script>
```

```vue
<!-- Parent.vue -->
<script setup lang="ts">
import { onMounted, useTemplateRef } from 'vue'
import DialogPanel from './DialogPanel.vue'

// Vue 3.5+ 使用 useTemplateRef
const panelRef = useTemplateRef('panelRef')

// Vue 3.5 之前使用手动类型化和 ref
// const panelRef = ref<InstanceType<typeof DialogPanel> | null>(null)

onMounted(() => {
  panelRef.value?.open()
})
</script>

<template>
  <DialogPanel ref="panelRef" />
</template>
```

## Emits: 显式事件向上

组件事件不冒泡。如果父级需要了解事件,则显式重新发出。

**错误:**
```vue
<!-- 父级期望从孙组件获得 "saved",但它不会冒泡 -->
<Child @saved="onSaved" />
```

**正确:**
```vue
<!-- Child.vue -->
<script setup>
const emit = defineEmits(['saved'])

function onGrandchildSaved(payload) {
  emit('saved', payload)
}
</script>

<template>
  <Grandchild @saved="onGrandchildSaved" />
</template>
```

**事件命名:** 在模板中使用 kebab-case,在脚本中使用 camelCase:
```vue
<script setup>
const emit = defineEmits(['updateUser'])
</script>

<template>
  <ProfileForm @update-user="emit('updateUser', $event)" />
</template>
```

## `v-model`: 可预测的双向绑定

默认使用 `defineModel` 进行组件绑定并在输入时发出更新。仅当您在 Vue < 3.4 时使用 `modelValue` + `update:modelValue` 模式。

**错误:**
```vue
<script setup>
const props = defineProps({ value: String })
</script>

<template>
  <input :value="props.value" @input="$emit('input', $event.target.value)" />
</template>
```

**正确 (Vue 3.4+):**
```vue
<script setup>
const model = defineModel({ type: String })
</script>

<template>
  <input v-model="model" />
</template>
```

**正确 (Vue < 3.4):**
```vue
<script setup>
const props = defineProps({ modelValue: String })
const emit = defineEmits(['update:modelValue'])
</script>

<template>
  <input
    :value="props.modelValue"
    @input="emit('update:modelValue', $event.target.value)"
  />
</template>
```

如果您需要在更改后立即获得更新的值,请在父级中使用输入事件值或 `nextTick`。

## Provide/Inject: 无 Prop 钻取的共享上下文

对跨树状态使用 provide/inject,但在提供者中保持变异集中并暴露显式操作。

**错误:**
```vue
// Provider.vue
provide('theme', reactive({ dark: false }))

// Consumer.vue
const theme = inject('theme')
// 从任何深度变异共享状态变得难以跟踪
theme.dark = true
```

**正确:**
```vue
// Provider.vue
const theme = reactive({ dark: false })
const toggleTheme = () => { theme.dark = !theme.dark }

provide(themeKey, readonly(theme))
provide(themeActionsKey, { toggleTheme })

// Consumer.vue
const theme = inject(themeKey)
const { toggleTheme } = inject(themeActionsKey)
```

使用符号作为键以避免大型应用中的冲突:
```ts
export const themeKey = Symbol('theme')
export const themeActionsKey = Symbol('theme-actions')
```

## 使用 TypeScript 契约作为公共组件 API

在 TypeScript 项目中,使用 `defineProps`、`defineEmits` 和 `InjectionKey` 直接类型化组件边界,以便无效负载和不匹配的注入在编译时失败。

**错误:**
```vue
<script setup lang="ts">
import { inject } from 'vue'

const props = defineProps({
  userId: String
})

const emit = defineEmits(['save'])
const settings = inject('settings')

// 此处未检查负载形状
emit('save', 123)

// 键是基于字符串的,不是类型安全的
settings?.theme = 'dark'
</script>
```

**正确:**
```vue
<script setup lang="ts">
import { inject, provide } from 'vue'
import type { InjectionKey } from 'vue'

interface Props {
  userId: string
}

interface Emits {
  save: [payload: { id: string; draft: boolean }]
}

interface Settings {
  theme: 'light' | 'dark'
}

const settingsKey: InjectionKey<Settings> = Symbol('settings')

const props = defineProps<Props>()
const emit = defineEmits<Emits>()

provide(settingsKey, { theme: 'light' })

const settings = inject(settingsKey)
if (settings) {
  emit('save', { id: props.userId, draft: false })
}
</script>
```
