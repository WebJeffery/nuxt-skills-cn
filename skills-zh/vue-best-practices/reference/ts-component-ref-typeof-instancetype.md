---
title: 使用 InstanceType 类型化组件 Ref
impact: MEDIUM
impactDescription: 没有适当的类型，组件 ref 会丢失暴露方法和属性的类型信息
type: best-practice
tags: [vue3, typescript, template-refs, component-refs, InstanceType]
---

# 使用 InstanceType 类型化组件 Ref

**影响：中等** - 创建子 Vue 组件的 ref 时，使用 `InstanceType<typeof Component>` 来正确类型化 ref。这使 TypeScript 能够识别通过 `defineExpose` 暴露的方法和属性。

## 任务清单

- [ ] 使用 `InstanceType<typeof Component>` 类型化组件 ref
- [ ] 在类型联合中包含 `| null`
- [ ] 确保子组件使用 `defineExpose` 暴露可访问的方法
- [ ] 对泛型组件使用 `ComponentExposed` 辅助工具（Vue 3.5+）
- [ ] 当确切类型不可用时，考虑 `ComponentPublicInstance`

## 基本模式

```vue
<!-- ChildComponent.vue -->
<script setup lang="ts">
import { ref } from 'vue'

const internalCount = ref(0)

function increment() {
  internalCount.value++
}

function reset() {
  internalCount.value = 0
}

// 向父组件暴露方法/属性
defineExpose({
  increment,
  reset,
  count: internalCount  // 暴露 ref 以供读取
})
</script>
```

```vue
<!-- ParentComponent.vue -->
<script setup lang="ts">
import { ref, onMounted } from 'vue'
import ChildComponent from './ChildComponent.vue'

// 使用 InstanceType 类型化 ref
const childRef = ref<InstanceType<typeof ChildComponent> | null>(null)

onMounted(() => {
  // TypeScript 知道暴露的方法
  childRef.value?.increment()  // OK
  childRef.value?.reset()      // OK

  // 访问暴露的 ref
  console.log(childRef.value?.count)  // Ref<number>
})

function handleClick() {
  childRef.value?.increment()
}
</script>

<template>
  <ChildComponent ref="childRef" />
  <button @click="handleClick">增加子组件</button>
</template>
```

## Vue 3.5+: useTemplateRef

```vue
<script setup lang="ts">
import { useTemplateRef, onMounted } from 'vue'
import ChildComponent from './ChildComponent.vue'

// useTemplateRef 带组件类型
const childRef = useTemplateRef<InstanceType<typeof ChildComponent>>('child')

onMounted(() => {
  childRef.value?.increment()
})
</script>

<template>
  <ChildComponent ref="child" />
</template>
```

## 泛型组件

对于泛型组件，仅 `InstanceType` 无法捕获泛型参数。使用 `vue-component-type-helpers` 中的 `ComponentExposed`：

```vue
<!-- GenericList.vue -->
<script setup lang="ts" generic="T">
defineProps<{
  items: T[]
}>()

const selectedItem = ref<T | null>(null)

function selectItem(item: T) {
  selectedItem.value = item
}

defineExpose({
  selectedItem,
  selectItem
})
</script>
```

```vue
<!-- Parent.vue -->
<script setup lang="ts">
import { useTemplateRef } from 'vue'
import type { ComponentExposed } from 'vue-component-type-helpers'
import GenericList from './GenericList.vue'

interface User {
  id: string
  name: string
}

// ComponentExposed 保留泛型类型
const listRef = useTemplateRef<ComponentExposed<typeof GenericList<User>>>('list')

function selectFirstUser() {
  const users: User[] = [{ id: '1', name: 'John' }]
  listRef.value?.selectItem(users[0])  // 正确类型化为 User
}
</script>

<template>
  <GenericList ref="list" :items="users" />
</template>
```

## 当确切类型不可用时

当您无法访问组件的暴露类型时，使用 `ComponentPublicInstance`：

```vue
<script setup lang="ts">
import { ref } from 'vue'
import type { ComponentPublicInstance } from 'vue'
import SomeThirdPartyComponent from 'third-party-lib'

// 后备方案：ComponentPublicInstance 提供对 $el、$refs 等的访问
const thirdPartyRef = ref<ComponentPublicInstance | null>(null)

function getElement() {
  // 可以访问标准 Vue 实例属性
  return thirdPartyRef.value?.$el
}
</script>

<template>
  <SomeThirdPartyComponent ref="thirdPartyRef" />
</template>
```

## 带有 v-for 的多个 Ref

```vue
<script setup lang="ts">
import { ref, onMounted } from 'vue'
import ListItem from './ListItem.vue'

type ListItemInstance = InstanceType<typeof ListItem>

const items = ref([
  { id: 1, name: 'Item 1' },
  { id: 2, name: 'Item 2' }
])

// 组件 ref 数组
const itemRefs = ref<(ListItemInstance | null)[]>([])

function setItemRef(el: ListItemInstance | null, index: number) {
  itemRefs.value[index] = el
}

function focusItem(index: number) {
  itemRefs.value[index]?.focus()
}
</script>

<template>
  <ListItem
    v-for="(item, index) in items"
    :key="item.id"
    :item="item"
    :ref="(el) => setItemRef(el, index)"
  />
</template>
```

## 提取暴露类型以供重用

```typescript
// types/components.ts
import type ChildComponent from '@/components/ChildComponent.vue'

// 创建可重用的类型别名
export type ChildComponentInstance = InstanceType<typeof ChildComponent>

// 用于组合式函数或其他文件
export type ChildComponentRef = Ref<ChildComponentInstance | null>
```

```vue
<script setup lang="ts">
import { ref } from 'vue'
import type { ChildComponentRef } from '@/types/components'
import ChildComponent from '@/components/ChildComponent.vue'

const childRef: ChildComponentRef = ref(null)
</script>
```

## 常见错误：忘记 defineExpose

```vue
<!-- Child.vue - 错误：没有 defineExpose -->
<script setup lang="ts">
function doSomething() {
  console.log('doing something')
}

// 忘记 defineExpose！
</script>
```

```vue
<!-- Parent.vue -->
<script setup lang="ts">
const childRef = ref<InstanceType<typeof Child> | null>(null)

onMounted(() => {
  // childRef.value?.doSomething 是 undefined！
  // Script setup 组件默认不暴露任何内容
  childRef.value?.doSomething()  // 运行时：undefined is not a function
})
</script>
```

在 `<script setup>` 组件中始终使用 `defineExpose` 以使方法/属性可被父组件 ref 访问。

## 参考
- [Vue.js TypeScript - 组件模板 Ref](https://vuejs.org/guide/typescript/composition-api.html#typing-component-template-refs)
- [Vue.js 模板 Ref](https://vuejs.org/guide/essentials/template-refs.html#ref-on-component)
- [vue-component-type-helpers](https://www.npmjs.com/package/vue-component-type-helpers)
