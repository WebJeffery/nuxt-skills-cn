---
title: 避免在 Composables 中隐藏副作用
impact: HIGH
impactDescription: 隐藏在 composables 中的副作用使调试困难并在组件之间创建隐式耦合
type: best-practice
tags: [vue3, composables, composition-api, side-effects, provide-inject, global-state]
---

# 避免在 Composables 中隐藏副作用

**影响：高** - Composables 应该封装有状态逻辑，而不是隐藏影响其范围之外事物的副作用。隐藏的副作用（如修改全局状态、内部使用 provide/inject 或直接操作 DOM）使 composables 不可预测且难以调试。

当 composable 具有意外的副作用时，消费者无法推断调用它将做什么。这导致难以追踪的 bug 和无法安全重用的 composables。

## 任务清单

- [ ] 避免在 composables 内部使用 provide/inject（使依赖显式）
- [ ] 不要在内部修改 Pinia/Vuex store 状态（改为接受 store 作为参数）
- [ ] 不要直接操作 DOM（使用作为参数传递的模板 refs）
- [ ] 清楚地记录任何不可避免的副作用
- [ ] 保持 composables 专注于返回响应式状态和方法

**错误：**
```javascript
// 错误：隐藏的 provide/inject 依赖
export function useTheme() {
  // 消费者不知道这依赖于提供的主题
  const theme = inject('theme')  // 如果没有提供这个怎么办？

  const isDark = computed(() => theme?.mode === 'dark')
  return { isDark }
}

// 错误：在内部修改全局 store
import { useUserStore } from '@/stores/user'

export function useLogin() {
  const userStore = useUserStore()

  async function login(credentials) {
    const user = await api.login(credentials)
    // 隐藏的副作用：修改全局状态
    userStore.setUser(user)
    userStore.setToken(user.token)
    // 消费者不知道 store 被修改了！
  }

  return { login }
}

// 错误：隐藏的 DOM 操作
export function useFocusTrap() {
  onMounted(() => {
    // 哪个元素？消费者没有控制
    document.querySelector('.modal')?.focus()
  })
}

// 错误：影响后代的隐藏 provide
export function useFormContext() {
  const form = reactive({ values: {}, errors: {} })
  // 调用此组件的组件不知道它提供了什么
  provide('form-context', form)
  return form
}
```

**正确：**
```javascript
// 正确：显式依赖注入
export function useTheme(injectedTheme) {
  // 如果没有传递主题，消费者必须处理它
  const theme = injectedTheme ?? { mode: 'light' }

  const isDark = computed(() => theme.mode === 'dark')
  return { isDark }
}

// 用法 - 依赖是显式的
const theme = inject('theme', { mode: 'light' })
const { isDark } = useTheme(theme)

// 正确：返回操作，让消费者决定何时调用它们
export function useLogin() {
  const user = ref(null)
  const token = ref(null)
  const isLoading = ref(false)
  const error = ref(null)

  async function login(credentials) {
    isLoading.value = true
    error.value = null
    try {
      const response = await api.login(credentials)
      user.value = response.user
      token.value = response.token
      return response
    } catch (e) {
      error.value = e
      throw e
    } finally {
      isLoading.value = false
    }
  }

  return { user, token, isLoading, error, login }
}

// 消费者决定对结果做什么
const { user, token, login } = useLogin()
const userStore = useUserStore()

async function handleLogin(credentials) {
  await login(credentials)
  // 消费者显式更新 store
  userStore.setUser(user.value)
  userStore.setToken(token.value)
}

// 正确：接受元素作为参数
export function useFocusTrap(targetRef) {
  onMounted(() => {
    targetRef.value?.focus()
  })

  onUnmounted(() => {
    // 清理焦点陷阱
  })
}

// 用法 - 消费者控制哪个元素
const modalRef = ref(null)
useFocusTrap(modalRef)

// 正确：将 composable 与提供者分开
export function useFormContext() {
  const form = reactive({ values: {}, errors: {} })
  return form
}

// 在父组件中 - 显式 provide
const form = useFormContext()
provide('form-context', form)
```

## 可接受的副作用（带文档）

当副作用是 composable 的核心目的时，某些副作用是可接受的：

```javascript
/**
 * 全局跟踪鼠标位置。
 *
 * 副作用：
 * - 向 window 添加 'mousemove' 事件监听器（在卸载时清理）
 *
 * @returns {Object} 鼠标坐标 { x, y }
 */
export function useMouse() {
  const x = ref(0)
  const y = ref(0)

  // 此副作用是 composable 的全部意义
  // 并且被正确清理
  onMounted(() => window.addEventListener('mousemove', update))
  onUnmounted(() => window.removeEventListener('mousemove', update))

  function update(event) {
    x.value = event.pageX
    y.value = event.pageY
  }

  return { x, y }
}
```

## 模式：依赖注入以实现灵活性

```javascript
// Composable 接受其依赖项
export function useDataFetcher(apiClient, cache = null) {
  const data = ref(null)

  async function fetch(url) {
    if (cache) {
      const cached = cache.get(url)
      if (cached) {
        data.value = cached
        return
      }
    }

    data.value = await apiClient.get(url)
    cache?.set(url, data.value)
  }

  return { data, fetch }
}

// 用法 - 依赖项是显式且可测试的
const apiClient = inject('apiClient')
const cache = inject('cache', null)
const { data, fetch } = useDataFetcher(apiClient, cache)
```

## 参考
- [Vue.js Composables](https://vuejs.org/guide/reusability/composables.html)
- [创建组合函数的常见错误](https://www.telerik.com/blogs/common-mistakes-creating-composition-functions-vue)
