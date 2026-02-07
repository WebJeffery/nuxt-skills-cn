---
name: best-practices-troubleshooting
description: Vue 3 常见问题解决方案
---

# 常见问题解决方案

## 响应式问题

### 问题：响应式数据不更新

```ts
import { reactive } from 'vue'

const state = reactive({ count: 0 })

const { count } = state  // 解构会失去响应式

// 解决方案 1：使用 toRefs
import { toRefs } from 'vue'
const { count } = toRefs(state)

// 解决方案 2：直接访问
const increment = () => {
  state.count++
}
```

### 问题：数组索引更新不触发响应式

```ts
import { ref } from 'vue'

const list = ref([1, 2, 3])

list.value[0] = 4  // 不会触发更新

// 解决方案
list.value = [...list.value]  // 创建新数组
// 或
list.value.splice(0, 1, 4)  // 使用数组方法
```

### 问题：对象属性添加不触发响应式

```ts
import { reactive } from 'vue'

const state = reactive({ name: 'John' })

state.age = 30  // 不会触发更新

// 解决方案
import { set } from '@vueuse/core'
set(state, 'age', 30)
```

## 组件问题

### 问题：组件不更新

```vue
<script setup lang="ts">
import { ref } from 'vue'

const count = ref(0)

const increment = () => {
  count.value++  // 确保使用 .value
}
</script>
```

### 问题：Props 修改警告

```vue
<script setup lang="ts">
const props = defineProps<{
  count: number
}>()

// 直接修改 props 会警告
props.count++  // 不要这样做

// 解决方案：使用 emit
const emit = defineEmits<{
  update: [value: number]
}>()

const increment = () => {
  emit('update', props.count + 1)
}
</script>
```

### 问题：组件卸载后访问状态

```ts
import { ref, onUnmounted } from 'vue'

const timer = ref<number | null>(null)

const startTimer = () => {
  timer.value = window.setInterval(() => {
    console.log('Timer tick')
  }, 1000)
}

onUnmounted(() => {
  if (timer.value) {
    clearInterval(timer.value)
  }
})
```

## 生命周期问题

### 问题：DOM 未就绪时访问元素

```vue
<script setup lang="ts">
import { ref, onMounted } from 'vue'

const elementRef = ref<HTMLElement | null>(null)

// 在 onMounted 之前访问 elementRef.value 为 null
const handleClick = () => {
  if (elementRef.value) {
    elementRef.value.focus()
  }
}

onMounted(() => {
  console.log('DOM 已就绪')
})
</script>

<template>
  <input ref="elementRef" @click="handleClick" />
</template>
```

### 问题：异步操作未完成时卸载组件

```ts
import { ref, onUnmounted, watchEffect } from 'vue'

const data = ref(null)
const controller = ref<AbortController | null>(null)

watchEffect(async () => {
  controller.value = new AbortController()
  
  try {
    const res = await fetch('/api/data', {
      signal: controller.value.signal
    })
    data.value = await res.json()
  } catch (e) {
    if (e.name !== 'AbortError') {
      console.error('Error:', e)
    }
  }
})

onUnmounted(() => {
  if (controller.value) {
    controller.value.abort()
  }
})
```

## 性能问题

### 问题：组件渲染频繁

```vue
<script setup lang="ts">
import { ref, computed } from 'vue'

const items = ref([1, 2, 3, 4, 5])

// 使用 computed 缓存结果
const sum = computed(() => items.value.reduce((a, b) => a + b, 0))
</script>
```

### 问题：大列表渲染缓慢

```vue
<script setup lang="ts">
import { ref } from 'vue'

const items = ref(Array(10000).fill(0).map((_, i) => i))

// 解决方案：使用虚拟滚动
import { VirtualList } from 'vue-virtual-scroll-list'
</script>

<template>
  <VirtualList
    :data-sources="items"
    :data-key="'id'"
    :estimate-size="50"
  />
</template>
```

### 问题：内存泄漏

```ts
import { ref, onUnmounted } from 'vue'

const listeners: Array<() => void> = []

const addListener = (callback: () => void) => {
  listeners.push(callback)
}

onUnmounted(() => {
  listeners.forEach(unsubscribe => unsubscribe())
})
```

## 类型问题

### 问题：defineProps 类型推断失败

```vue
<script setup lang="ts">
// 错误方式
const props = defineProps({
  count: Number,
  name: String
})

// 正确方式
const props = defineProps<{
  count: number
  name: string
}>()
</script>
```

### 问题：组合式函数类型不明确

```ts
import { ref } from 'vue'

// 明确返回类型
export function useCounter(initialValue: number) {
  const count = ref(initialValue)
  const increment = () => count.value++
  const decrement = () => count.value--
  
  return {
    count,
    increment,
    decrement
  } as const
}
```

## 构建问题

### 问题：生产环境错误

```ts
// 确保使用环境变量
const apiUrl = import.meta.env.VITE_API_URL

if (!apiUrl) {
  throw new Error('API_URL is not defined')
}
```

### 问题：静态资源路径错误

```vue
<script setup lang="ts">
import logo from '@/assets/logo.png'
</script>

<template>
  <img :src="logo" alt="Logo" />
</template>
```

## 最佳实践总结

1. **响应式问题**：使用 toRefs、避免直接修改数组索引、使用 set 方法
2. **组件问题**：确保使用 .value、通过 emit 更新 props、清理定时器
3. **生命周期问题**：在 onMounted 中访问 DOM、使用 AbortController 清理异步操作
4. **性能问题**：使用 computed、虚拟滚动、清理事件监听器
5. **类型问题**：使用 TypeScript 类型定义、明确返回类型
6. **构建问题**：使用环境变量、正确引用静态资源

<!--
Source references:
- https://vuejs.org/guide/essentials/reactivity-fundamentals
- https://vuejs.org/guide/components/props
- https://vuejs.org/guide/essentials/lifecycle
-->
