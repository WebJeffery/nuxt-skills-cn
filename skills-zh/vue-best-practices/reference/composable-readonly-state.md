---
title: 将状态返回为只读并提供显式更新方法
impact: MEDIUM
impactDescription: 直接暴露可变状态允许在整个代码库中进行不受控制的变异
type: best-practice
tags: [vue3, composables, composition-api, readonly, encapsulation, state-management]
---

# 将状态返回为只读并提供显式更新方法

**影响: 中等** - 当 composable 管理应该只能以受控方式修改的状态时，将状态返回为 `readonly` 并提供显式方法进行更新。这可以防止分散的、不受控制的变异，并使状态更改可追踪和可预测。

暴露原始 refs 允许任何使用者直接修改状态，导致难以跟踪的错误，因为变异可能发生在代码库的任何地方。

## 任务清单

- [ ] 使用 `readonly()` 包装不应该直接修改的状态
- [ ] 为所有有效的状态转换提供显式方法
- [ ] 记录更新状态的预期方式
- [ ] 考虑返回 `shallowReadonly()` 以提高大对象的性能

**不正确:**
```javascript
// 错误: 状态可以被任何使用者完全变异
export function useCart() {
  const items = ref([])
  const total = computed(() =>
    items.value.reduce((sum, item) => sum + item.price * item.quantity, 0)
  )

  return { items, total }  // 任何人都可以直接变异 items！
}

// 使用者代码 - 变异分散在各处
const { items, total } = useCart()

// 在组件 A 中
items.value.push({ id: 1, name: 'Widget', price: 10, quantity: 1 })

// 在组件 B 中 - 不同的变异模式
items.value = items.value.filter(item => item.id !== 1)

// 在组件 C 中 - 直接修改
items.value[0].quantity = 5

// 难以跟踪: 这个项目从哪里来？为什么数量改变了？
```

**正确:**
```javascript
import { ref, computed, readonly } from 'vue'

export function useCart() {
  const items = ref([])

  const total = computed(() =>
    items.value.reduce((sum, item) => sum + item.price * item.quantity, 0)
  )

  const itemCount = computed(() =>
    items.value.reduce((sum, item) => sum + item.quantity, 0)
  )

  // 显式、受控的变异
  function addItem(product, quantity = 1) {
    const existing = items.value.find(item => item.id === product.id)
    if (existing) {
      existing.quantity += quantity
    } else {
      items.value.push({ ...product, quantity })
    }
  }

  function removeItem(productId) {
    const index = items.value.findIndex(item => item.id === productId)
    if (index > -1) {
      items.value.splice(index, 1)
    }
  }

  function updateQuantity(productId, quantity) {
    const item = items.value.find(item => item.id === productId)
    if (item) {
      item.quantity = Math.max(0, quantity)
      if (item.quantity === 0) {
        removeItem(productId)
      }
    }
  }

  function clearCart() {
    items.value = []
  }

  return {
    // 状态是只读的 - 不能直接变异
    items: readonly(items),
    total,
    itemCount,
    // 只有这些方法可以修改状态
    addItem,
    removeItem,
    updateQuantity,
    clearCart
  }
}

// 使用者代码 - 仅受控变异
const { items, total, addItem, removeItem, updateQuantity } = useCart()

// items.value.push(...)  // TypeScript 错误: readonly！
// items.value = []       // TypeScript 错误: readonly！

// 正确的方式 - 通过显式方法
addItem({ id: 1, name: 'Widget', price: 10 })
updateQuantity(1, 3)
removeItem(1)
```

## 模式: 内部 vs 外部状态

保持内部状态私有，暴露只读视图：

```javascript
export function useAuth() {
  // 内部、完全可变
  const _user = ref(null)
  const _token = ref(null)
  const _isLoading = ref(false)
  const _error = ref(null)

  async function login(credentials) {
    _isLoading.value = true
    _error.value = null

    try {
      const response = await api.login(credentials)
      _user.value = response.user
      _token.value = response.token
    } catch (e) {
      _error.value = e.message
      throw e
    } finally {
      _isLoading.value = false
    }
  }

  function logout() {
    _user.value = null
    _token.value = null
  }

  return {
    // 内部状态的只读视图
    user: readonly(_user),
    isAuthenticated: computed(() => !!_user.value),
    isLoading: readonly(_isLoading),
    error: readonly(_error),
    // 状态更改的方法
    login,
    logout
  }
}
```

## 何时使用 readonly vs 不使用

| 使用 `readonly` | 不使用 `readonly` |
|----------------|----------------------|
| 具有特定更新规则的状态 | 简单的双向绑定状态 |
| 组件之间的共享状态 | 表单输入值 |
| 更改时需要验证的状态 | 本地组件状态 |
| 调试变异来源很重要时 | 当使用者需要完全控制时 |

```javascript
// 表单输入 - 使用者应该直接变异
export function useForm(initial) {
  const values = ref({ ...initial })
  return { values }  // 没有 readonly - 它意味着被变异
}

// 带有 min/max 的计数器 - 需要受控变异
export function useCounter(min = 0, max = 100) {
  const _count = ref(min)

  function increment() {
    if (_count.value < max) _count.value++
  }

  function decrement() {
    if (_count.value > min) _count.value--
  }

  return {
    count: readonly(_count),
    increment,
    decrement
  }
}
```

## 性能: shallowReadonly

对于大对象，使用 `shallowReadonly` 避免深度只读转换：

```javascript
export function useLargeDataset() {
  const data = ref([/* 数千个项目 */])

  return {
    // shallowReadonly - 只有顶层是只读的
    // 嵌套属性在技术上仍然是可变的
    // 但 ref 本身不能被重新赋值
    data: shallowReadonly(data)
  }
}
```

## 参考
- [Vue.js 响应式 API - readonly](https://vuejs.org/api/reactivity-core.html#readonly)
- [13 Vue Composables 技巧](https://michaelnthiessen.com/13-vue-composables-tips/)
