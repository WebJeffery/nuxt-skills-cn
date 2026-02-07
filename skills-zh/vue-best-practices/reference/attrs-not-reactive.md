---
title: useAttrs() 对象不是响应式的
impact: MEDIUM
impactDescription: 直接监视 attrs 不会触发 - 使用 onUpdated() 或转换为 props
type: gotcha
tags: [vue3, attrs, reactivity, composition-api]
---

# useAttrs() 对象不是响应式的

**影响: 中等** - `useAttrs()` 返回的对象不是响应式的。虽然它始终反映最新的透传属性，但您不能使用 `watch()` 或 `watchEffect()` 来观察其更改。对 attrs 属性的监视器在属性更改时不会触发。

## 任务清单

- [ ] 切勿使用 `watch()` 来观察 attrs 更改 - 它不会触发
- [ ] 使用 `onUpdated()` 生命周期钩子来处理基于 attrs 的副作用
- [ ] 如果需要响应式，将频繁访问的 attrs 转换为 props
- [ ] 记住 attrs 在模板和事件处理程序中始终是当前的

**不正确:**
```vue
<script setup>
import { watch, useAttrs } from 'vue'

const attrs = useAttrs()

// 错误: 当 attrs 更改时，此监视器永远不会触发！
watch(
  () => attrs.someAttr,
  (newValue) => {
    console.log('属性已更改:', newValue)
    // 此回调在属性更改时从不运行
  }
)

// 错误: watchEffect 也不跟踪 attrs
watchEffect(() => {
  console.log(attrs.class) // 仅在挂载时运行，class 更改时不运行
})
</script>
```

**正确:**
```vue
<script setup>
import { onUpdated, useAttrs } from 'vue'

const attrs = useAttrs()

// 正确: 使用 onUpdated 来访问最新的 attrs
onUpdated(() => {
  console.log('当前 attrs:', attrs)
  // 在此处使用最新的 attrs 执行副作用
})
</script>
```

```vue
<script setup>
import { watch } from 'vue'

// 正确: 如果需要响应式，将其声明为 prop
const props = defineProps({
  someAttr: String
})

// 现在可以监视它
watch(
  () => props.someAttr,
  (newValue) => {
    console.log('属性已更改:', newValue)
  }
)
</script>
```

## 为什么 Attrs 不是响应式的

Vue 官方文档指出：

> "注意，虽然这里的 attrs 对象始终反映最新的透传属性，但它不是响应式的（出于性能原因）。您不能使用监视器来观察其更改。"

这是一个为了性能的刻意设计决策 - 使 attrs 响应式会给每个使用透传属性的组件增加开销。

## Attrs 在何处反映当前值

尽管不是响应式的，attrs 在以下上下文中始终具有当前值：

```vue
<script setup>
import { useAttrs } from 'vue'

const attrs = useAttrs()

// 在事件处理程序中 - 处理程序执行时 attrs 是当前的
function handleClick() {
  console.log(attrs.class) // 点击时的当前值
}
</script>

<template>
  <!-- 在模板中 - 始终当前 -->
  <div :class="attrs.class">
    {{ attrs.title }}
  </div>

  <!-- 使用 v-bind 展开所有 attrs -->
  <input v-bind="attrs" />
</template>
```

## 使用 Attrs 的计算属性

引用 attrs 的计算属性会在组件重新渲染时更新：

```vue
<script setup>
import { computed, useAttrs } from 'vue'

const attrs = useAttrs()

// 这会更新 - 因为计算在渲染时重新评估
const hasCustomClass = computed(() => {
  return !!attrs.class
})
</script>

<template>
  <div v-bind="attrs">
    <span v-if="hasCustomClass">具有自定义样式</span>
  </div>
</template>
```

注意：计算属性更新是因为组件在 props/attrs 更改时重新渲染，而不是因为 attrs 是响应式的。

## 替代方案: 使用 getCurrentInstance()（高级）

对于高级用例，您可以通过组件实例访问 attrs：

```vue
<script setup>
import { watch, getCurrentInstance } from 'vue'

const instance = getCurrentInstance()

// 这在某些情况下可能有效，但不受官方支持
// getCurrentInstance() 是内部 API，可能会更改
watch(
  () => instance?.attrs.someAttr,
  (newValue) => {
    console.log('属性已更改:', newValue)
  }
)
</script>
```

> **警告:** `getCurrentInstance()` 是内部 API。首选 `onUpdated()` 或转换为 props。

## 参考
- [透传属性 - 在 JavaScript 中访问](https://vuejs.org/guide/components/attrs.html#accessing-fallthrough-attributes-in-javascript)
- [Vue 3 响应式基础](https://vuejs.org/guide/essentials/reactivity-fundamentals.html)
