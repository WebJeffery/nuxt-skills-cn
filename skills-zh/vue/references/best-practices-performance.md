---
name: best-practices-performance
description: Vue 3 性能优化最佳实践
---

# 性能优化

## 响应式优化

### 使用 shallowRef

```ts
import { ref, shallowRef } from 'vue'

// 对于大型数据结构，使用 shallowRef
const largeData = shallowRef({ items: [] })

// 只有 .value 赋值会触发响应式
largeData.value = { items: newData }
```

### 避免深度侦听

```ts
import { ref, watch } from 'vue'

const state = ref({ nested: { value: 1 } })

// 避免深度侦听
watch(() => state.value.nested.value, callback)

// 使用 shallowRef + 侦听特定属性
const shallowState = shallowRef({ nested: { value: 1 } })
watch(() => shallowState.value.nested, callback)
```

### 计算属性缓存

```ts
import { ref, computed } from 'vue'

const list = ref([1, 2, 3, 4, 5])

// 计算属性会自动缓存
const sum = computed(() => list.value.reduce((a, b) => a + b, 0))
```

### 使用 v-memo

```vue
<template>
  <div v-for="item in items" :key="item.id" v-memo="[item.selected]">
    <ExpensiveComponent :item="item" />
  </div>
</template>
```

## 渲染优化

### 虚拟滚动

```vue
<template>
  <VirtualList
    :data-sources="items"
    :data-key="'id'"
    :estimate-size="50"
  />
</template>
```

### 懒加载组件

```vue
<script setup lang="ts">
import { defineAsyncComponent } from 'vue'

const AsyncComponent = defineAsyncComponent(() =>
  import('./HeavyComponent.vue')
)
</script>

<template>
  <Suspense>
    <template #default>
      <AsyncComponent />
    </template>
    <template #fallback>
      <div>加载中...</div>
    </template>
  </Suspense>
</template>
```

### 使用 v-once

```vue
<template>
  <!-- 静态内容只渲染一次 -->
  <div v-once>
    <h1>静态标题</h1>
    <p>静态内容</p>
  </div>
</template>
```

### 列表渲染优化

```vue
<template>
  <!-- 为每个项目提供唯一的 key -->
  <div v-for="item in items" :key="item.id">
    {{ item.name }}
  </div>
</template>
```

## 组件优化

### 组件拆分

```vue
<!-- 将大型组件拆分为更小的组件 -->
<template>
  <Header />
  <Content />
  <Footer />
</template>
```

### 使用函数式组件

```vue
<script setup lang="ts">
import { h } from 'vue'

const FunctionalComponent = (props: { title: string }) => {
  return h('div', props.title)
}
</script>
```

### 避免不必要的响应式

```ts
// 不需要响应式的数据使用普通变量
const staticData = { name: 'John', age: 30 }

// 需要响应式的数据使用 ref
const reactiveData = ref({ name: 'John', age: 30 })
```

## 事件优化

### 事件委托

```vue
<template>
  <ul @click="handleClick">
    <li v-for="item in items" :key="item.id" :data-id="item.id">
      {{ item.name }}
    </li>
  </ul>
</template>

<script setup lang="ts">
const handleClick = (e: MouseEvent) => {
  const target = e.target as HTMLElement
  const id = target.dataset.id
  console.log('Clicked:', id)
}
</script>
```

### 防抖和节流

```ts
import { ref, watch } from 'vue'

const searchQuery = ref('')

// 防抖
watch(searchQuery, debounce((newVal) => {
  fetchResults(newVal)
}, 300))

// 节流
watch(scrollPosition, throttle((newPos) => {
  updateView(newPos)
}, 100))
```

## 内存优化

### 清理副作用

```ts
import { onUnmounted, watchEffect } from 'vue'

watchEffect(() => {
  const timer = setInterval(() => {
    // 定时任务
  }, 1000)

  // 清理副作用
  onUnmounted(() => {
    clearInterval(timer)
  })
})
```

### 使用 onWatcherCleanup

```ts
import { ref, watchEffect, onWatcherCleanup } from 'vue'

const id = ref(1)

watchEffect(async () => {
  const controller = new AbortController()
  
  // 清理之前的请求
  onWatcherCleanup(() => controller.abort())
  
  const res = await fetch(`/api/${id.value}`, {
    signal: controller.signal
  })
  data.value = await res.json()
})
```

### KeepAlive 优化

```vue
<template>
  <KeepAlive :max="10">
    <component :is="currentTab" />
  </KeepAlive>
</template>
```

## 最佳实践总结

1. **响应式优化**：使用 shallowRef、避免深度侦听、利用计算属性缓存
2. **渲染优化**：虚拟滚动、懒加载、v-once、正确的 key
3. **组件优化**：拆分大型组件、使用函数式组件、避免不必要的响应式
4. **事件优化**：事件委托、防抖和节流
5. **内存优化**：清理副作用、使用 onWatcherCleanup、KeepAlive 限制

<!--
Source references:
- https://vuejs.org/guide/best-practices/performance
- https://vuejs.org/guide/extras/rendering-mechanism
-->
