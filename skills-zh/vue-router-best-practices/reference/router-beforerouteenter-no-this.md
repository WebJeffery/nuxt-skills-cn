---
title: beforeRouteEnter 无法访问组件实例
impact: MEDIUM
impactDescription: beforeRouteEnter 守卫在组件创建之前运行，因此 'this' 未定义；使用 next 回调来访问实例
type: gotcha
tags: [vue3, vue-router, navigation-guards, lifecycle, this]
---

# beforeRouteEnter 无法访问组件实例

**影响：中等** - `beforeRouteEnter` 组件内导航守卫在组件创建之前执行，意味着你无法访问 `this` 或任何组件实例属性。这是唯一支持在 `next()` 函数中使用回调来在导航后访问组件实例的导航守卫。

## 任务清单

- [ ] 使用 next(vm => ...) 回调来访问组件实例
- [ ] 或使用具有不同模式的组合式 API 守卫
- [ ] 根据时机需求适当移动数据获取逻辑
- [ ] 考虑使用全局守卫来处理不需要组件访问的数据

## 问题所在

```javascript
// Options API - 错误：this 未定义
export default {
  data() {
    return { user: null }
  },
  beforeRouteEnter(to, from, next) {
    // 错误：this 在这里未定义 - 组件还不存在！
    this.user = await fetchUser(to.params.id)  // 错误！
    next()
  }
}
```

## 解决方案：使用 next() 回调（Options API）

```javascript
// Options API - 正确：使用回调来访问 vm
export default {
  data() {
    return {
      user: null,
      loading: true
    }
  },

  beforeRouteEnter(to, from, next) {
    // 在组件存在之前获取数据
    fetchUser(to.params.id)
      .then(user => {
        // 将回调传递给 next() - 接收组件实例作为 'vm'
        next(vm => {
          vm.user = user
          vm.loading = false
        })
      })
      .catch(error => {
        next(vm => {
          vm.error = error
          vm.loading = false
        })
      })
  }
}
```

## 解决方案：异步 beforeRouteEnter（Options API）

```javascript
export default {
  data() {
    return { userData: null }
  },

  async beforeRouteEnter(to, from, next) {
    try {
      const user = await fetchUser(to.params.id)

      // 仍然需要回调来访问组件
      next(vm => {
        vm.userData = user
      })
    } catch (error) {
      // 出错时重定向
      next('/error')
    }
  }
}
```

## 组合式 API 替代方案

在使用 `<script setup>` 的组合式 API 中，你不能直接使用 `beforeRouteEnter`，因为组件实例正在设置中。请使用不同的模式：

```vue
<script setup>
import { ref, onMounted } from 'vue'
import { useRoute, onBeforeRouteUpdate } from 'vue-router'

const route = useRoute()
const user = ref(null)
const loading = ref(true)

// 选项 1：在 onMounted 中获取（组件存在之后）
onMounted(async () => {
  user.value = await fetchUser(route.params.id)
  loading.value = false
})

// 选项 2：处理后续参数变化
onBeforeRouteUpdate(async (to, from) => {
  if (to.params.id !== from.params.id) {
    loading.value = true
    user.value = await fetchUser(to.params.id)
    loading.value = false
  }
})
</script>
```

## 路由级别数据获取

对于应该在导航之前加载的数据，请使用路由级别守卫：

```javascript
// router.js
const routes = [
  {
    path: '/users/:id',
    component: () => import('./UserProfile.vue'),
    beforeEnter: async (to, from) => {
      try {
        // 存储数据供组件访问
        const user = await fetchUser(to.params.id)
        to.meta.user = user  // 附加到路由 meta
      } catch (error) {
        return '/error'
      }
    }
  }
]
```

```vue
<!-- UserProfile.vue -->
<script setup>
import { useRoute } from 'vue-router'

const route = useRoute()
// 从 meta 访问预获取的数据
const user = route.meta.user
</script>
```

## 导航守卫比较

| 守卫 | 有 `this`/组件？ | 可以延迟导航？ | 用例 |
|-------|----------------------|----------------------|----------|
| beforeRouteEnter | 否（使用 next 回调） | 是 | 预获取，数据缺失时重定向 |
| beforeRouteUpdate | 是 | 是 | 响应参数变化 |
| beforeRouteLeave | 是 | 是 | 未保存更改警告 |
| 全局 beforeEach | 否 | 是 | 身份验证检查 |
| 路由 beforeEnter | 否 | 是 | 路由特定验证 |

## 关键要点

1. **beforeRouteEnter 在组件创建之前运行** - 无法访问 `this`
2. **使用 next(vm => ...) 回调** - 访问组件实例的唯一方法
3. **组合式 API 有局限性** - 改用 onMounted 或全局守卫
4. **考虑使用路由 meta 存储预获取数据** - 关注点分离清晰
5. **beforeRouteUpdate 和 beforeRouteLeave 可以访问组件** - 它们在组件存在时运行

## 参考
- [Vue Router 组件内守卫](https://router.vuejs.org/guide/advanced/navigation-guards.html#in-component-guards)
- [Vue Router 导航解析流程](https://router.vuejs.org/guide/advanced/navigation-guards.html#the-full-navigation-resolution-flow)
