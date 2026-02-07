---
title: 每个路由的 beforeEnter 守卫忽略参数/查询变化
impact: MEDIUM
impactDescription: 路由级别的 beforeEnter 守卫仅当从不同路由进入时才会触发，当只有参数、查询字符串或哈希变化时不会触发，可能导致绕过重要的验证逻辑
type: gotcha
tags: [vue3, vue-router, navigation-guards, params, query]
---

# 每个路由的 beforeEnter 守卫忽略参数/查询变化

**影响：中等** - 在路由配置中定义的 `beforeEnter` 守卫仅当从不同路由进入路由时才会触发。同一路由内的参数、查询字符串或哈希的变化不会触发 `beforeEnter`，可能会绕过重要的验证逻辑。

## 任务清单

- [ ] 使用组件内 `onBeforeRouteUpdate` 处理参数/查询变化
- [ ] 或使用全局 `beforeEach` 并检查 route.params/query
- [ ] 记录哪些守卫保护哪些场景
- [ ] 测试在相同路由之间使用不同参数的导航

## 问题所在

```javascript
// router.js
const routes = [
  {
    path: '/orders/:id',
    component: OrderDetail,
    beforeEnter: async (to, from) => {
      // 从 /products 进入时运行
      // 但从 /orders/1 导航到 /orders/2 时不会运行！
      const order = await checkOrderAccess(to.params.id)
      if (!order.canView) {
        return '/unauthorized'
      }
    }
  }
]
```

**场景：**
1. 用户从 `/products` 导航到 `/orders/1` - beforeEntry 运行，检查访问权限
2. 用户从 `/orders/1` 导航到 `/orders/2` - beforeEntry 不会运行！
3. 用户可能会访问他们没有权限的订单！

## 什么会触发 beforeEntry，什么不会

| 导航 | beforeEntry 触发？ |
|------------|-------------------|
| `/products` → `/orders/1` | 是 |
| `/orders/1` → `/orders/2` | 否 |
| `/orders/1` → `/orders/1?tab=details` | 否 |
| `/orders/1#section` → `/orders/1#other` | 否 |
| `/orders/1` → `/products` → `/orders/2` | 是（离开并重新进入） |

## 解决方案 1：添加组件内守卫

```vue
<!-- OrderDetail.vue -->
<script setup>
import { onBeforeRouteUpdate } from 'vue-router'

// 处理同一路由内的参数变化
onBeforeRouteUpdate(async (to, from) => {
  if (to.params.id !== from.params.id) {
    const order = await checkOrderAccess(to.params.id)
    if (!order.canView) {
      return '/unauthorized'
    }
  }
})
</script>
```

## 解决方案 2：改用全局 beforeEach

```javascript
// router.js
router.beforeEach(async (to, from) => {
  // 全局处理所有订单访问检查
  if (to.name === 'OrderDetail') {
    // 每次导航到此路由时都会运行，包括参数变化
    const order = await checkOrderAccess(to.params.id)
    if (!order.canView) {
      return '/unauthorized'
    }
  }
})
```

## 解决方案 3：结合两种守卫

```javascript
// router.js - 用于从不同路由进入
const routes = [
  {
    path: '/orders/:id',
    component: OrderDetail,
    beforeEnter: (to) => validateOrderAccess(to.params.id)
  }
]

// 在组件中 - 用于路由内的参数变化
// OrderDetail.vue
onBeforeRouteUpdate((to) => validateOrderAccess(to.params.id))

// 共享验证函数
async function validateOrderAccess(orderId) {
  const order = await checkOrderAccess(orderId)
  if (!order.canView) {
    return '/unauthorized'
  }
}
```

## 解决方案 4：使用带有守卫数组的 beforeEnter

```javascript
// guards/orderGuards.js
export const orderAccessGuard = async (to) => {
  const order = await checkOrderAccess(to.params.id)
  if (!order.canView) {
    return '/unauthorized'
  }
}

// router.js
const routes = [
  {
    path: '/orders/:id',
    component: OrderDetail,
    beforeEnter: [orderAccessGuard]  // 可以添加多个守卫
  }
]

// 仍然需要组件内守卫来处理参数变化！
```

## 完整的导航守卫执行顺序

了解每种守卫类型何时触发：

```
1. beforeRouteLeave（组件内，离开组件）
2. beforeEach（全局）
3. beforeEnter（每个路由，仅当从不同路由进入时）
4. beforeRouteEnter（组件内，进入组件）
5. beforeResolve（全局）
6. afterEach（全局，导航确认后）

对于同一路由上的参数/查询变化：
1. beforeRouteUpdate（组件内）- 只有这个会触发！
2. beforeEach（全局）
3. beforeResolve（全局）
4. afterEach（全局）
```

## 关键要点

1. **beforeEntry 仅用于路由进入** - 不用于路由内变化
2. **使用 onBeforeRouteUpdate 处理参数变化** - 这是组件内解决方案
3. **全局 beforeEach 始终运行** - 适合集中验证
4. **测试参数变化场景** - 开发过程中容易遗漏
5. **考虑安全影响** - 基于参数的访问控制需要两种守卫

## 参考
- [Vue Router 导航守卫](https://router.vuejs.org/guide/advanced/navigation-guards.html)
- [Vue Router 每个路由守卫](https://router.vuejs.org/guide/advanced/navigation-guards.html#per-route-guard)
