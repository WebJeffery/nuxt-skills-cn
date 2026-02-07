---
title: 模板 Ref 在挂载之前为 Null
impact: HIGH
impactDescription: 在挂载之前或卸载之后访问模板 ref 会导致运行时错误
type: gotcha
tags: [vue3, typescript, template-refs, lifecycle, null-safety]
---

# 模板 Ref 在挂载之前为 Null

**影响：高** - 模板 ref 的初始值为 `null`，并且直到组件挂载之前都保持为 null。如果被引用的元素被 `v-if` 移除，它们也可能再次变为 null。在 TypeScript 中始终使用联合类型和可选链来处理这个问题。

## 任务清单

- [ ] 始终使用 `| null` 联合类型化模板 ref
- [ ] 仅在 `onMounted` 内部或之后访问 ref
- [ ] 访问 ref 属性时使用可选链（`?.`）
- [ ] 处理 ref 可能再次变为 null 的 `v-if` 场景
- [ ] 考虑在 Vue 3.5+ 中使用 `useTemplateRef`

## 问题

```vue
<script setup lang="ts">
import { ref } from 'vue'

// 错误：没有考虑 null
const inputRef = ref<HTMLInputElement>()

// 错误：如果在挂载之前访问会崩溃
inputRef.value.focus()  // 错误：无法读取 null 的属性

// 错误：在 setup 中访问，元素还不存在
console.log(inputRef.value.value)  // 错误！
</script>

<template>
  <input ref="inputRef" />
</template>
```

## 解决方案

```vue
<script setup lang="ts">
import { ref, onMounted } from 'vue'

// 正确：在类型中包含 null
const inputRef = ref<HTMLInputElement | null>(null)

// 正确：在 onMounted 中访问，当 DOM 存在时
onMounted(() => {
  inputRef.value?.focus()  // 使用可选链安全
})

// 正确：在访问之前保护
function focusInput() {
  if (inputRef.value) {
    inputRef.value.focus()
  }
}
</script>

<template>
  <input ref="inputRef" />
</template>
```

## Vue 3.5+：useTemplateRef

Vue 3.5 引入了 `useTemplateRef`，具有更好的类型推断：

```vue
<script setup lang="ts">
import { useTemplateRef, onMounted } from 'vue'

// 类型自动推断静态 ref
const inputRef = useTemplateRef<HTMLInputElement>('input')

onMounted(() => {
  inputRef.value?.focus()
})
</script>

<template>
  <input ref="input" />
</template>
```

## 处理 v-if 场景

当元素有条件渲染时，ref 可能变为 `null`：

```vue
<script setup lang="ts">
import { ref, watch } from 'vue'

const showModal = ref(false)
const modalRef = ref<HTMLDivElement | null>(null)

// 错误：假设 ref 在首次挂载后始终存在
function closeModal() {
  modalRef.value.classList.remove('open')  // 可能为 null！
}

// 正确：始终保护访问
function closeModal() {
  modalRef.value?.classList.remove('open')
}

// 正确：监视 ref 更改
watch(modalRef, (newRef) => {
  if (newRef) {
    // 模态框元素刚刚挂载
    newRef.focus()
  }
  // 如果为 null，模态框已卸载
})
</script>

<template>
  <div v-if="showModal" ref="modalRef" class="modal">
    模态框内容
  </div>
</template>
```

## 组件 Refs

对于组件 refs，使用 `InstanceType`：

```vue
<script setup lang="ts">
import { ref, onMounted } from 'vue'
import ChildComponent from './ChildComponent.vue'

// 带有 null 的组件 ref
const childRef = ref<InstanceType<typeof ChildComponent> | null>(null)

onMounted(() => {
  // 访问暴露的方法/属性
  childRef.value?.exposedMethod()
})
</script>

<template>
  <ChildComponent ref="childRef" />
</template>
```

记住：子组件必须使用 `defineExpose` 来暴露方法：

```vue
<!-- ChildComponent.vue -->
<script setup lang="ts">
function exposedMethod() {
  console.log('从父组件调用')
}

defineExpose({
  exposedMethod
})
</script>
```

## 使用 v-for 的多个 Refs

```vue
<script setup lang="ts">
import { ref, onMounted } from 'vue'

const items = ref(['a', 'b', 'c'])

// 用于 v-for 的 ref 数组
const itemRefs = ref<(HTMLLIElement | null)[]>([])

onMounted(() => {
  // 访问特定项目
  itemRefs.value[0]?.focus()

  // 安全地迭代
  itemRefs.value.forEach(el => {
    el?.classList.add('mounted')
  })
})
</script>

<template>
  <ul>
    <li
      v-for="(item, index) in items"
      :key="item"
      :ref="el => { itemRefs[index] = el as HTMLLIElement }"
    >
      {{ item }}
    </li>
  </ul>
</template>
```

## 异步操作和 Refs

对异步操作要小心：

```vue
<script setup lang="ts">
import { ref, onMounted } from 'vue'

const containerRef = ref<HTMLDivElement | null>(null)

onMounted(async () => {
  // containerRef.value 在这里存在

  await fetchData()

  // 注意：组件可能在 await 期间已卸载
  // 在访问之前始终重新检查
  if (containerRef.value) {
    containerRef.value.scrollTop = 0
  }
})
</script>
```

## 类型保护模式

创建可重用的类型保护以获得更简洁的代码：

```typescript
// utils/refs.ts
export function assertRef<T>(
  ref: Ref<T | null>,
  message = 'Ref 不可用'
): asserts ref is Ref<T> {
  if (ref.value === null) {
    throw new Error(message)
  }
}

// 在组件中使用
function mustFocus() {
  assertRef(inputRef, '输入元素未挂载')
  inputRef.value.focus() // TypeScript 在这里知道它不是 null
}
```

## 参考
- [Vue.js TypeScript 与 Composition API - 模板 Refs](https://vuejs.org/guide/typescript/composition-api.html#typing-template-refs)
- [Vue.js 模板 Refs](https://vuejs.org/guide/essentials/template-refs.html)
