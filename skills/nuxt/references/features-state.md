---
name: state-management
description: useState composable 和 Nuxt 中 SSR 友好的状态管理
---

# 状态管理

Nuxt 提供 `useState` 用于跨组件共享状态的 SSR 安全响应式状态。

## useState

`ref` 的 SSR 安全替代品，跨组件共享状态：

```vue
<script setup lang="ts">
// 通过 'counter' 键在所有组件之间共享状态
const counter = useState('counter', () => 0)
</script>

<template>
  <div>
    计数器：{{ counter }}
    <button @click="counter++">+</button>
    <button @click="counter--">-</button>
  </div>
</template>
```

## 创建共享状态

定义可复用的状态 composables：

```ts
// composables/useUser.ts
export function useUser() {
  return useState<User | null>('user', () => null)
}

export function useLocale() {
  return useState('locale', () => 'en')
}
```

```vue
<script setup lang="ts">
// 在任何地方使用相同的实例
const user = useUser()
const locale = useLocale()
</script>
```

## 使用默认值

```vue
<script setup lang="ts">
// 带默认值的简单状态
const counter = useState('counter', () => 0)

// 复杂对象的默认值
interface Settings {
  theme: 'light' | 'dark'
  notifications: boolean
}

const settings = useState<Settings>('settings', () => ({
  theme: 'light',
  notifications: true
}))
</script>
```

## 更新状态

```vue
<script setup lang="ts">
const user = useState('user')

const updateUser = (newUser: User) => {
  user.value = newUser
}

const logout = () => {
  user.value = null
}
</script>

<template>
  <div v-if="user">欢迎，{{ user.name }}！</div>
  <button @click="logout" v-else>登录</button>
</template>
```

## 数组和对象状态

```vue
<script setup lang="ts">
// 数组状态
const items = useState('items', () => [
  { id: 1, text: '项目 1' },
  { id: 2, text: '项目 2' }
])

const addItem = (item: string) => {
  items.value.push({ id: Date.now(), text: item })
}

const removeItem = (id: number) => {
  items.value = items.value.filter(item => item.id !== id)
}
</script>

<template>
  <ul>
    <li v-for="item in items" :key="item.id">
      {{ item.text }}
      <button @click="removeItem(item.id)">删除</button>
    </li>
  </ul>
  <button @click="addItem('新项目')">添加</button>
</template>
```

## 状态持久化

```vue
<script setup lang="ts">
// 使用默认值进行持久化
const settings = useState('settings', () => ({
  theme: 'light',
  language: 'zh-CN'
}))

const toggleTheme = () => {
  settings.value.theme = settings.value.theme === 'light' ? 'dark' : 'light'
}
</script>
```

## 最佳实践

### SSR 安全性

使用 `useState` 而不是 `ref` 确保状态在 SSR 和客户端之间正确传递：

```vue
<script setup lang="ts">
// ✅ 正确 - SSR 安全
const count = useState('count', () => 0)

// ❌ 错误 - 会在水合时丢失
import { ref } from 'vue'
const count = ref(0)
</script>
```

### 键命名

使用描述性且唯一的键：

```ts
// ✅ 好的键名
const userState = useState('user-profile', () => null)
const appSettings = useState('app-config', () => {})

// ❌ 避免的键名
const s1 = useState('s1', () => {})
const s2 = useState('s2', () => {})
```

### 重置状态

```vue
<script setup lang="ts">
const counter = useState('counter', () => 0)

const reset = () => {
  counter.value = 0
}
</script>

<template>
  <div>计数器：{{ counter }}</div>
  <button @click="reset">重置</button>
</template>
```

## 组合多个状态

```vue
<script setup lang="ts">
const user = useState('user')
const isAuthenticated = useState('auth', () => false)

const login = () => {
  user.value = userData
  isAuthenticated.value = true
}

const logout = () => {
  user.value = null
  isAuthenticated.value = false
}
</script>

<template>
  <div v-if="isAuthenticated">欢迎，{{ user.name }}！</div>
  <button @click="logout" v-else>登录</button>
</template>
```

## 类型安全

```ts
// 定义状态类型
interface TodoItem {
  id: number
  text: string
  completed: boolean
}

const todos = useState<TodoItem[]>('todos', () => [])

const addTodo = (text: string) => {
  todos.value.push({
    id: Date.now(),
    text,
    completed: false
  })
}

const toggleTodo = (id: number) => {
  const todo = todos.value.find(t => t.id === id)
  if (todo) {
    todo.completed = !todo.completed
  }
}
```

## 响应式更新

```vue
<script setup lang="ts">
const data = useState('data', () => {
  name: '',
  email: ''
})

const updateData = (key: keyof typeof data.value, value: any) => {
  data.value[key] = value
}
</script>

<template>
  <input v-model="data.name" placeholder="姓名">
  <input v-model="data.email" placeholder="邮箱">
  <p>你好，{{ data.name }}！</p>
</template>
```

