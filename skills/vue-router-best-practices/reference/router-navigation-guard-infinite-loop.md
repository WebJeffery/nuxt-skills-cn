---
title: 导航守卫中的无限重定向循环
impact: HIGH
impactDescription: 在导航守卫中重定向到当前路由或创建循环重定向链会导致浏览器挂起或崩溃
type: gotcha
tags: [vue3, vue-router, navigation-guards, infinite-loop, redirect]
---

# 导航守卫中的无限重定向循环

**影响: HIGH** - 在导航守卫中重定向到当前路由或创建循环重定向链会导致浏览器挂起或崩溃。这是 Vue Router 中最常见的错误之一,通常发生在认证守卫或条件重定向中。

## 任务清单

- [ ] 检查重定向是否匹配当前路由
- [ ] 检查重定向链是否循环
- [ ] 添加条件以避免重定向到目标路由
- [ ] 使用调试工具跟踪导航流程
- [ ] 测试所有可能的导航路径

## 问题

```javascript
// WRONG: 重定向到当前路由
router.beforeEach((to, from) => {
  if (!isAuthenticated()) {
    return '/login'  // 如果已经在 /login,这会导致无限循环!
  }
})

// WRONG: 循环重定向链
router.beforeEach((to, from) => {
  if (to.path === '/login') {
    if (isAuthenticated()) {
      return '/dashboard'
    }
  }

  if (to.path === '/dashboard') {
    if (!isAuthenticated()) {
      return '/login'
    }
  }
})

// WRONG: 条件始终为真
router.beforeEach((to, from) => {
  if (to.meta.requiresAuth && !isAuthenticated()) {
    return { name: 'Login' }
  }
  // 如果 Login 路由也有 requiresAuth,这会导致循环!
})
```

## 解决方案 1: 检查当前路由

```javascript
// CORRECT: 不要重定向到当前路由
router.beforeEach((to, from) => {
  if (!isAuthenticated() && to.path !== '/login') {
    return '/login'
  }
})

// CORRECT: 使用路由名称
router.beforeEach((to, from) => {
  if (!isAuthenticated() && to.name !== 'Login') {
    return { name: 'Login' }
  }
})
```

## 解决方案 2: 处理认证/未认证状态

```javascript
// CORRECT: 根据认证状态处理
router.beforeEach((to, from) => {
  const isAuthenticated = checkAuth()

  // 如果已认证且尝试访问登录页面,重定向到仪表板
  if (isAuthenticated && to.name === 'Login') {
    return { name: 'Dashboard' }
  }

  // 如果未认证且尝试访问受保护路由,重定向到登录页面
  if (!isAuthenticated && to.meta.requiresAuth) {
    return { name: 'Login', query: { redirect: to.fullPath } }
  }
})
```

## 解决方案 3: 使用公共路由列表

```javascript
// 定义不需要认证的路由
const publicRoutes = ['Login', 'Register', 'Home', 'ForgotPassword']

router.beforeEach((to, from) => {
  const isAuthenticated = checkAuth()

  // 如果已认证且在公共路由上,重定向到仪表板
  if (isAuthenticated && publicRoutes.includes(to.name)) {
    return { name: 'Dashboard' }
  }

  // 如果未认证且不在公共路由上,重定向到登录页面
  if (!isAuthenticated && !publicRoutes.includes(to.name)) {
    return { name: 'Login', query: { redirect: to.fullPath } }
  }
})
```

## 解决方案 4: 使用路由元字段

```javascript
// 路由配置
const routes = [
  {
    path: '/login',
    name: 'Login',
    component: Login,
    meta: { requiresAuth: false, isPublic: true }
  },
  {
    path: '/dashboard',
    name: 'Dashboard',
    component: Dashboard,
    meta: { requiresAuth: true }
  }
]

// 导航守卫
router.beforeEach((to, from) => {
  const isAuthenticated = checkAuth()

  // 如果已认证且在公共路由上,重定向到仪表板
  if (isAuthenticated && to.meta.isPublic) {
    return { name: 'Dashboard' }
  }

  // 如果未认证且需要认证,重定向到登录页面
  if (!isAuthenticated && to.meta.requiresAuth) {
    return { name: 'Login', query: { redirect: to.fullPath } }
  }
})
```

## 解决方案 5: 使用导航历史检查

```javascript
router.beforeEach((to, from) => {
  const isAuthenticated = checkAuth()

  // 如果正在从登录页面重定向到登录页面,停止
  if (from.name === 'Login' && to.name === 'Login') {
    return false  // 取消导航
  }

  // 如果未认证且需要认证
  if (!isAuthenticated && to.meta.requiresAuth) {
    // 如果已经在登录页面,不要重定向
    if (to.name === 'Login') {
      return false
    }
    return { name: 'Login', query: { redirect: to.fullPath } }
  }
})
```

## 调试无限循环

```javascript
// 添加调试日志以跟踪导航
let navigationCount = 0
const MAX_NAVIGATIONS = 10

router.beforeEach((to, from) => {
  navigationCount++

  if (navigationCount > MAX_NAVIGATIONS) {
    console.error('Possible infinite redirect loop detected!')
    console.error('Current route:', to.path)
    console.error('From route:', from.path)
    return false  // 停止导航
  }

  console.log(`Navigation #${navigationCount}: ${from.path} -> ${to.path}`)

  // 你的守卫逻辑
})
```

## 常见循环场景

### 场景 1: 认证循环

```javascript
// 问题: Login 路由也需要认证
const routes = [
  { path: '/login', name: 'Login', meta: { requiresAuth: true } }  // BUG!
]

router.beforeEach((to, from) => {
  if (to.meta.requiresAuth && !isAuthenticated()) {
    return { name: 'Login' }  // 无限循环!
  }
})

// 修复: Login 不应该需要认证
const routes = [
  { path: '/login', name: 'Login', meta: { requiresAuth: false } }
]
```

### 场景 2: 重定向链

```javascript
// 问题: 两个路由相互重定向
router.beforeEach((to, from) => {
  if (to.path === '/route1') {
    return '/route2'
  }
  if (to.path === '/route2') {
    return '/route1'  // 无限循环!
  }
})

// 修复: 添加条件
router.beforeEach((to, from) => {
  if (to.path === '/route1' && someCondition) {
    return '/route2'
  }
  if (to.path === '/route2' && someOtherCondition) {
    return '/route1'
  }
})
```

### 场景 3: 参数更改循环

```javascript
// 问题: 参数更改导致重定向到同一路由
router.beforeEach((to, from) => {
  if (to.params.id !== 'default') {
    return { name: 'SameRoute', params: { id: 'default' } }  // 可能循环!
  }
})

// 修复: 检查是否已经重定向
router.beforeEach((to, from) => {
  if (to.params.id !== 'default' && from.params.id !== 'default') {
    return { name: 'SameRoute', params: { id: 'default' } }
  }
})
```

## 关键点

1. **始终检查当前路由** - 不要重定向到已经在的路由
2. **使用公共路由列表** - 明确定义哪些路由不需要认证
3. **处理认证/未认证状态** - 两个方向都需要考虑
4. **添加调试日志** - 跟踪导航流程以识别循环
5. **测试所有路径** - 确保没有意外的重定向链

## 参考
- [Vue Router 导航守卫](https://router.vuejs.org/guide/advanced/navigation-guards.html)
- [Vue Router 重定向](https://router.vuejs.org/guide/essentials/dynamic-matching.html#redirect)
