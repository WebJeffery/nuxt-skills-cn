---
title: 响应式系统最佳实践
impact: HIGH
impactDescription: 正确使用响应式系统对性能和可维护性至关重要;误用会导致内存泄漏和难以调试的问题
type: best-practice
tags: [vue3, reactivity, ref, reactive, computed, watch, performance]
---

# 响应式系统最佳实践

**影响: HIGH** - 正确使用响应式系统对性能和可维护性至关重要。误用会导致内存泄漏、性能问题和难以调试的响应式行为。

## 任务列表

- 对原始值使用 `ref`,对对象使用 `reactive`
- 避免解构 `reactive` 对象
- 使用 `computed` 进行派生状态
- 使用 `watch` 进行副作用,使用 `watchEffect` 进行即时响应
- 在侦听器中正确清理副作用
- 避免在模板中直接修改响应式状态

## 对原始值使用 ref

**正确:**
```javascript
const count = ref(0)
const message = ref('hello')
```

**错误:**
```javascript
const count = reactive(0) // 不工作
```

## 对对象使用 reactive

**正确:**
```javascript
const state = reactive({
  count: 0,
  name: 'John'
})
```

**错误:**
```javascript
const state = ref({
  count: 0,
  name: 'John'
}) // 需要使用 .value 访问
```

## 避免解构 reactive 对象

**错误:**
```javascript
const state = reactive({
  count: 0,
  name: 'John'
})

const { count, name } = state // 失去响应性
```

**正确:**
```javascript
const state = reactive({
  count: 0,
  name: 'John'
})

// 使用 toRefs 保持响应性
const { count, name } = toRefs(state)
```

## 使用 computed 进行派生状态

**正确:**
```javascript
const firstName = ref('John')
const lastName = ref('Doe')

const fullName = computed(() => `${firstName.value} ${lastName.value}`)
```

**错误:**
```javascript
const firstName = ref('John')
const lastName = ref('Doe')

const fullName = `${firstName.value} ${lastName.value}` // 不是响应式的
```

## 使用 watch 进行副作用

**正确:**
```javascript
const count = ref(0)

watch(count, (newValue, oldValue) => {
  console.log(`Count changed from ${oldValue} to ${newValue}`)
})
```

**错误:**
```javascript
const count = ref(0)

watchEffect(() => {
  console.log(`Count is ${count.value}`) // 立即执行
})
```

## 清理侦听器

**正确:**
```javascript
watch(source, (value) => {
  const timer = setTimeout(() => {
    // 副作用
  }, 1000)
  
  return () => {
    clearTimeout(timer) // 清理
  }
})
```

## 避免在模板中修改状态

**错误:**
```vue
<template>
  <button @click="count++">{{ count }}</button>
</template>

<script setup>
const count = ref(0)
</script>
```

**正确:**
```vue
<template>
  <button @click="increment">{{ count }}</button>
</template>

<script setup>
const count = ref(0)

function increment() {
  count.value++
}
</script>
```

## 性能优化

1. **使用 shallowRef 和 shallowReactive:**
   - 对于大型对象,避免深度响应式
   - 仅在需要时使用深度响应式

2. **使用 computed 缓存:**
   - 避免重复计算
   - 只在依赖项变化时重新计算

3. **使用 watch 的 deep 选项谨慎:**
   - 深度侦听会影响性能
   - 仅在必要时使用

4. **避免不必要的响应式:**
   - 静态数据不需要响应式
   - 使用 markRaw 标记不应响应式的对象
