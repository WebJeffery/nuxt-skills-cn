---
title: 快速替换期间过渡中的卸载钩子可能不会触发
impact: MEDIUM
impactDescription: 竞态条件下，过渡中的组件可能在没有触发卸载钩子的情况下被销毁
type: gotcha
tags: [vue3, lifecycle, transition, onUnmounted, unmounted, cleanup, race-condition]
---

# 快速替换期间过渡中的卸载钩子可能不会触发

**影响：中等** - 当 `<transition>` 内的组件在过渡的加载阶段被另一个组件替换时，即使组件已从 DOM 中移除，卸载钩子（`onBeforeUnmount`、`onUnmounted`）也可能不会被调用。这可能导致副作用未清理而引起的内存泄漏和资源泄漏。

这是一个已知的边缘情况，发生在特定时机时——如果带有过渡内子组件的父组件在子组件仍在挂载时被替换。子组件的挂载钩子会触发，但卸载钩子永远不会触发。

## 任务清单

- [ ] 注意过渡中的卸载钩子不是 100% 保证的
- [ ] 对于关键清理，考虑替代清理策略
- [ ] 在过渡上使用 `mode="out-in"` 以确保旧组件在新组件挂载之前完全卸载
- [ ] 对于重要资源，考虑在父组件级别进行清理
- [ ] 在开发期间测试组件替换场景

**有问题的场景：**
```vue
<!-- 带有过渡中懒加载子组件的父组件 -->
<template>
  <transition>
    <Suspense>
      <component :is="currentComponent" />
    </Suspense>
  </transition>
</template>
```

```javascript
// 子组件 - 如果父组件在挂载阶段快速更改，卸载钩子可能不会触发
export default {
  setup() {
    const socket = new WebSocket('wss://example.com')

    onMounted(() => {
      console.log('已挂载 - 这将运行')
      socket.connect()
    })

    onUnmounted(() => {
      // 警告：如果组件在过渡中且父组件在挂载阶段导航离开，这可能不会运行！
      console.log('已卸载 - 可能不会运行')
      socket.close()
    })
  }
}
```

**更安全的模式：**
```vue
<!-- 更安全：使用 out-in 模式确保正确的排序 -->
<template>
  <transition mode="out-in">
    <component :is="currentComponent" :key="currentKey" />
  </transition>
</template>
```

```javascript
// 更安全：在父组件级别清理关键资源
// 父组件
export default {
  setup() {
    const childSocket = ref(null)

    // 父组件控制资源生命周期
    provide('registerSocket', (socket) => {
      childSocket.value = socket
    })

    onUnmounted(() => {
      // 父组件确保清理，即使子组件卸载钩子不触发
      childSocket.value?.close()
    })
  }
}

// 子组件
export default {
  setup() {
    const registerSocket = inject('registerSocket')
    const socket = new WebSocket('wss://example.com')

    // 向父组件注册以进行备份清理
    registerSocket(socket)

    onMounted(() => {
      socket.connect()
    })

    onUnmounted(() => {
      socket.close() // 仍然在此处尝试清理
    })
  }
}
```

```javascript
// 更安全：使用 AbortController 模式进行可取消操作
export default {
  setup() {
    const abortController = new AbortController()

    onMounted(() => {
      fetch('/api/data', { signal: abortController.signal })
        .then(handleData)
        .catch(err => {
          if (err.name !== 'AbortError') {
            handleError(err)
          }
        })
    })

    onUnmounted(() => {
      // 如果这不触发，请求继续但响应被忽略
      // 不是内存泄漏 - 只是可能浪费的网络调用
      abortController.abort()
    })
  }
}
```

## 测试此问题

```javascript
// 通过在异步加载期间快速切换组件来测试
async function testUnmountHooks() {
  // 挂载组件 A（具有异步设置）
  await mountComponent('A')

  // 在 A 完成挂载之前立即切换到 B
  await mountComponent('B')

  // 检查 A 的卸载钩子是否触发
  // 它们可能没有触发！
}
```

## 参考
- [Vue.js GitHub Issue #6260](https://github.com/vuejs/core/issues/6260)
- [Vue.js Transition](https://vuejs.org/guide/built-ins/transition.html)
- [Vue.js Lifecycle Hooks](https://vuejs.org/guide/essentials/lifecycle.html)
