---
name: best-practices-debugging
description: Vue 3 调试技巧和工具
---

# 调试技巧

## Vue DevTools

### 安装和使用

```ts
// main.ts
import { createApp } from 'vue'
import App from './App.vue'

const app = createApp(App)

// 开发环境自动启用
if (import.meta.env.DEV) {
  app.config.devtools = true
}
```

### 组件检查

1. 打开浏览器开发者工具
2. 切换到 Vue DevTools 面板
3. 查看组件树、Props、Events 和状态

### 时间旅行调试

```ts
import { ref, watchEffect } from 'vue'

const count = ref(0)

watchEffect(() => {
  console.log('Count changed:', count.value)
})
```

## 控制台调试

### 使用 debugger

```vue
<script setup lang="ts">
import { ref } from 'vue'

const count = ref(0)

const increment = () => {
  debugger  // 在此处暂停
  count.value++
}
</script>
```

### console.log 调试

```ts
import { ref, watch } from 'vue'

const count = ref(0)

watch(count, (newVal, oldVal) => {
  console.log('Count changed from', oldVal, 'to', newVal)
})
```

### 使用 console.table

```ts
const users = ref([
  { id: 1, name: 'John', age: 30 },
  { id: 2, name: 'Jane', age: 25 }
])

console.table(users.value)
```

## 响应式调试

### 使用 markRaw

```ts
import { reactive, markRaw } from 'vue'

const state = reactive({
  user: markRaw({ name: 'John', age: 30 })
})

// user 不会被转换为响应式
```

### 使用 shallowRef

```ts
import { shallowRef } from 'vue'

const data = shallowRef({ items: [] })

// 只有 .value 赋值会触发响应式
```

### 使用 toRaw

```ts
import { reactive, toRaw } from 'vue'

const state = reactive({ count: 0 })

const rawState = toRaw(state)
console.log(rawState)  // 原始对象
```

## 组件调试

### 使用 defineExpose

```vue
<script setup lang="ts">
import { ref } from 'vue'

const count = ref(0)
const reset = () => { count.value = 0 }

defineExpose({
  count,
  reset
})
</script>
```

### 使用 v-bind 调试

```vue
<template>
  <div v-bind="{ id: 'debug', 'data-count': count }">
    {{ count }}
  </div>
</template>
```

### 使用 v-once 调试

```vue
<template>
  <div v-once>
    {{ debugInfo }}
  </div>
</template>
```

## 性能调试

### 使用 performance.mark

```ts
import { onMounted } from 'vue'

onMounted(() => {
  performance.mark('component-mounted-start')
  
  // 执行一些操作
  
  performance.mark('component-mounted-end')
  performance.measure(
    'component-mounted',
    'component-mounted-start',
    'component-mounted-end'
  )
  
  const measure = performance.getEntriesByName('component-mounted')[0]
  console.log('Component mounted in:', measure.duration, 'ms')
})
```

### 使用 Vue DevTools 性能面板

1. 打开 Vue DevTools
2. 切换到 Performance 面板
3. 记录组件渲染时间

## 错误处理

### 使用 onErrorCaptured

```ts
import { onErrorCaptured } from 'vue'

onErrorCaptured((err, instance, info) => {
  console.error('Error captured:', err)
  console.error('Component instance:', instance)
  console.error('Error info:', info)
  
  return false  // 阻止错误继续传播
})
```

### 使用 errorCaptured 生命周期

```vue
<script setup lang="ts">
import { onErrorCaptured } from 'vue'

onErrorCaptured((err, instance, info) => {
  console.error('Error:', err)
  return false
})
</script>

<template>
  <ErrorBoundary>
    <ChildComponent />
  </ErrorBoundary>
</template>
```

## 网络调试

### 使用 watchEffect 调试 API 调用

```ts
import { ref, watchEffect } from 'vue'

const data = ref(null)
const error = ref(null)

watchEffect(async () => {
  try {
    console.log('Fetching data...')
    const res = await fetch('/api/data')
    data.value = await res.json()
    console.log('Data fetched:', data.value)
  } catch (e) {
    error.value = e
    console.error('Error fetching data:', e)
  }
})
```

### 使用 onWatcherCleanup

```ts
import { ref, watchEffect, onWatcherCleanup } from 'vue'

const id = ref(1)

watchEffect(async () => {
  const controller = new AbortController()
  
  onWatcherCleanup(() => {
    console.log('Cleaning up previous request')
    controller.abort()
  })
  
  console.log('Fetching data for id:', id.value)
  const res = await fetch(`/api/${id.value}`, {
    signal: controller.signal
  })
  data.value = await res.json()
})
```

## 最佳实践总结

1. **Vue DevTools**：使用组件检查、时间旅行调试
2. **控制台调试**：使用 debugger、console.log、console.table
3. **响应式调试**：使用 markRaw、shallowRef、toRaw
4. **组件调试**：使用 defineExpose、v-bind、v-once
5. **性能调试**：使用 performance.mark、Vue DevTools 性能面板
6. **错误处理**：使用 onErrorCaptured、errorCaptured 生命周期
7. **网络调试**：使用 watchEffect、onWatcherCleanup

<!--
Source references:
- https://vuejs.org/guide/extras/debugging-tools
- https://vuejs.org/guide/scaling-up/debugging
-->
