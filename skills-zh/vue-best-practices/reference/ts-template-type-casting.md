---
title: 在模板中对联合类型使用类型转换
impact: MEDIUM
impactDescription: 即使在运行时类型已知，具有联合类型的模板表达式也会导致 TypeScript 错误
type: gotcha
tags: [typescript, templates, type-casting, union-types, script-setup]
---

# 在模板中对联合类型使用类型转换

**影响：中** - 在 Vue 单文件组件中使用 `lang="ts"` 时，模板表达式会进行严格的类型检查。如果变量具有联合类型，您可能需要内联类型转换来访问类型特定的方法或属性。

## 任务清单

- [ ] 在模板中使用 `(value as Type)` 语法进行类型转换
- [ ] 考虑在模板中使用之前在 script 中缩小类型
- [ ] 记住模板类型检查需要 `lang="ts"` 属性
- [ ] 对于 Vue CLI/webpack：确保 vue-loader >= 16.8.0

## 问题

当变量具有联合类型时，TypeScript 无法在模板编译时知道它是哪种特定类型：

**带有类型错误的模板：**
```vue
<script setup lang="ts">
// 联合类型：可能是 string 或 number
let x: string | number = 1
</script>

<template>
  <!-- 错误：属性 'toFixed' 不存在于类型 'string | number' 上 -->
  {{ x.toFixed(2) }}
</template>
```

TypeScript 正确地识别出 `toFixed()` 只存在于 `number` 上，而不是 `string` 上。

## 解决方案1：内联类型转换

直接在模板中使用 `(value as Type)` 语法：

```vue
<script setup lang="ts">
let x: string | number = 1
</script>

<template>
  <!-- 正确：转换为 number 以访问 toFixed -->
  {{ (x as number).toFixed(2) }}
</template>
```

## 解决方案2：计算属性（推荐）

创建一个缩小或转换类型的计算属性：

```vue
<script setup lang="ts">
import { ref, computed } from 'vue'

const value = ref<string | number>(1)

const formattedValue = computed(() => {
  if (typeof value.value === 'number') {
    return value.value.toFixed(2)
  }
  return value.value
})
</script>

<template>
  <!-- 模板简洁，script 中类型安全的逻辑 -->
  {{ formattedValue }}
</template>
```

## 解决方案3：类型保护函数

定义类型保护并在模板中使用：

```vue
<script setup lang="ts">
import { ref } from 'vue'

const data = ref<string | number | null>(null)

function isNumber(val: unknown): val is number {
  return typeof val === 'number'
}

function formatNumber(val: number): string {
  return val.toFixed(2)
}
</script>

<template>
  <div v-if="isNumber(data)">
    {{ formatNumber(data) }}
  </div>
  <div v-else-if="data !== null">
    {{ data }}
  </div>
</template>
```

## 常见用例

### API 响应数据

```vue
<script setup lang="ts">
interface ApiResponse {
  status: 'success' | 'error'
  data?: UserData
  error?: string
}

const response = ref<ApiResponse | null>(null)
</script>

<template>
  <div v-if="response?.status === 'success'">
    <!-- 转换以安全访问 data -->
    {{ (response as { data: UserData }).data.name }}
  </div>
</template>
```

**更好的方法使用计算属性：**
```vue
<script setup lang="ts">
const userData = computed(() => {
  if (response.value?.status === 'success') {
    return response.value.data
  }
  return null
})
</script>

<template>
  <div v-if="userData">
    {{ userData.name }}
  </div>
</template>
```

### 带有事件类型的事件处理程序

```vue
<script setup lang="ts">
function handleInput(event: Event) {
  // 转换为 HTMLInputElement 以访问 'value'
  const value = (event.target as HTMLInputElement).value
  console.log(value)
}
</script>

<template>
  <input @input="handleInput" />
</template>
```

### 数组项访问

```vue
<script setup lang="ts">
const items = ref<(string | number)[]>([1, 'two', 3])
</script>

<template>
  <ul>
    <li v-for="item in items" :key="item">
      <!-- 当你知道类型时转换 -->
      <span v-if="typeof item === 'number'">
        Number: {{ (item as number).toFixed(1) }}
      </span>
      <span v-else>
        String: {{ (item as string).toUpperCase() }}
      </span>
    </li>
  </ul>
</template>
```

## 何时需要类型转换

| 场景 | 解决方案 |
|----------|----------|
| 联合类型 | 转换为特定类型 |
| 可空类型 | 使用可选链或在 null 检查后转换 |
| 事件目标 | 将 `event.target` 转换为特定元素类型 |
| 数组方法 | 当 TypeScript 无法缩小数组项类型时转换 |

## 重要说明

### 模板类型检查要求

在以下情况下启用模板类型检查：
1. 使用了 `<script lang="ts">` 或 `<script setup lang="ts">`
2. Vue 语言服务器（Volar）在您的 IDE 中处于活动状态
3. 对于 webpack：需要 vue-loader >= 16.8.0

### 避免过度转换

如果您发现自己经常在模板中进行转换，请考虑：
- 将逻辑移动到计算属性
- 在 script 部分使用类型保护
- 重构数据结构以更具体

## 参考

- [Vue.js TypeScript 概述 - 模板中的 TypeScript](https://vuejs.org/guide/typescript/overview.html#typescript-in-templates)
- [Vue.js TypeScript 与 Composition API](https://vuejs.org/guide/typescript/composition-api.html)
