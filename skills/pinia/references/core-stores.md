---
name: stores
description: 在 Pinia 中定义 store、state、getters 和 actions
---

# Pinia Stores

Store 使用 `defineStore()` 和唯一名称定义。每个 store 有三个核心概念: **state**、**getters** 和 **actions**。

## 定义 Stores

### Option Stores

类似于 Vue 的 Options API:

```ts
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', {
  state: () => ({
    count: 0,
    name: 'Eduardo',
  }),
  getters: {
    doubleCount: (state) => state.count * 2,
  },
  actions: {
    increment() {
      this.count++
    },
  },
})
```

可以将 `state` 理解为 `data`，`getters` 理解为 `computed`，`actions` 理解为 `methods`。

### Setup Stores (推荐)

使用 Composition API 语法 - 更灵活、更强大:

```ts
import { ref, computed } from 'vue'
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)
  const name = ref('Eduardo')
  const doubleCount = computed(() => count.value * 2)

  function increment() {
    count.value++
  }

  return { count, name, doubleCount, increment }
})
```

在 Setup Stores 中: `ref()` → state，`computed()` → getters，`function()` → actions。

**重要:** 你必须返回所有 state 属性，以便 Pinia 跟踪它们。

### 使用 Stores

```vue
<script setup>
import { useCounterStore } from '@/stores/counter'

const store = useCounterStore()
// 访问: store.count, store.doubleCount, store.increment()
</script>
```

### 使用 storeToRefs 解构

```vue
<script setup>
import { storeToRefs } from 'pinia'
import { useCounterStore } from '@/stores/counter'

const store = useCounterStore()

// ❌ 破坏响应性
const { name, doubleCount } = store

// ✅ 为 state/getters 保留响应性
const { name, doubleCount } = storeToRefs(store)

// ✅ Actions 可以直接解构
const { increment } = store
</script>
```

---

## State

State 定义为返回初始状态的函数。

### TypeScript

类型推断自动工作。对于复杂类型:

```ts
interface UserInfo {
  name: string
  age: number
}

export const useUserStore = defineStore('user', {
  state: () => ({
    userList: [] as UserInfo[],
    user: null as UserInfo | null,
  }),
})
```

或使用接口作为返回类型:

```ts
interface State {
  userList: UserInfo[]
  user: UserInfo | null
}

export const useUserStore = defineStore('user', {
  state: (): State => ({
    userList: [],
    user: null,
  }),
})
```

### 访问和修改

```ts
const store = useStore()
store.count++
```

```vue
<input v-model="store.count" type="number" />
```

### 使用 $patch 修改

一次性应用多个更改:

```ts
// 对象语法
store.$patch({
  count: store.count + 1,
  name: 'DIO',
})

// 函数语法 (用于复杂修改)
store.$patch((state) => {
  state.items.push({ name: 'shoes', quantity: 1 })
  state.hasChanged = true
})
```

### 重置 State

Option Stores 有内置的 `$reset()`。对于 Setup Stores，实现你自己的:

```ts
export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)

  function $reset() {
    count.value = 0
  }

  return { count, $reset }
})
```

### 订阅 State 变化

```ts
cartStore.$subscribe((mutation, state) => {
  mutation.type // 'direct' | 'patch object' | 'patch function'
  mutation.storeId // 'cart'
  mutation.payload // patch 对象 (仅用于 'patch object')

  localStorage.setItem('cart', JSON.stringify(state))
})

// 选项
cartStore.$subscribe(callback, { flush: 'sync' }) // 立即
cartStore.$subscribe(callback, { detached: true }) // 卸载后保持
```

---

## Getters

Getters 是计算值，等同于 Vue 的 `computed()`。

### 基本 Getters

```ts
getters: {
  doubleCount: (state) => state.count * 2,
}
```

### 访问其他 Getters

使用 `this` 并指定显式返回类型:

```ts
getters: {
  doubleCount: (state) => state.count * 2,
  doublePlusOne(): number {
    return this.doubleCount + 1
  },
},
```

### 带参数的 Getters

返回一个函数 (注意: 失去缓存):

```ts
getters: {
  getUserById: (state) => {
    return (userId: string) => state.users.find((user) => user.id === userId)
  },
},
```

在参数化 getters 中缓存:

```ts
getters: {
  getActiveUserById(state) {
    const activeUsers = state.users.filter((user) => user.active)
    return (userId: string) => activeUsers.find((user) => user.id === userId)
  },
},
```

### 在 Getters 中访问其他 Stores

```ts
import { useOtherStore } from './other-store'

getters: {
  combined(state) {
    const otherStore = useOtherStore()
    return state.localData + otherStore.data
  },
},
```

---

## Actions

Actions 是业务逻辑的方法。与 getters 不同，它们可以是异步的。

### 定义 Actions

```ts
actions: {
  increment() {
    this.count++
  },
  randomizeCounter() {
    this.count = Math.round(100 * Math.random())
  },
},
```

### 异步 Actions

```ts
actions: {
  async registerUser(login: string, password: string) {
    try {
      this.userData = await api.post({ login, password })
    } catch (error) {
      return error
    }
  },
},
```

### 在 Actions 中访问其他 Stores

```ts
import { useAuthStore } from './auth-store'

actions: {
  async fetchUserPreferences() {
    const auth = useAuthStore()
    if (auth.isAuthenticated) {
      this.preferences = await fetchPreferences()
    }
  },
},
```

**SSR:** 在任何 `await` 之前调用所有 `useStore()`:

```ts
async orderCart() {
  // ✅ 在 await 之前调用 store
  const user = useUserStore()

  await apiOrderCart(user.token, this.items)
  // ❌ 不要在 SSR 中 await 之后调用 useStore()
}
```

### 订阅 Actions

```ts
const unsubscribe = someStore.$onAction(
  ({ name, store, args, after, onError }) => {
    const startTime = Date.now()
    console.log(`Start "${name}" with params [${args.join(', ')}]`)

    after((result) => {
      console.log(`Finished "${name}" after ${Date.now() - startTime}ms`)
    })

    onError((error) => {
      console.warn(`Failed "${name}": ${error}`)
    })
  }
)

unsubscribe() // 清理
```

在组件卸载后保持订阅:

```ts
someStore.$onAction(callback, true)
```

---

## Options API 辅助函数

```ts
import { mapState, mapWritableState, mapActions } from 'pinia'
import { useCounterStore } from '../stores/counter'

export default {
  computed: {
    // 只读 state/getters
    ...mapState(useCounterStore, ['count', 'doubleCount']),
    // 可写 state
    ...mapWritableState(useCounterStore, ['count']),
  },
  methods: {
    ...mapActions(useCounterStore, ['increment']),
  },
}
```

---

## 在 Setup Stores 中访问全局提供者

```ts
import { inject } from 'vue'
import { useRoute } from 'vue-router'
import { defineStore } from 'pinia'

export const useSearchFilters = defineStore('search-filters', () => {
  const route = useRoute()
  const appProvided = inject('appProvided')

  // 不要返回这些 - 在组件中直接访问
  return { /* ... */ }
})
```

<!--
源引用:
- https://pinia.vuejs.org/core-concepts/
- https://pinia.vuejs.org/core-concepts/state.html
- https://pinia.vuejs.org/core-concepts/getters.html
- https://pinia.vuejs.org/core-concepts/actions.html
-->