## 性能优化

```vue
<script setup lang="ts">
// 使用 shallow ref 避免不必要的重新渲染
const largeList = useState('items', () => Array(1000).fill(null))

const getItem = (index: number) => largeList.value[index]
</script>
```

## 高级用法

### 状态持久化

```ts
// composables/usePersistedState.ts
export function usePersistedState<T>(key: string, defaultValue: T) {
  const state = useState<T>(key, () => {
    if (import.meta.client) {
      const stored = localStorage.getItem(key)
      return stored ? JSON.parse(stored) : defaultValue
    }
    return defaultValue
  })
  
  watch(state, (newValue) => {
    if (import.meta.client) {
      localStorage.setItem(key, JSON.stringify(newValue))
    }
  }, { deep: true })
  
  return state
}
```

### 状态工厂

```ts
// composables/createState.ts
export function createState<T>(key: string, initialState: () => T) {
  const state = useState<T>(key, initialState)
  
  const reset = () => {
    state.value = initialState()
  }
  
  const update = (updater: (current: T) => T) => {
    state.value = updater(state.value)
  }
  
  return {
    state,
    reset,
    update,
  }
}
```

### 状态组合

```ts
// composables/useAuthStore.ts
export function useAuthStore() {
  const user = useState<User | null>('user', () => null)
  const token = useState<string | null>('token', () => null)
  
  const isAuthenticated = computed(() => !!token.value)
  
  async function login(credentials: Credentials) {
    const result = await $fetch('/api/login', {
      method: 'POST',
      body: credentials,
    })
    
    user.value = result.user
    token.value = result.token
  }
  
  function logout() {
    user.value = null
    token.value = null
  }
  
  return {
    user,
    token,
    isAuthenticated,
    login,
    logout,
  }
}
```

### 异步状态

```ts
// composables/useAsyncState.ts
export function useAsyncState<T>(
  key: string,
  fetcher: () => Promise<T>
) {
  const data = useState<T | null>(`${key}-data`, () => null)
  const error = useState<Error | null>(`${key}-error`, () => null)
  const loading = useState<boolean>(`${key}-loading`, () => false)
  
  const execute = async () => {
    loading.value = true
    error.value = null
    
    try {
      data.value = await fetcher()
    } catch (e) {
      error.value = e as Error
    } finally {
      loading.value = false
    }
  }
  
  onMounted(execute)
  
  return {
    data,
    error,
    loading,
    execute,
  }
}
```

## 状态共享模式

### 单例状态

```ts
// composables/useTheme.ts
export function useTheme() {
  // 所有组件共享同一个实例
  const theme = useState<'light' | 'dark'>('theme', () => 'light')
  
  const toggle = () => {
    theme.value = theme.value === 'light' ? 'dark' : 'light'
  }
  
  return {
    theme,
    toggle,
  }
}
```

### 局部状态

```ts
// 每个组件实例独立
export function useComponentState() {
  const isOpen = ref(false)
  
  const toggle = () => {
    isOpen.value = !isOpen.value
  }
  
  return { isOpen, toggle }
}
```

## 状态测试

```ts
// tests/composables/useCounter.test.ts
import { describe, it, expect } from 'vitest'
import { setActivePinia, createPinia } from 'pinia'
import { useCounter } from '~/composables/useCounter'

describe('useCounter', () => {
  beforeEach(() => {
    setActivePinia(createPinia())
  })
  
  it('increments correctly', () => {
    const { count, increment } = useCounter()
    
    expect(count.value).toBe(0)
    increment()
    expect(count.value).toBe(1)
  })
})
```

## 最佳实践

1. **使用 useState** - 替代 ref 实现 SSR 安全
2. **描述性键名** - 避免命名冲突
3. **封装状态** - 使用 composables 封装复杂状态
4. **类型安全** - 使用 TypeScript 泛型
5. **适度使用** - 避免全局状态滥用

## 常见问题

### 水合不匹配

**原因：** 使用了 ref 而不是 useState

**解决：**

```vue
<script setup lang="ts">
// ✅ 正确
const count = useState('count', () => 0)

// ❌ 错误 - 会导致水合不匹配
const count = ref(0)
</script>
```

### 状态不共享

**原因：** 使用了不同的键名

**解决：**

```ts
// ✅ 正确 - 使用相同键
const state1 = useState('shared', () => 0)
const state2 = useState('shared', () => 0)

// ❌ 错误 - 键名不同
const state1 = useState('state1', () => 0)
const state2 = useState('state2', () => 0)
```

### 状态重置

**解决：**

```ts
const counter = useState('counter', () => 0)

function reset() {
  counter.value = 0
}
```

<!-- 
源码参考：
- https://nuxt.com/docs/getting-started/state-management
- https://nuxt.com/docs/api/composables/use-state
- https://nuxt.com/docs/guide/going-further/state
-->