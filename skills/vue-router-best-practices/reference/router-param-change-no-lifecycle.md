---
title: 路由参数更改不触发生命周期钩子
impact: HIGH
impactDescription: 在使用不同参数的路由之间导航会重用组件实例,跳过 created/mounted 钩子并留下过时数据
type: gotcha
tags: [vue3, vue-router, lifecycle, params, reactivity]
---

# 路由参数更改不触发生命周期钩子

**影响: HIGH** - 在使用相同组件的路由之间导航时(例如,`/users/1` 到 `/users/2`),Vue Router 为了性能会重用现有的组件实例。这意味着 `onMounted`、`created` 和其他生命周期钩子**不会**触发,导致你拥有来自上一个路由的过时数据。

## 任务清单

- [ ] 使用 `watch` 监听路由参数以获取数据
- [ ] 或使用 `onBeforeRouteUpdate` 组件内守卫
- [ ] 或使用 `:key="route.params.id"` 强制重新创建(效率较低)
- [ ] 不要仅依赖 `onMounted` 获取依赖路由参数的数据

## 问题

```vue
<!-- UserProfile.vue - 用于 /users/:id -->
<script setup>
import { ref, onMounted } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const user = ref(null)

// BUG: 仅在组件首次挂载时运行一次!
// 从 /users/1 导航到 /users/2 不会触发此操作!
onMounted(async () => {
  user.value = await fetchUser(route.params.id)
})
</script>

<template>
  <div>
    <!-- 导航到 /users/2 时仍显示用户 1 的数据! -->
    <h1>{{ user?.name }}</h1>
  </div>
</template>
```

**场景:**
1. 访问 `/users/1` - 组件挂载,获取用户 1 数据
2. 导航到 `/users/2` - 组件被重用,onMounted 不运行
3. UI 仍显示用户 1 的数据!

## 解决方案 1: 监听路由参数(推荐)

```vue
<script setup>
import { ref, watch } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const user = ref(null)
const loading = ref(false)

// 监听参数更改 - 处理初始加载和导航
watch(
  () => route.params.id,
  async (newId) => {
    loading.value = true
    user.value = await fetchUser(newId)
    loading.value = false
  },
  { immediate: true }  // 立即运行以进行初始加载
)
</script>
```

## 解决方案 2: 使用 onBeforeRouteUpdate 守卫

```vue
<script setup>
import { ref, onMounted } from 'vue'
import { useRoute, onBeforeRouteUpdate } from 'vue-router'

const route = useRoute()
const user = ref(null)

async function loadUser(id) {
  user.value = await fetchUser(id)
}

// 初始加载
onMounted(() => loadUser(route.params.id))

// 处理同一路由内的参数更改
onBeforeRouteUpdate(async (to, from) => {
  if (to.params.id !== from.params.id) {
    await loadUser(to.params.id)
  }
})
</script>
```

## 解决方案 3: 使用 Key 强制组件重新创建

```vue
<!-- App.vue 或父组件 -->
<template>
  <router-view :key="$route.fullPath" />
</template>
```

**权衡:**
- 简单但性能较低
- 在每次参数更改时销毁并重新创建组件
- 丢失组件状态
- 仅在组件状态应完全重置时使用

## 解决方案 4: 用于路由响应式数据的 Composable

```javascript
// composables/useRouteData.js
import { ref, watch } from 'vue'
import { useRoute } from 'vue-router'

export function useRouteData(paramName, fetcher) {
  const route = useRoute()
  const data = ref(null)
  const loading = ref(false)
  const error = ref(null)

  watch(
    () => route.params[paramName],
    async (id) => {
      if (!id) return

      loading.value = true
      error.value = null

      try {
        data.value = await fetcher(id)
      } catch (e) {
        error.value = e
      } finally {
        loading.value = false
      }
    },
    { immediate: true }
  )

  return { data, loading, error }
}
```

```vue
<!-- 在组件中使用 -->
<script setup>
import { useRouteData } from '@/composables/useRouteData'
import { fetchUser } from '@/api/users'

const { data: user, loading, error } = useRouteData('id', fetchUser)
</script>
```

## 什么触发 vs 什么不触发

| 导航类型 | 生命周期钩子 | beforeRouteUpdate | 监听参数 |
|----------------|-----------------|-------------------|-----------------|
| `/users/1` 到 `/posts/1` | 是 | 否 | 是 |
| `/users/1` 到 `/users/2` | 否 | 是 | 是 |
| `/users/1?tab=a` 到 `/users/1?tab=b` | 否 | 是 | 否(不同的监听器) |
| `/users/1` 到 `/users/1` (相同) | 否 | 否 | 否 |

## 关键点

1. **相同路由,不同参数 = 相同组件实例** - 这是一个性能优化
2. **生命周期钩子仅触发一次** - 当组件首次挂载时
3. **使用 `watch` 并设置 `immediate: true`** - 涵盖初始加载和更新
4. **`onBeforeRouteUpdate` 是导航感知的** - 适合必须在视图更新前加载的数据
5. **`:key="route.fullPath"` 是一把大锤** - 仅在必要时使用

## 参考
- [Vue Router 动态路由匹配](https://router.vuejs.org/guide/essentials/dynamic-matching.html#reacting-to-params-changes)
- [Vue School: 响应参数更改](https://vueschool.io/lessons/reacting-to-param-changes)
