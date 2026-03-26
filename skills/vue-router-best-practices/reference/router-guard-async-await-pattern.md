---
title: 导航守卫中的异步 API 调用需要正确的 async/await 模式
impact: HIGH
impactDescription: 在导航守卫中进行 API 调用而不使用 async/await 会导致导航在 API 调用完成前继续,使守卫无效
type: gotcha
tags: [vue3, vue-router, navigation-guards, async, await, api]
---

# 导航守卫中的异步 API 调用需要正确的 async/await 模式

**影响: HIGH** - 在导航守卫中调用 API 时,必须正确使用 `async/await` 或返回 Promise。如果忘记等待异步操作,导航将在 API 调用完成前继续,导致守卫失效和潜在的安全漏洞。

## 任务清单

- [ ] 将导航守卫标记为 `async`
- [ ] 使用 `await` 等待 API 调用
- [ ] 根据结果返回重定向或允许导航
- [ ] 在守卫中处理错误
- [ ] 测试守卫在慢速 API 下的行为

## 问题

```javascript
// WRONG: 没有 async/await
router.beforeEach((to, from, next) => {
  if (to.meta.requiresAuth) {
    checkAuth()  // 返回 Promise,但我们没有等待它!
    if (!isAuthenticated) {  // 此检查在 API 调用完成前运行!
      return '/login'
    }
  }
})

// WRONG: 使用 next() 但没有等待
router.beforeEach((to, from, next) => {
  if (to.meta.requiresAuth) {
    checkAuth().then(() => {
      if (!isAuthenticated) {
        next('/login')
      }
    })
    next()  // BUG: 立即调用,不等待 then()!
  }
})

// WRONG: 混合 async/await 和 next()
router.beforeEach(async (to, from, next) => {
  if (to.meta.requiresAuth) {
    await checkAuth()
    if (!isAuthenticated) {
      next('/login')
      return
    }
  }
  next()  // BUG: 不应将 next() 与 async/await 一起使用
})
```

## 解决方案: 使用 async/await 和返回值

```javascript
// CORRECT: 带返回值的 async 函数
router.beforeEach(async (to, from) => {
  if (to.meta.requiresAuth) {
    const isAuthenticated = await checkAuth()
    if (!isAuthenticated) {
      return { name: 'Login', query: { redirect: to.fullPath } }
    }
  }
  // 返回 undefined(或不返回)以允许导航
})

// CORRECT: 带错误处理
router.beforeEach(async (to, from) => {
  if (to.meta.requiresAuth) {
    try {
      const isAuthenticated = await checkAuth()
      if (!isAuthenticated) {
        return { name: 'Login', query: { redirect: to.fullPath } }
      }
    } catch (error) {
      console.error('Auth check failed:', error)
      return { name: 'Error' }
    }
  }
})

// CORRECT: 带多个检查
router.beforeEach(async (to, from) => {
  // 检查认证
  if (to.meta.requiresAuth) {
    const isAuthenticated = await checkAuth()
    if (!isAuthenticated) {
      return { name: 'Login', query: { redirect: to.fullPath } }
    }
  }

  // 检查角色
  if (to.meta.requiresRole) {
    const hasRole = await checkRole(to.meta.requiresRole)
    if (!hasRole) {
      return { name: 'Forbidden' }
    }
  }

  // 允许导航
})
```

## 组件内守卫

```javascript
// Options API
export default {
  async beforeRouteEnter(to, from, next) {
    try {
      const data = await fetchData(to.params.id)
      next(vm => {
        vm.data = data
      })
    } catch (error) {
      next({ name: 'Error' })
    }
  }
}

// Composition API
import { onBeforeRouteUpdate } from 'vue-router'

onBeforeRouteUpdate(async (to, from) => {
  if (to.params.id !== from.params.id) {
    try {
      await fetchData(to.params.id)
    } catch (error) {
      return { name: 'Error' }
    }
  }
})
```

## 常见模式

### 模式 1: 带重定向的简单认证检查

```javascript
router.beforeEach(async (to, from) => {
  const publicRoutes = ['Login', 'Register', 'Home']

  if (!publicRoutes.includes(to.name)) {
    const isAuthenticated = await checkAuth()
    if (!isAuthenticated) {
      return { name: 'Login', query: { redirect: to.fullPath } }
    }
  }
})
```

### 模式 2: 数据预取

```javascript
router.beforeEach(async (to, from) => {
  if (to.meta.loadData) {
    try {
      const data = await fetchData(to.params.id)
      to.meta.loadedData = data
    } catch (error) {
      return { name: 'Error' }
    }
  }
})
```

### 模式 3: 条件重定向

```javascript
router.beforeEach(async (to, from) => {
  // 如果用户已认证,不要重定向到登录页面
  if (to.name === 'Login') {
    const isAuthenticated = await checkAuth()
    if (isAuthenticated) {
      return { name: 'Dashboard' }
    }
  }

  // 如果用户未认证,不要重定向到仪表板
  if (to.name === 'Dashboard') {
    const isAuthenticated = await checkAuth()
    if (!isAuthenticated) {
      return { name: 'Login' }
    }
  }
})
```

### 模式 4: 带超时的异步守卫

```javascript
async function withTimeout(promise, timeoutMs) {
  const timeout = new Promise((_, reject) =>
    setTimeout(() => reject(new Error('Timeout')), timeoutMs)
  )
  return Promise.race([promise, timeout])
}

router.beforeEach(async (to, from) => {
  if (to.meta.requiresAuth) {
    try {
      const isAuthenticated = await withTimeout(checkAuth(), 5000)
      if (!isAuthenticated) {
        return { name: 'Login' }
      }
    } catch (error) {
      console.error('Auth check timeout:', error)
      return { name: 'Error' }
    }
  }
})
```

## 错误处理最佳实践

```javascript
router.beforeEach(async (to, from) => {
  try {
    if (to.meta.requiresAuth) {
      const isAuthenticated = await checkAuth()
      if (!isAuthenticated) {
        return { name: 'Login', query: { redirect: to.fullPath } }
      }
    }

    if (to.meta.requiresSubscription) {
      const hasSubscription = await checkSubscription()
      if (!hasSubscription) {
        return { name: 'Upgrade' }
      }
    }

    // 所有检查通过,允许导航
  } catch (error) {
    // 根据错误类型处理
    if (error.status === 401) {
      return { name: 'Login', query: { redirect: to.fullPath } }
    }
    if (error.status === 403) {
      return { name: 'Forbidden' }
    }
    if (error.name === 'Timeout') {
      return { name: 'Error', params: { message: 'Request timeout' } }
    }
    // 未知错误
    console.error('Navigation guard error:', error)
    return { name: 'Error' }
  }
})
```

## 关键点

1. **始终使用 async/await** - 导航守卫中的 API 调用必须等待
2. **不要混合 async/await 和 next()** - 选择一种模式
3. **返回重定向对象** - 使用返回值而不是 next()
4. **处理错误** - API 调用可能会失败
5. **测试慢速 API** - 确保守卫在 API 延迟时正确工作

## 参考
- [Vue Router 导航守卫](https://router.vuejs.org/guide/advanced/navigation-guards.html)
- [Vue Router 异步组件](https://router.vuejs.org/guide/advanced/navigation-guards.html#async-navigation-guards)
