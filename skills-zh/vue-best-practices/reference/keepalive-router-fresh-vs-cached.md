---
title: KeepAlive 路由导航新鲜与缓存问题
impact: MEDIUM
impactDescription: 将 KeepAlive 与 Vue Router 一起使用时，用户可能在他们期望新鲜内容时获得缓存（陈旧）页面
type: gotcha
tags: [vue3, keepalive, vue-router, navigation, cache, ux]
---

# KeepAlive 路由导航新鲜与缓存问题

**影响: 中等** - 将 KeepAlive 与 Vue Router 一起使用时，从菜单或面包屑导航可能会显示缓存（陈旧）内容，而用户期望的是新鲜页面。这会造成令人困惑的用户体验，页面似乎"卡"在旧数据上。

## 任务清单

- [ ] 定义何时使用缓存与新鲜页面的明确规则
- [ ] 策略性地使用路由 key 控制新鲜度
- [ ] 实现 `onActivated` 刷新陈旧数据
- [ ] 决定缓存行为时考虑导航来源

## 问题

```vue
<!-- App.vue -->
<template>
  <nav>
    <router-link to="/products">产品</router-link>
  </nav>

  <router-view v-slot="{ Component }">
    <KeepAlive>
      <component :is="Component" />
    </KeepAlive>
  </router-view>
</template>
```

**场景：**
1. 用户访问 `/products?category=shoes` - 看到鞋子
2. 用户导航到 `/products?category=hats` - 看到帽子
3. 用户点击"产品"导航链接（到 `/products`）
4. **预期：** 新鲜的产品页面或默认类别
5. **实际：** 仍然显示帽子（缓存状态）！

用户点击导航期望"重新开始"但得到缓存状态。

## 解决方案

### 解决方案 1: 使用路由完整路径作为 Key

```vue
<template>
  <router-view v-slot="{ Component, route }">
    <KeepAlive>
      <!-- 不同的查询参数 = 不同的缓存条目 -->
      <component :is="Component" :key="route.fullPath" />
    </KeepAlive>
  </router-view>
</template>
```

**权衡：** 为每个唯一 URL 创建单独的缓存条目。可能会增加内存使用。

### 解决方案 2: 激活时刷新数据

```vue
<!-- Products.vue -->
<script setup>
import { ref, onActivated, watch } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const products = ref([])
const lastParams = ref(null)

async function fetchProducts() {
  const params = route.query
  products.value = await api.getProducts(params)
  lastParams.value = JSON.stringify(params)
}

// 初始获取
fetchProducts()

// 重新激活时如果参数更改则刷新
onActivated(() => {
  const currentParams = JSON.stringify(route.query)
  if (currentParams !== lastParams.value) {
    fetchProducts()
  }
})

// 组件处于活动状态时也监视更改
watch(() => route.query, fetchProducts, { deep: true })
</script>
```

### 解决方案 3: 导航感知缓存控制

根据用户导航方式的不同行为：

```vue
<script setup>
import { ref, onActivated } from 'vue'
import { useRoute, useRouter, onBeforeRouteUpdate } from 'vue-router'

const route = useRoute()
const router = useRouter()
const shouldRefresh = ref(false)

// 从导航链接（没有查询参数）导航时标记为刷新
onBeforeRouteUpdate((to, from) => {
  // 如果导航到没有参数的基本路径，用户想要新视图
  if (Object.keys(to.query).length === 0 && Object.keys(from.query).length > 0) {
    shouldRefresh.value = true
  }
})

onActivated(() => {
  if (shouldRefresh.value) {
    resetToDefaultState()
    shouldRefresh.value = false
  }
})

function resetToDefaultState() {
  // 重置过滤器、清除搜索、显示默认视图
}
</script>
```

### 解决方案 4: 不缓存依赖路由的页面

```vue
<script setup>
// 不缓存查询参数显著更改内容的页面
const noCacheRoutes = ['ProductSearch', 'SearchResults', 'FilteredList']
</script>

<template>
  <router-view v-slot="{ Component, route }">
    <KeepAlive :exclude="noCacheRoutes">
      <component :is="Component" />
    </KeepAlive>
  </router-view>
</template>
```

### 解决方案 5: 使用路由元信息进行新鲜导航

```javascript
// router.js
const routes = [
  {
    path: '/products',
    component: Products,
    meta: {
      keepAlive: true,
      refreshOnDirectNavigation: true
    }
  }
]
```

```vue
<!-- App.vue -->
<script setup>
import { watch } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const forceRefreshKey = ref(0)

// 监视想要新状态的路由导航
watch(
  () => route.fullPath,
  (newPath, oldPath) => {
    // 直接导航到基本路径 = 用户想要新的
    if (route.meta.refreshOnDirectNavigation && !route.query.length) {
      forceRefreshKey.value++
    }
  }
)
</script>

<template>
  <router-view v-slot="{ Component }">
    <KeepAlive>
      <component
        :is="Component"
        :key="`${route.name}-${forceRefreshKey}`"
      />
    </KeepAlive>
  </router-view>
</template>
```

## 最佳实践：明确缓存行为

记录您的缓存规则：

```javascript
// cacheRules.js
export const CACHE_RULES = {
  // 始终缓存 - 静态内容、用户偏好
  ALWAYS: ['Dashboard', 'Settings', 'Profile'],

  // 从不缓存 - 动态搜索/过滤结果
  NEVER: ['SearchResults', 'FilteredProducts'],

  // 缓存但在激活时刷新
  STALE_WHILE_REVALIDATE: ['Notifications', 'Messages']
}
```

## 关键点

1. **用户期望不匹配** - 导航链接通常暗示"新鲜"但得到缓存
2. **谨慎使用 `fullPath` key** - 防止重用但增加内存
3. **实现 `onActivated` 刷新** - 检查数据是否需要更新
4. **不缓存过滤器/搜索页面** - 这些高度依赖于查询
5. **记录缓存行为** - 为您的团队明确规则

## 参考
- [Vue.js KeepAlive 文档](https://vuejs.org/guide/built-ins/keep-alive.html)
- [Vue Router 导航](https://router.vuejs.org/guide/essentials/navigation.html)
- [Stack Keep-Alive 库](https://github.com/Zippowxk/stack-keep-alive)
