---
title: 异步导航守卫需要正确的 Promise 处理
impact: MEDIUM
impactDescription: 守卫中未等待的 Promise 会导致导航在异步检查完成之前完成，允许未经授权的访问或数据缺失
type: gotcha
tags: [vue3, vue-router, navigation-guards, async, promises]
---

# 异步导航守卫需要正确的 Promise 处理

**影响：中等** - 执行异步操作（API 调用、身份验证检查）的导航守卫必须正确处理 Promise。如果不等待异步操作或返回 Promise，导航将在检查完成之前完成，可能会允许未经授权的访问或使用不完整的数据进行导航。

## 任务清单

- [ ] 在导航守卫中使用 async/await
- [ ] 如果不使用 async/await，则返回 Promise
- [ ] 为长时间异步操作添加加载状态
- [ ] 为慢速 API 调用实现超时
- [ ] 处理错误以防止导航挂起

## 问题所在

```javascript
// 错误：未等待 - 导航立即继续
router.beforeEach((to, from) => {
  if (to.meta.requiresAuth) {
    checkAuth()  // 这返回一个 Promise 但我们没有等待！
    // 在 checkAuth 完成之前导航继续
  }
})

// 错误：异步函数但忘记返回
router.beforeEach(async (to, from) => {
  if (to.meta.requiresAuth) {
    const isValid = await checkAuth()
    if (!isValid) {
      // 这个重定向可能在导航已经完成后发生！
      return '/login'
    }
  }
  // 缺少返回 - 隐式返回 undefined，允许导航
})
```

## 解决方案：正确的 Async/Await 模式

```javascript
// 正确：带有正确返回的异步函数
router.beforeEach(async (to, from) => {
  if (to.meta.requiresAuth) {
    try {
      const isAuthenticated = await checkAuth()

      if (!isAuthenticated) {
        return { name: 'Login', query: { redirect: to.fullPath } }
      }
    } catch (error) {
      console.error('身份验证检查失败：', error)
      return { name: 'Error', params: { message: '身份验证失败' } }
    }
  }
  // 显式返回无内容以继续
  return true
})
```

## 解决方案：基于 Promise 的模式（替代方案）

```javascript
// 正确：显式返回 Promise
router.beforeEach((to, from) => {
  if (to.meta.requiresAuth) {
    return checkAuth()
      .then(isAuthenticated => {
        if (!isAuthenticated) {
          return { name: 'Login' }
        }
      })
      .catch(error => {
        console.error('身份验证检查失败：', error)
        return { name: 'Error' }
      })
  }
})
```

## 异步守卫期间的加载状态

```javascript
// app/composables/useNavigationLoading.js
import { ref } from 'vue'

const isNavigating = ref(false)

export function useNavigationLoading() {
  return { isNavigating }
}

export function setupNavigationLoading(router) {
  router.beforeEach(() => {
    isNavigating.value = true
  })

  router.afterEach(() => {
    isNavigating.value = false
  })

  router.onError(() => {
    isNavigating.value = false
  })
}
```

```vue
<!-- App.vue -->
<script setup>
import { useNavigationLoading } from '@/composables/useNavigationLoading'

const { isNavigating } = useNavigationLoading()
</script>

<template>
  <LoadingBar v-if="isNavigating" />
  <router-view />
</template>
```

## 慢速 API 的超时模式

```javascript
// 正确：添加超时以防止无限等待
function withTimeout(promise, ms = 5000) {
  return Promise.race([
    promise,
    new Promise((_, reject) =>
      setTimeout(() => reject(new Error('请求超时')), ms)
    )
  ])
}

router.beforeEach(async (to, from) => {
  if (to.meta.requiresAuth) {
    try {
      const isValid = await withTimeout(checkAuth(), 5000)
      if (!isValid) {
        return '/login'
      }
    } catch (error) {
      if (error.message === '请求超时') {
        // 让用户通过但显示警告
        console.warn('身份验证检查超时')
      } else {
        return '/login'
      }
    }
  }
})
```

## 多个异步检查

```javascript
// 正确：并行运行独立检查
router.beforeEach(async (to, from) => {
  if (to.meta.requiresAuth && to.meta.requiresSubscription) {
    try {
      const [isAuthenticated, hasSubscription] = await Promise.all([
        checkAuth(),
        checkSubscription()
      ])

      if (!isAuthenticated) {
        return '/login'
      }

      if (!hasSubscription) {
        return '/subscribe'
      }
    } catch (error) {
      return '/error'
    }
  }
})
```

## 错误处理最佳实践

```javascript
router.beforeEach(async (to, from) => {
  try {
    // 你的异步逻辑
    await performChecks(to)
  } catch (error) {
    // 始终处理错误以防止导航挂起

    if (error.response?.status === 401) {
      return '/login'
    }

    if (error.response?.status === 403) {
      return '/forbidden'
    }

    if (error.code === 'NETWORK_ERROR') {
      // 离线 - 也许允许导航但显示警告
      return true
    }

    // 未知错误 - 重定向到错误页面
    console.error('导航守卫错误：', error)
    return { name: 'Error', state: { error: error.message } }
  }
})
```

## 关键要点

1. **始终等待异步操作** - 否则导航立即继续
2. **返回值很重要** - 返回路由以重定向，返回 false 以取消，返回 true/undefined 以继续
3. **处理所有错误情况** - 未捕获的错误可能导致导航挂起
4. **添加超时** - 慢速 API 不应无限期阻止导航
5. **显示加载状态** - 用户在异步检查期间需要反馈
6. **并行化独立检查** - 使用 Promise.all 以获得更好的性能

## 参考
- [Vue Router 导航守卫](https://router.vuejs.org/guide/advanced/navigation-guards.html)
- [Vue Router 导航失败](https://router.vuejs.org/guide/advanced/navigation-failures.html)
