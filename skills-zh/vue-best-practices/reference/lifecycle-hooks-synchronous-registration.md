---
title: 在设置期间同步注册生命周期钩子
impact: HIGH
impactDescription: 异步注册的生命周期钩子永远不会执行
type: capability
tags: [vue3, composition-api, lifecycle, onMounted, onUnmounted, async, setup]
---

# 在设置期间同步注册生命周期钩子

**影响：高** - 异步注册的生命周期钩子（例如，在 setTimeout 内、await 之后）永远不会被调用，因为 Vue 无法将它们与组件实例关联。这导致静默失败，其中预期的初始化或清理代码永远不会运行。

在 Vue 3 的组合式 API 中，像 `onMounted`、`onUnmounted`、`onUpdated` 等生命周期钩子必须在组件设置期间同步注册。钩子注册不需要在 `setup()` 或 `<script setup>` 中按词法嵌套，但调用堆栈必须是同步的并且源自设置内部。

## 任务清单

- [ ] 在 setup() 或 `<script setup>` 的顶层注册所有生命周期钩子
- [ ] 永远不要在 setTimeout、setInterval 或 Promise 回调中注册钩子
- [ ] 当调用使用生命周期钩子的 composables 时，同步调用它们
- [ ] 钩子可以在外部函数中，如果从 setup 同步调用

**错误：**
```javascript
// 错误：钩子异步注册 - 永远不会执行
import { onMounted } from 'vue'

export default {
  async setup() {
    // 在 await 之后，我们在不同的调用堆栈中
    const data = await fetchInitialData()

    // 此钩子不会被注册！
    onMounted(() => {
      console.log('这永远不会运行')
    })
  }
}
```

```javascript
// 错误：钩子在 setTimeout 中注册 - 永远不会执行
import { onMounted } from 'vue'

export default {
  setup() {
    setTimeout(() => {
      // 这是异步的 - 钩子不会被注册！
      onMounted(() => {
        initializeChart()
      })
    }, 100)
  }
}
```

```javascript
// 错误：钩子在 Promise 回调中注册
import { onMounted } from 'vue'

export default {
  setup() {
    fetchConfig().then(() => {
      // 异步！这将静默失败
      onMounted(() => {
        applyConfig()
      })
    })
  }
}
```

**正确：**
```javascript
// 正确：钩子在顶层同步注册
import { onMounted, ref } from 'vue'

export default {
  setup() {
    const data = ref(null)

    // 首先同步注册钩子
    onMounted(async () => {
      // 钩子内部的异步操作没问题
      data.value = await fetchInitialData()
      initializeChart()
    })

    return { data }
  }
}
```

```vue
<!-- 正确：<script setup> - 钩子在顶层 -->
<script setup>
import { onMounted, onUnmounted, ref } from 'vue'

const isReady = ref(false)

// 这些在脚本设置执行期间是同步的
onMounted(() => {
  isReady.value = true
})

onUnmounted(() => {
  cleanup()
})
</script>
```

```javascript
// 正确：钩子在从 setup 同步调用的外部函数中
import { onMounted, onUnmounted } from 'vue'

function useWindowResize(callback) {
  // 这没问题 - 它从 setup 同步调用
  onMounted(() => {
    window.addEventListener('resize', callback)
  })

  onUnmounted(() => {
    window.removeEventListener('resize', callback)
  })
}

export default {
  setup() {
    // Composable 同步调用 - 钩子将被注册
    useWindowResize(handleResize)
  }
}
```

## 允许多个钩子

```javascript
// 正确：你可以多次注册同一个钩子
import { onMounted } from 'vue'

export default {
  setup() {
    // 两者都将运行，按注册顺序
    onMounted(() => {
      initializeA()
    })

    onMounted(() => {
      initializeB()
    })
  }
}
```

## 参考
- [Vue.js 生命周期钩子](https://vuejs.org/guide/essentials/lifecycle.html)
- [组合式 API 生命周期钩子](https://vuejs.org/api/composition-api-lifecycle.html)
