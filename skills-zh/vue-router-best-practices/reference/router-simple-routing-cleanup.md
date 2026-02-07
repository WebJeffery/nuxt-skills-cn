---
title: 简单的哈希路由需要事件监听器清理
impact: MEDIUM
impactDescription: 在不使用 Vue Router 的情况下实现基本路由时，忘记移除 hashchange 监听器会导致内存泄漏和多个处理程序执行
type: gotcha
tags: [vue3, routing, events, memory-leak, cleanup]
---

# 简单的哈希路由需要事件监听器清理

**影响：中等** - 在不使用 Vue Router 的情况下实现基本客户端路由（使用基于哈希的路由和 `hashchange` 事件）时，必须在组件卸载时清理事件监听器。否则会导致内存泄漏，并可能导致组件重新创建后多个处理程序触发。

## 任务清单

- [ ] 存储事件监听器引用以进行清理
- [ ] 使用 onUnmounted 移除事件监听器
- [ ] 考虑为生产应用使用 Vue Router
- [ ] 测试组件挂载/卸载周期

## 问题所在

```vue
<script setup>
import { ref, computed } from 'vue'
import Home from './Home.vue'
import About from './About.vue'

const routes = {
  '/': Home,
  '/about': About
}

const currentPath = ref(window.location.hash)

// 错误：事件监听器从未被移除！
// 每次此组件挂载时，都会添加一个新的监听器
// 挂载 5 次后，你有 5 个监听器在运行
window.addEventListener('hashchange', () => {
  currentPath.value = window.location.hash
})

const currentView = computed(() => {
  return routes[currentPath.value.slice(1) || '/']
})
</script>
```

**会发生什么：**
1. 组件挂载，添加监听器
2. 组件卸载（例如，路由更改，v-if 切换）
3. 组件再次挂载，添加另一个监听器
4. 现在两个监听器响应每个哈希更改
5. 最终导致性能问题和内存泄漏

## 解决方案：使用 onUnmounted 进行适当清理

```vue
<script setup>
import { ref, computed, onUnmounted } from 'vue'
import Home from './Home.vue'
import About from './About.vue'
import NotFound from './NotFound.vue'

const routes = {
  '/': Home,
  '/about': About
}

const currentPath = ref(window.location.hash)

// 存储处理程序引用以进行清理
function handleHashChange() {
  currentPath.value = window.location.hash
}

// 添加监听器
window.addEventListener('hashchange', handleHashChange)

// 关键：在卸载时移除监听器
onUnmounted(() => {
  window.removeEventListener('hashchange', handleHashChange)
})

const currentView = computed(() => {
  return routes[currentPath.value.slice(1) || '/'] || NotFound
})
</script>
```

## 解决方案：使用 Options API

```vue
<script>
import Home from './Home.vue'
import About from './About.vue'
import NotFound from './NotFound.vue'

const routes = {
  '/': Home,
  '/about': About
}

export default {
  data() {
    return {
      currentPath: window.location.hash
    }
  },

  computed: {
    currentView() {
      return routes[this.currentPath.slice(1) || '/'] || NotFound
    }
  },

  mounted() {
    // 存储绑定处理程序以进行清理
    this.hashHandler = () => {
      this.currentPath = window.location.hash
    }
    window.addEventListener('hashchange', this.hashHandler)
  },

  beforeUnmount() {
    // 清理
    window.removeEventListener('hashchange', this.hashHandler)
  }
}
</script>
```

## 解决方案：用于可重用哈希路由的可组合函数

```javascript
// composables/useHashRouter.js
import { ref, computed, onUnmounted } from 'vue'

export function useHashRouter(routes, notFoundComponent = null) {
  const currentPath = ref(window.location.hash)

  function handleHashChange() {
    currentPath.value = window.location.hash
  }

  // 设置
  window.addEventListener('hashchange', handleHashChange)

  // 清理 - 组件卸载时自动处理
  onUnmounted(() => {
    window.removeEventListener('hashchange', handleHashChange)
  })

  const currentView = computed(() => {
    const path = currentPath.value.slice(1) || '/'
    return routes[path] || notFoundComponent
  })

  function navigate(path) {
    window.location.hash = path
  }

  return {
    currentPath,
    currentView,
    navigate
  }
}
```

```vue
<!-- 使用 -->
<script setup>
import { useHashRouter } from '@/composables/useHashRouter'
import Home from './Home.vue'
import About from './About.vue'
import NotFound from './NotFound.vue'

const { currentView } = useHashRouter({
  '/': Home,
  '/about': About
}, NotFound)
</script>

<template>
  <component :is="currentView" />
</template>
```

## 何时使用简单路由 vs Vue Router

| 使用简单哈希路由 | 使用 Vue Router |
|------------------------|----------------|
| 学习/原型设计 | 生产应用 |
| 非常简单的应用（2-3 页） | 需要嵌套路由 |
| 无构建步骤可用 | 需要导航守卫 |
| 包大小关键 | 需要懒加载 |
| 仅静态托管 | 历史模式（干净的 URL） |

## 关键要点

1. **始终清理事件监听器** - 使用 onUnmounted 或 beforeUnmount
2. **存储处理程序引用** - 匿名函数无法被移除
3. **为真实应用考虑 Vue Router** - 它自动处理清理
4. **测试卸载场景** - v-if 切换，热模块替换
5. **可组合函数有助于封装清理逻辑** - 可重用且自动

## 参考
- [Vue.js 路由文档](https://vuejs.org/guide/scaling-up/routing.html)
- [Vue Router 官方库](https://router.vuejs.org/)
