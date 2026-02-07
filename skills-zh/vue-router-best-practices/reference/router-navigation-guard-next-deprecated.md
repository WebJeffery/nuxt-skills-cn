---
title: Vue Router 导航守卫 next() 函数已弃用
impact: HIGH
impactDescription: 错误使用已弃用的 next() 函数会导致导航挂起、无限循环或静默失败
type: gotcha
tags: [vue3, vue-router, navigation-guards, migration, async]
---

# Vue Router 导航守卫 next() 函数已弃用

**影响：高** - Vue Router 4 中已弃用导航守卫中的第三个 `next()` 参数。虽然为了向后兼容仍然支持，但错误使用它是导致错误的最常见原因之一：多次调用、忘记调用或在没有适当逻辑的情况下有条件地调用。

## 任务清单

- [ ] 将守卫重构为使用基于返回的语法而不是 next()
- [ ] 从导航守卫中删除所有 next() 调用
- [ ] 对异步检查使用 async/await 模式
- [ ] 返回 false 以取消，返回路由以重定向，返回无内容以继续

## 问题所在

```javascript
// 错误：使用已弃用的 next() 函数
router.beforeEach((to, from, next) => {
  if (!isAuthenticated) {
    next('/login')  // 容易忘记这个调用
  }
  // 错误：经过身份验证时未调用 next() - 导航挂起！
})

// 错误：多次 next() 调用
router.beforeEach((to, from, next) => {
  if (!isAuthenticated) {
    next('/login')
  }
  next()  // 错误：未经过身份验证时调用了两次！
})

// 错误：异步代码中的 next() 没有正确处理
router.beforeEach(async (to, from, next) => {
  const user = await fetchUser()
  if (!user) {
    next('/login')
  }
  next()  // 即使在重定向后仍然会被调用！
})
```

## 解决方案：使用基于返回的守卫

```javascript
// 正确：基于返回的语法（现代 Vue Router 4+）
router.beforeEach((to, from) => {
  if (!isAuthenticated) {
    return '/login'  // 重定向
  }
  // 返回无内容（undefined）以继续
})

// 正确：返回 false 以取消导航
router.beforeEach((to, from) => {
  if (hasUnsavedChanges) {
    return false  // 取消导航
  }
})

// 正确：带有基于返回语法的异步
router.beforeEach(async (to, from) => {
  const user = await fetchUser()
  if (!user) {
    return { name: 'Login', query: { redirect: to.fullPath } }
  }
  // 继续导航
})
```

## 返回值说明

```javascript
router.beforeEach((to, from) => {
  // 返回无内容/undefined - 允许导航
  return

  // 返回 false - 取消导航，停留在当前路由
  return false

  // 返回字符串路径 - 重定向到路径
  return '/login'

  // 返回路由对象 - 具有完全控制权的重定向
  return { name: 'Login', query: { redirect: to.fullPath } }

  // 返回 Error - 取消并触发 router.onError()
  return new Error('导航已取消')
})
```

## 如果必须使用 next()（旧代码）

如果维护使用 `next()` 的旧代码，请严格遵循以下规则：

```javascript
// 正确：每个代码路径恰好调用一次 next()
router.beforeEach((to, from, next) => {
  if (!isAuthenticated) {
    next('/login')
    return  // 关键：调用 next() 后退出
  }

  if (!hasPermission(to)) {
    next('/forbidden')
    return  // 关键：调用 next() 后退出
  }

  next()  // 只有在所有检查通过时才会到达
})
```

## 错误处理模式

```javascript
router.beforeEach(async (to, from) => {
  try {
    await validateAccess(to)
    // 继续
  } catch (error) {
    if (error.status === 401) {
      return '/login'
    }
    if (error.status === 403) {
      return '/forbidden'
    }
    // 记录错误并继续（或返回 false）
    console.error('访问验证失败：', error)
    return false
  }
})
```

## 关键要点

1. **首选基于返回的语法** - 更清晰，更不容易出错，现代标准
2. **next() 必须恰好调用一次** - 如果使用旧语法，确保每个路径调用一次
3. **重定向前始终返回/退出** - 防止多个导航操作
4. **异步守卫自然工作** - 只需返回重定向路由或无内容
5. **测试所有代码路径** - 每个分支必须导致返回或 next()

## 参考
- [Vue Router 导航守卫](https://router.vuejs.org/guide/advanced/navigation-guards.html)
- [RFC：从导航守卫中移除 next()](https://github.com/vuejs/rfcs/discussions/302)
