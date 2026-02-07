---
title: 不要将工具函数包装为组合式函数
impact: MEDIUM
impactDescription: 将无状态工具函数包装为组合式函数会增加不必要的复杂性,而没有任何好处
type: best-practice
tags: [vue3, composables, composition-api, utilities, patterns]
---

# 不要将工具函数包装为组合式函数

**影响:中等** - 并非每个函数都需要是组合式函数。组合式函数专门用于封装使用 Vue 响应式系统的**有状态逻辑**。仅转换数据或执行计算的纯工具函数应保持为常规 JavaScript 函数。

将工具函数包装为组合式函数会增加不必要的抽象层,使代码更难理解,并且没有任何好处,因为没有响应式状态需要管理。

## 任务清单

- [ ] 识别函数是否管理响应式状态或使用 Vue 生命周期钩子
- [ ] 将纯转换/计算函数保持为常规工具函数
- [ ] 直接导出工具函数,而不是包装在返回它们的函数中
- [ ] 将 "use" 前缀保留给实际的组合式函数

**不正确:**
```javascript
// 错误: 这些只是不必要的包装工具函数

// 没有任何价值 - 没有响应式状态
export function useFormatters() {
  const formatDate = (date) => {
    return new Intl.DateTimeFormat('en-US').format(date)
  }

  const formatCurrency = (amount) => {
    return new Intl.NumberFormat('en-US', {
      style: 'currency',
      currency: 'USD'
    }).format(amount)
  }

  const capitalize = (str) => {
    return str.charAt(0).toUpperCase() + str.slice(1)
  }

  return { formatDate, formatCurrency, capitalize }
}

// 错误: 纯计算,没有响应式状态
export function useMath() {
  const add = (a, b) => a + b
  const multiply = (a, b) => a * b
  const clamp = (value, min, max) => Math.min(Math.max(value, min), max)

  return { add, multiply, clamp }
}

// 用法增加了仪式感,没有任何好处
const { formatDate, formatCurrency } = useFormatters()
const { clamp } = useMath()
```

**正确:**
```javascript
// 正确: 作为常规工具函数导出

// utils/formatters.js
export function formatDate(date) {
  return new Intl.DateTimeFormat('en-US').format(date)
}

export function formatCurrency(amount) {
  return new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency: 'USD'
  }).format(amount)
}

export function capitalize(str) {
  return str.charAt(0).toUpperCase() + str.slice(1)
}

// utils/math.js
export function clamp(value, min, max) {
  return Math.min(Math.max(value, min), max)
}

// 用法 - 简单直接
import { formatDate, formatCurrency } from '@/utils/formatters'
import { clamp } from '@/utils/math'
```

## 何时使用组合式函数 vs 工具函数

| 使用组合式函数当... | 使用工具函数当... |
|------------------------|---------------------|
| 管理响应式状态 (`ref`, `reactive`) | 纯数据转换 |
| 使用生命周期钩子 (`onMounted`, `onUnmounted`) | 无状态计算 |
| 设置侦听器 (`watch`, `watchEffect`) | 字符串/数组操作 |
| 创建计算属性 | 格式化函数 |
| 需要在组件卸载时清理 | 验证函数 |
| 状态随时间变化 | 数学运算 |

## 示例: 组合式函数 vs 工具函数

```javascript
// 组合式函数: 具有响应式状态和生命周期
export function useWindowSize() {
  const width = ref(window.innerWidth)
  const height = ref(window.innerHeight)

  function update() {
    width.value = window.innerWidth
    height.value = window.innerHeight
  }

  onMounted(() => window.addEventListener('resize', update))
  onUnmounted(() => window.removeEventListener('resize', update))

  return { width, height }
}

// 工具函数: 纯转换,没有状态
export function parseQueryString(queryString) {
  return Object.fromEntries(new URLSearchParams(queryString))
}

// 组合式函数: 随时间管理表单状态
export function useForm(initialValues) {
  const values = ref({ ...initialValues })
  const errors = ref({})
  const isDirty = computed(() =>
    JSON.stringify(values.value) !== JSON.stringify(initialValues)
  )

  function reset() {
    values.value = { ...initialValues }
    errors.value = {}
  }

  return { values, errors, isDirty, reset }
}

// 工具函数: 无状态验证
export function validateEmail(email) {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)
}

export function validateRequired(value) {
  return value !== null && value !== undefined && value !== ''
}
```

## 混合模式: 组合式函数使用工具函数

组合式函数使用工具函数是完全没问题的:

```javascript
// utils/validators.js
export function validateEmail(email) {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)
}

// composables/useEmailInput.js
import { ref, computed } from 'vue'
import { validateEmail } from '@/utils/validators'

export function useEmailInput(initialValue = '') {
  const email = ref(initialValue)
  const isValid = computed(() => validateEmail(email.value))
  const error = computed(() =>
    email.value && !isValid.value ? 'Invalid email format' : null
  )

  return { email, isValid, error }
}
```

## 文件组织

```
src/
  composables/        # 有状态响应式逻辑
    useAuth.js
    useFetch.js
    useLocalStorage.js
  utils/              # 纯工具函数
    formatters.js
    validators.js
    math.js
    strings.js
```

## 参考
- [Vue.js 组合式函数 - 什么是组合式函数](https://vuejs.org/guide/reusability/composables.html#what-is-a-composable)
- [创建组合式函数的常见错误](https://www.telerik.com/blogs/common-mistakes-creating-composition-functions-vue)
