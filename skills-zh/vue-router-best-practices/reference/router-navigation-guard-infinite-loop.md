---
title: 导航守卫无限重定向循环
impact: HIGH
impactDescription: 配置错误的导航守卫可能会将用户困在无限重定向循环中，导致浏览器崩溃或应用无法使用
type: gotcha
tags: [vue3, vue-router, navigation-guards, redirect, debugging]
---

# 导航守卫无限重定向循环

**影响：高** - 导航守卫中的一个常见错误是创建导致无限重定向的条件。Vue Router 会检测到这一点并显示警告，但在生产环境中，它可能会导致浏览器崩溃或创建糟糕的用户体验。

## 任务清单

- [ ] 重定向前始终检查是否已在目标路由上
- [ ] 使用所有可能的导航场景测试守卫逻辑
- [ ] 添加路由 meta 来控制哪些路由需要保护
- [ ] 使用 Vue Router devtools 调试重定向链

## 问题所在

```javascript
// 错误：无限循环 - 始终重定向到登录，即使在登录页上！
router.beforeEach((to, from) => {
  if (!isAuthenticated()) {
    return '/login'  // 重定向到 /login，这会再次触发守卫...
  }
})

// 错误：两个路由之间的循环重定向
router.beforeEach((to, from) => {
  if (to.path === '/dashboard' && !hasProfile()) {
    return '/profile'
  }
  if (to.path === '/profile' && !isVerified()) {
    return '/dashboard'  // 回到仪表板，这会转到个人资料...
  }
})
```

**你会看到的错误：**
```
[Vue Router warn]: Detected an infinite redirection in a navigation guard when going from "/" to "/login". Aborting to avoid a Stack Overflow.
```

## 解决方案 1：排除目标路由

```javascript
// 正确：如果已经转到登录则不重定向
router.beforeEach((to, from) => {
  if (!isAuthenticated() && to.path !== '/login') {
    return '/login'
  }
})

// 正确：使用路由名称进行更清晰的检查
router.beforeEach((to, from) => {
  const publicPages = ['Login', 'Register', 'ForgotPassword']

  if (!isAuthenticated() && !publicPages.includes(to.name)) {
    return { name: 'Login' }
  }
})
```

## 解决方案 2：使用路由 Meta 字段

```javascript
// router.js
const routes = [
  {
    path: '/login',
    name: 'Login',
    component: Login,
    meta: { requiresAuth: false }
  },
  {
    path: '/dashboard',
    name: 'Dashboard',
    component: Dashboard,
    meta: { requiresAuth: true }
  },
  {
    path: '/public',
    name: 'PublicPage',
    component: PublicPage,
    meta: { requiresAuth: false }
  }
]

// 守卫检查 meta 字段
router.beforeEach((to, from) => {
  // 仅在路由需要身份验证时重定向
  if (to.meta.requiresAuth && !isAuthenticated()) {
    return { name: 'Login', query: { redirect: to.fullPath } }
  }
})
```

## 解决方案 3：谨慎处理重定向链

```javascript
// 正确：打破潜在的循环重定向
router.beforeEach((to, from) => {
  // 通过跟踪重定向深度来防止重定向循环
  const redirectCount = to.query._redirectCount || 0

  if (redirectCount > 3) {
    console.error('重定向次数过多，停止于：', to.path)
    return '/error'  // 逃生口
  }

  if (needsRedirect(to)) {
    return {
      path: getRedirectTarget(to),
      query: { ...to.query, _redirectCount: redirectCount + 1 }
    }
  }
})
```

## 解决方案 4：集中式重定向逻辑

```javascript
// guards/auth.js
export function createAuthGuard(router) {
  const publicRoutes = new Set(['Login', 'Register', 'ForgotPassword', 'ResetPassword'])
  const guestOnlyRoutes = new Set(['Login', 'Register'])

  router.beforeEach((to, from) => {
    const isPublic = publicRoutes.has(to.name)
    const isGuestOnly = guestOnlyRoutes.has(to.name)
    const isLoggedIn = isAuthenticated()

    // 未登录，尝试访问受保护的路由
    if (!isLoggedIn && !isPublic) {
      return { name: 'Login', query: { redirect: to.fullPath } }
    }

    // 已登录，尝试访问仅限访客的路由（如登录页面）
    if (isLoggedIn && isGuestOnly) {
      return { name: 'Dashboard' }
    }

    // 所有其他情况：继续
  })
}
```

## 调试重定向循环

```javascript
// 添加日志以了解重定向链
router.beforeEach((to, from) => {
  console.log(`导航：${from.path} -> ${to.path}`)
  console.log('身份验证状态：', isAuthenticated())
  console.log('路由 meta：', to.meta)

  // 你的守卫逻辑
})

// 或使用 afterEach 来确认导航
router.afterEach((to, from) => {
  console.log(`已导航：${from.path} -> ${to.path}`)
})
```

## 常见重定向循环模式

| 模式 | 问题 | 修复 |
|---------|---------|-----|
| 无排除的身份验证检查 | 登录重定向到登录 | 从检查中排除 `/login` |
| 带有循环依赖的基于角色 | 管理员 -> 用户 -> 管理员 | 使用单一事实来源来处理角色要求 |
| 入门流程 | 步骤 1 -> 步骤 2 -> 步骤 1 | 正确跟踪完成状态 |
| 重定向查询处理 | 读取重定向创建新的重定向 | 仅处理一次重定向 |

## 关键要点

1. **始终排除目标路由** - 永远不要重定向到会触发相同重定向的路由
2. **使用路由 meta 字段** - 比路径字符串比较更清晰
3. **测试边缘情况** - 直接 URL 访问、刷新、返回按钮
4. **在开发期间添加日志** - 有助于跟踪重定向链
5. **有一个逃生口** - 错误页面或最大重定向计数

## 参考
- [Vue Router 导航守卫](https://router.vuejs.org/guide/advanced/navigation-guards.html)
- [Vue Router 路由 Meta 字段](https://router.vuejs.org/guide/advanced/meta.html)
