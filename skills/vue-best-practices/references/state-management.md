---
title: 状态管理最佳实践
impact: HIGH
impactDescription: 正确的状态管理对应用可维护性至关重要;误用会导致难以调试的状态问题
type: best-practice
tags: [vue3, state-management, pinia, vuex, composition-api, reactive]
---

# 状态管理最佳实践

**影响: HIGH** - 正确的状态管理对应用可维护性至关重要。误用会导致难以调试的状态问题和性能瓶颈。

## 任务列表

- 根据应用规模选择合适的状态管理方案
- 使用 Pinia 作为 Vue 3 的推荐状态管理库
- 将状态按功能域组织
- 避免在组件之间直接传递复杂状态
- 使用 composables 处理局部状态
- 为状态提供 TypeScript 类型

## 选择合适的状态管理方案

### 小型应用
**使用 composables:**
```javascript
// composables/useCounter.js
import { ref, computed } from 'vue'

export function useCounter() {
  const count = ref(0)
  const double = computed(() => count.value * 2)

  function increment() {
    count.value++
  }

  return { count, double, increment }
}
```

### 中型应用
**使用 Pinia:**
```javascript
// stores/counter.js
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', {
  state: () => ({
    count: 0
  }),
  getters: {
    double: (state) => state.count * 2
  },
  actions: {
    increment() {
      this.count++
    }
  }
})
```

### 大型应用
**使用 Pinia + 模块化:**
```javascript
// stores/index.js
export { useUserStore } from './user'
export { useCartStore } from './cart'
export { useProductStore } from './product'
```

## 使用 Pinia Store

**正确:**
```javascript
import { defineStore } from 'pinia'

export const useUserStore = defineStore('user', {
  state: () => ({
    user: null,
    isAuthenticated: false
  }),
  getters: {
    displayName: (state) => state.user?.name || '访客'
  },
  actions: {
    async login(credentials) {
      const user = await api.login(credentials)
      this.user = user
      this.isAuthenticated = true
    },
    logout() {
      this.user = null
      this.isAuthenticated = false
    }
  }
})
```

## 在组件中使用 Store

**正确:**
```vue
<script setup>
import { useUserStore } from '@/stores/user'

const userStore = useUserStore()
</script>

<template>
  <div v-if="userStore.isAuthenticated">
    欢迎,{{ userStore.displayName }}!
  </div>
  <button @click="userStore.logout">登出</button>
</template>
```

## 组织 Store

**按功能域组织:**
```
stores/
├── user.js          # 用户相关状态
├── cart.js          # 购物车状态
├── product.js       # 产品状态
└── index.js         # 导出所有 stores
```

## 避免状态穿透

**错误:**
```vue
<!-- 祖先组件 -->
<script setup>
const data = ref({ /* 复杂对象 */ })
provide('data', data)
</script>

<!-- 孙子组件 -->
<script setup>
const data = inject('data')
</script>
```

**正确:**
```vue
<!-- 使用 store -->
<script setup>
import { useDataStore } from '@/stores/data'

const dataStore = useDataStore()
</script>
```

## TypeScript 支持

```typescript
// stores/user.ts
import { defineStore } from 'pinia'

interface User {
  id: string
  name: string
  email: string
}

interface UserState {
  user: User | null
  isAuthenticated: boolean
}

export const useUserStore = defineStore('user', {
  state: (): UserState => ({
    user: null,
    isAuthenticated: false
  }),
  getters: {
    displayName: (state): string => state.user?.name || '访客'
  },
  actions: {
    async login(credentials: LoginCredentials): Promise<void> {
      const user = await api.login(credentials)
      this.user = user
      this.isAuthenticated = true
    }
  }
})
```

## 最佳实践

1. **状态分层:**
   - 局部状态: 使用 ref/reactive
   - 组件间共享: 使用 composables
   - 全局状态: 使用 Pinia

2. **保持 store 专注:**
   - 每个 store 只负责一个功能域
   - 避免在 store 中混合不相关的状态
   - 将复杂逻辑提取到 composables

3. **避免过度使用全局状态:**
   - 只在真正需要共享时使用 store
   - 优先使用 props 和 emits 进行父子通信
   - 使用 provide/inject 进行深层嵌套组件通信

4. **性能优化:**
   - 使用计算属性缓存派生状态
   - 避免在 store 中存储大型对象
   - 考虑使用 shallowRef/shallowReactive 优化大型数据
