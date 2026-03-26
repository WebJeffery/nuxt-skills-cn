---
title: Vue Router 导航守卫 next() 函数已弃用
impact: HIGH
impactDescription: 错误使用已弃用的 next() 函数会导致导航挂起、无限循环或静默失败
type: gotcha
tags: [vue3, vue-router, navigation-guards, migration, async]
---

# Vue Router 导航守卫 next() 函数已弃用

**影响: HIGH** - Vue Router 4 中导航守卫的第三个 `next()` 参数已弃用。虽然为了向后兼容仍然支持,但错误使用它是导致 bug 的最常见原因之一:多次调用、忘记调用或在没有适当逻辑的情况下有条件地调用。

## 任务清单

- [ ] 重构守卫以使用基于返回值的语法而不是 next()
- [ ] 从导航守卫中删除所有 next() 调用
- [ ] 使用 async/await 模式进行异步检查
- [ ] 返回 false 以取消,返回路由以重定向,不返回任何内容以继续

## 问题

```javascript
// WRONG: 使用已弃用的 next() 函数
router.beforeEach((to, from, next) => {
  if (!isAuthenticated) {
    next('/login')  // 容易忘记这个调用
  }
  // BUG: 认证时不调用 next() - 导航挂起!
})

// WRONG: 多次 next() 调用
router.beforeEach((to, from, next) => {
  if (!isAuthenticated) {
    next('/login')
  }
  next()  // BUG: 未认证时调用两次!
})

// WRONG: 在异步代码中使用 next() 而没有正确处理
router.beforeEach(async (to, from, next) => {
  const user = await fetchUser()
  if (!user) {
    next('/login')
  }
  next()  // 重定向后仍然调用!
})
```

## 解决方案: 使用基于返回值的守卫

```javascript
// CORRECT: 基于返回值的语法(现代 Vue Router 4+)
router.beforeEach((to, from) => {
  if (!isAuthenticated) {
    return '/login'  // 重定向
  }
  // 不返回任何内容(undefined)以继续
})

// CORRECT: 返回 false 以取消导航
router.beforeEach((to, from) => {
  if (hasUnsavedChanges) {
    return false  // 取消导航
  }
})

// CORRECT: 带返回值的异步
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
  // 不返回任何内容/undefined - 允许导航
  return

  // 返回 false - 取消导航,停留在当前路由
  return false

  // 返回字符串路径 - 重定向到路径
  return '/login'

  // 返回路由对象 - 带有完全控制的重定向
  return { name: 'Login', query: { redirect: to.fullPath } }

  // 返回 Error - 取消并触发 router.onError()
  return new Error('Navigation cancelled')
})
```

## 如果必须使用 next()(遗留代码)

如果维护使用 `next()` 的遗留代码,请严格遵循这些规则:

```javascript
// CORRECT: 每个代码路径恰好一个 next() 调用
router.beforeEach((to, from, next) => {
  if (!isAuthenticated) {
    next('/login')
    return  // 关键: 调用 next() 后退出
  }

  if (!hasPermission(to)) {
    next('/forbidden')
    return  // 关键: 调用 next() 后退出
  }

  next()  // 只有在所有检查通过时才到达
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
    // 记录错误并继续(或返回 false)
    console.error('Access validation failed:', error)
    return false
  }
})
```

## 关键点

1. **首选基于返回值的语法** - 更清晰,更少错误,现代标准
2. **next() 必须恰好调用一次** - 如果使用遗留语法,确保每个路径单个调用
3. **重定向后始终返回/退出** - 防止多个导航操作
4. **异步守卫自然工作** - 只需返回重定向路由或不返回任何内容
5. **测试所有代码路径** - 每个分支必须导致返回或 next()

## 参考
- [Vue Router 导航守卫](https://router.vuejs.org/guide/advanced/navigation-guards.html)
- [RFC: 从导航守卫中移除 next()](https://github.com/vuejs/rfcs/discussions/302)
