---
title: 模板函数必须是纯函数且无副作用
impact: MEDIUM
impactDescription: 模板中具有副作用的函数在每次重新渲染时都会导致不可预测的行为
type: efficiency
tags: [vue3, template, functions, performance, side-effects]
---

# 模板函数必须是纯函数且无副作用

**影响：中** - 在模板中调用的函数在每次组件重新渲染时都会执行。具有副作用（修改数据、API 调用、日志记录）的函数将导致不可预测的行为、性能问题和难以调试的 bug。

包括函数调用的模板表达式在组件更新时都会被评估。这使得它们不适合应该只发生一次或修改状态的操作。

## 任务清单

- [ ] 保持模板函数纯函数（相同输入 = 相同输出）
- [ ] 永远不要在模板函数中修改响应式状态
- [ ] 永远不要在模板函数中进行 API 调用或异步操作
- [ ] 将副作用移到事件处理程序、watchers 或生命周期钩子中
- [ ] 尽可能使用计算属性代替函数来获取派生值
- [ ] 避免昂贵的计算；使用计算属性进行缓存

**错误：**
```vue
<template>
  <!-- 错误：在每次渲染时修改状态 -->
  <p>{{ incrementAndGet() }}</p>

  <!-- 错误：在每次渲染时进行 API 调用 -->
  <div>{{ fetchUserName() }}</div>

  <!-- 错误：日志记录副作用 -->
  <span>{{ logAndFormat(date) }}</span>

  <!-- 错误：昂贵的计算没有缓存 -->
  <ul>
    <li v-for="item in filterAndSort(items)" :key="item.id">
      {{ item.name }}
    </li>
  </ul>

  <!-- 错误：随机值在每次渲染时都会改变 -->
  <p>{{ getRandomGreeting() }}</p>
</template>

<script setup>
import { ref } from 'vue'

const count = ref(0)
const items = ref([/* 大型数组 */])

// 错误：有副作用 - 修改状态
function incrementAndGet() {
  count.value++  // 副作用！
  return count.value
}

// 错误：模板中的异步操作
async function fetchUserName() {
  const res = await fetch('/api/user')  // 副作用！
  return (await res.json()).name
}

// 错误：日志记录是副作用
function logAndFormat(date) {
  console.log('格式化日期：', date)  // 副作用！
  return new Date(date).toLocaleDateString()
}

// 错误：昂贵，每次渲染都运行没有缓存
function filterAndSort(items) {
  return items
    .filter(i => i.active)
    .sort((a, b) => a.name.localeCompare(b.name))
}

// 错误：非确定性
function getRandomGreeting() {
  const greetings = ['你好', '嗨', '嘿']
  return greetings[Math.floor(Math.random() * greetings.length)]
}
</script>
```

**正确：**
```vue
<template>
  <!-- 正确：纯格式化函数 -->
  <p>计数：{{ count }}</p>
  <button @click="increment">增加</button>

  <!-- 正确：通过生命周期/watcher 获取数据 -->
  <div>{{ userName }}</div>

  <!-- 正确：纯函数，没有副作用 -->
  <span>{{ formatDate(date) }}</span>

  <!-- 正确：计算属性缓存结果 -->
  <ul>
    <li v-for="item in filteredAndSortedItems" :key="item.id">
      {{ item.name }}
    </li>
  </ul>

  <!-- 正确：随机值设置一次 -->
  <p>{{ greeting }}</p>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const count = ref(0)
const userName = ref('')
const date = ref(new Date())
const items = ref([/* 大型数组 */])

// 事件处理程序中的副作用
function increment() {
  count.value++
}

// 在生命周期钩子中获取数据
onMounted(async () => {
  const res = await fetch('/api/user')
  userName.value = (await res.json()).name
})

// 纯函数 - 相同输入，相同输出
function formatDate(date) {
  return new Date(date).toLocaleDateString()
}

// 计算属性 - 缓存，仅在依赖项更改时重新计算
const filteredAndSortedItems = computed(() => {
  return items.value
    .filter(i => i.active)
    .sort((a, b) => a.name.localeCompare(b.name))
})

// 设置随机值一次，而不是每次渲染
const greetings = ['你好', '嗨', '嘿']
const greeting = ref(greetings[Math.floor(Math.random() * greetings.length)])
</script>
```

## 纯函数指南

纯函数：
1. 给定相同输入，总是返回相同输出
2. 不修改任何外部状态
3. 不执行 I/O 操作（网络、控制台、文件系统）
4. 不依赖于可变的外部状态

```javascript
// 纯 - 对模板安全
function formatCurrency(amount, currency = 'USD') {
  return new Intl.NumberFormat('en-US', { style: 'currency', currency }).format(amount)
}

function fullName(first, last) {
  return `${first} ${last}`
}

function isExpired(date) {
  return new Date(date) < new Date()
}

// 不纯 - 对模板不安全
function logAndReturn(value) {
  console.log(value)  // I/O
  return value
}

function getFromLocalStorage(key) {
  return localStorage.getItem(key)  // 外部状态
}

function updateAndReturn(obj, key, value) {
  obj[key] = value  // 变异
  return obj
}
```

## 参考
- [Vue.js 模板语法 - 调用函数](https://vuejs.org/guide/essentials/template-syntax.html#calling-functions)
- [Vue.js 计算属性](https://vuejs.org/guide/essentials/computed.html)
