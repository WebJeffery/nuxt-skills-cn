---
title: Vue 3 KeepAlive 没有直接的缓存移除 API
impact: MEDIUM
impactDescription: 与 Vue 2 不同，Vue 3 中没有编程方式从 KeepAlive 缓存中移除特定组件
type: gotcha
tags: [vue3, keepalive, cache, migration, vue2-to-vue3]
---

# Vue 3 KeepAlive 没有直接的缓存移除 API

**影响: 中等** - Vue 3 移除了 Vue 2 开发人员用于间接清除 KeepAlive 缓存条目的 `$destroy()` 方法。Vue 3 中没有直接 API 来移除特定的缓存组件。

## 任务清单

- [ ] 不要依赖 Vue 2 模式的编程缓存移除
- [ ] 使用 `include`/`exclude` 属性进行动态缓存控制
- [ ] 使用 key 更改强制缓存失效
- [ ] 设置适当的 `max` 属性以自动驱逐旧条目

## 问题

### Vue 2 模式（不再有效）

```javascript
// Vue 2: 可以销毁特定组件实例
this.$children[0].$destroy()
```

### Vue 3: 没有等效 API

```javascript
// Vue 3: $destroy() 不存在
// 没有直接方法移除特定的缓存实例
```

## 解决方案

### 解决方案 1: 动态包含/排除

通过响应式属性控制缓存成员资格：

```vue
<script setup>
import { ref, computed } from 'vue'

const cachedViews = ref(['Dashboard', 'Settings', 'Profile'])

function removeFromCache(viewName) {
  cachedViews.value = cachedViews.value.filter(v => v !== viewName)
}

function addToCache(viewName) {
  if (!cachedViews.value.includes(viewName)) {
    cachedViews.value.push(viewName)
  }
}
</script>

<template>
  <KeepAlive :include="cachedViews">
    <component :is="currentView" />
  </KeepAlive>
</template>
```

当组件从 `include` 中移除时，它将在下次切换时被销毁。

### 解决方案 2: 基于 Key 的缓存失效

更改 key 以强制创建新实例：

```vue
<script setup>
import { ref, reactive } from 'vue'

const currentView = ref('Dashboard')
const viewKeys = reactive({
  Dashboard: 0,
  Settings: 0,
  Profile: 0
})

function invalidateCache(viewName) {
  viewKeys[viewName]++
}

function switchView(viewName) {
  currentView.value = viewName
}

// 下次访问时强制刷新 Dashboard
function refreshDashboard() {
  invalidateCache('Dashboard')
}
</script>

<template>
  <KeepAlive>
    <component
      :is="currentView"
      :key="`${currentView}-${viewKeys[currentView]}`"
    />
  </KeepAlive>
</template>
```

### 解决方案 3: 条件 KeepAlive

根据缓存需求包装或解包：

```vue
<script setup>
import { ref } from 'vue'

const currentView = ref('Dashboard')
const shouldCache = ref(true)

function clearCacheAndSwitch(viewName) {
  shouldCache.value = false
  currentView.value = viewName
  // 切换后重新启用缓存
  nextTick(() => {
    shouldCache.value = true
  })
}
</script>

<template>
  <KeepAlive v-if="shouldCache">
    <component :is="currentView" />
  </KeepAlive>
  <component v-else :is="currentView" />
</template>
```

### 解决方案 4: 使用 Max 进行自动驱逐

让 LRU 缓存处理清理：

```vue
<template>
  <!-- 缓存满时自动驱逐最近最少使用的 -->
  <KeepAlive :max="5">
    <component :is="currentView" />
  </KeepAlive>
</template>
```

## Vue Router: 在某些导航时清除缓存

```vue
<script setup>
import { ref, watch } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const cachedRoutes = ref(['Dashboard', 'Settings'])

// 从某些路径导航时从缓存中清除特定路由
watch(() => route.name, (newRoute, oldRoute) => {
  if (oldRoute === 'Login') {
    // 用户刚刚登录 - 清除并刷新 Dashboard
    cachedRoutes.value = cachedRoutes.value.filter(r => r !== 'Dashboard')
    nextTick(() => {
      cachedRoutes.value.push('Dashboard')
    })
  }
})
</script>

<template>
  <router-view v-slot="{ Component }">
    <KeepAlive :include="cachedRoutes">
      <component :is="Component" />
    </KeepAlive>
  </router-view>
</template>
```

## 关键点

1. **Vue 3 中没有 `$destroy()`** - 无法直接移除缓存实例
2. **使用动态 `include`** - 响应式控制哪些组件被缓存
3. **使用 key 更改** - 更改 key 创建新的缓存条目
4. **使用 `max` 属性** - LRU 驱逐自动处理清理
5. **提前规划缓存策略** - 围绕这些约束进行设计

## 参考
- [Vue.js KeepAlive 文档](https://vuejs.org/guide/built-ins/keep-alive.html)
- [Vue 3 迁移指南](https://v3-migration.vuejs.org/)
- [Vue RFC 讨论 #283: KeepAlive 的自定义缓存策略](https://github.com/vuejs/rfcs/discussions/283)
