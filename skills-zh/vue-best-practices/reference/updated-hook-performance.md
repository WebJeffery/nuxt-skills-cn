---
title: 避免在 Updated Hook 中执行昂贵操作
impact: MEDIUM
impactDescription: 在 updated hook 中执行繁重的计算会导致性能瓶颈和潜在的无限循环
type: capability
tags: [vue3, vue2, lifecycle, updated, performance, optimization, reactivity]
---

# 避免在 Updated Hook 中执行昂贵操作

**影响：中** - `updated` hook 在每次导致重新渲染的响应式状态更改后运行。在此处放置昂贵操作、API 调用或状态变异会导致严重的性能下降、无限循环，以及低于最佳 60fps 阈值的掉帧。

谨慎使用 `updated`/`onUpdated`，仅用于无法通过监视器或计算属性处理的 DOM 更新后操作。对于大多数响应式数据处理，首选监视器（`watch`/`watchEffect`），它们可以更好地控制什么触发回调。

## 任务清单

- [ ] 永远不要在 updated hook 中执行 API 调用
- [ ] 永远不要在 updated 中变异响应式状态（会导致无限循环）
- [ ] 使用条件检查在操作之前验证更新是否相关
- [ ] 首选 `watch` 或 `watchEffect` 来响应特定的数据更改
- [ ] 如果 updated 操作昂贵，请使用节流/防抖
- [ ] 将 updated 保留用于低级 DOM 同步任务

**错误：**
```javascript
// 错误：在 updated 中进行 API 调用 - 在每次重新渲染时触发
export default {
  data() {
    return { items: [], lastUpdate: null }
  },
  updated() {
    // 这在每次单个状态更改后运行！
    fetch('/api/sync', {
      method: 'POST',
      body: JSON.stringify(this.items)
    })
  }
}
```

```javascript
// 错误：在 updated 中变异状态 - 无限循环
export default {
  data() {
    return { renderCount: 0 }
  },
  updated() {
    // 这会导致另一次更新，再次触发 updated！
    this.renderCount++ // 无限循环！
  }
}
```

```javascript
// 错误：在每次更新时进行繁重计算
export default {
  updated() {
    // 昂贵的操作在每次击键、每次状态更改时运行
    this.processedData = this.heavyComputation(this.rawData)
    this.analytics = this.calculateMetrics(this.allData)
  }
}
```

**正确：**
```javascript
// 正确：使用监视器进行特定的数据更改
export default {
  data() {
    return { items: [] }
  },
  watch: {
    // 仅在 items 实际更改时触发
    items: {
      handler(newItems) {
        this.syncToServer(newItems)
      },
      deep: true
    }
  },
  methods: {
    syncToServer: debounce(function(items) {
      fetch('/api/sync', {
        method: 'POST',
        body: JSON.stringify(items)
      })
    }, 500)
  }
}
```

```vue
<!-- 正确：Composition API 与定向监视器 -->
<script setup>
import { ref, watch, onUpdated } from 'vue'
import { useDebounceFn } from '@vueuse/core'

const items = ref([])
const scrollContainer = ref(null)

// 监视特定数据 - 不是所有更新
watch(items, (newItems) => {
  syncToServer(newItems)
}, { deep: true })

const syncToServer = useDebounceFn((items) => {
  fetch('/api/sync', { method: 'POST', body: JSON.stringify(items) })
}, 500)

// 仅将 onUpdated 用于 DOM 同步
onUpdated(() => {
  // 仅在内容更改高度时滚动到底部
  if (scrollContainer.value) {
    scrollContainer.value.scrollTop = scrollContainer.value.scrollHeight
  }
})
</script>
```

```javascript
// 正确：在 updated hook 中进行条件检查
export default {
  data() {
    return {
      content: '',
      lastSyncedContent: ''
    }
  },
  updated() {
    // 仅在满足特定条件时操作
    if (this.content !== this.lastSyncedContent) {
      this.syncContent()
      this.lastSyncedContent = this.content
    }
  },
  methods: {
    syncContent: debounce(function() {
      // 同步逻辑
    }, 300)
  }
}
```

## Updated Hook 的有效用例

```javascript
// 正确：低级 DOM 同步
export default {
  updated() {
    // 将第三方库与 Vue 的 DOM 同步
    this.thirdPartyWidget.refresh()

    // 内容更改后更新滚动位置
    this.$nextTick(() => {
      this.maintainScrollPosition()
    })
  }
}
```

## 对派生数据首选计算属性

```javascript
// 错误：在 updated 中计算派生数据
export default {
  data() {
    return { numbers: [1, 2, 3, 4, 5] }
  },
  updated() {
    this.sum = this.numbers.reduce((a, b) => a + b, 0) // 导致另一次更新！
  }
}

// 正确：改用计算属性
export default {
  data() {
    return { numbers: [1, 2, 3, 4, 5] }
  },
  computed: {
    sum() {
      return this.numbers.reduce((a, b) => a + b, 0)
    }
  }
}
```

## 参考
- [Vue.js 生命周期 Hook](https://vuejs.org/guide/essentials/lifecycle.html)
- [Vue.js 监听器](https://vuejs.org/guide/essentials/watchers.html)
- [Vue.js 计算属性](https://vuejs.org/guide/essentials/computed.html)
