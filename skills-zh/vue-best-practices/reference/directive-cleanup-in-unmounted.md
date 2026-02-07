---
title: 在指令的 unmounted 钩子中清理副作用
impact: HIGH
impactDescription: 未能清理指令中的 intervals、事件监听器和订阅会导致内存泄漏
type: gotcha
tags: [vue3, directives, memory-leak, cleanup, unmounted, event-listeners]
---

# 在指令的 unmounted 钩子中清理副作用

**影响: 高** - 创建自定义指令时的一个常见且严重的错误是忘记在 `unmounted` 钩子中清理 intervals、事件监听器和其他副作用。这会导致内存泄漏和幽灵处理程序，在元素从 DOM 中移除后继续运行。

避免此类错误的关键始终是实现 `unmounted` 钩子以清理在 `mounted` 或其他生命周期钩子中创建的任何资源。

## 任务清单

- [ ] 始终在 `mounted` 中创建资源时在 `unmounted` 中配对清理
- [ ] 存储 intervals、timeouts 和监听器的引用以便稍后清理
- [ ] 使用 `el.dataset` 或 WeakMap 在指令钩子之间共享数据
- [ ] 测试指令在元素被移除时（v-if 切换）正确清理

**不正确:**
```javascript
// 错误: 没有清理 - 内存泄漏！
const vPoll = {
  mounted(el, binding) {
    // 此 interval 永远运行，即使在元素被移除后！
    setInterval(() => {
      console.log('polling...')
      binding.value?.()
    }, 1000)
  }
}

// 错误: unmount 后事件监听器仍然存在
const vClickOutside = {
  mounted(el, binding) {
    document.addEventListener('click', (e) => {
      if (!el.contains(e.target)) {
        binding.value()
      }
    })
    // 没有清理 - 监听器仍然附加到 document！
  }
}
```

**正确:**
```javascript
// 正确: 存储引用并清理
const vPoll = {
  mounted(el, binding) {
    // 将 interval ID 存储在元素上以便稍后清理
    el._pollInterval = setInterval(() => {
      console.log('polling...')
      binding.value?.()
    }, 1000)
  },
  unmounted(el) {
    // 清理 interval
    if (el._pollInterval) {
      clearInterval(el._pollInterval)
      delete el._pollInterval
    }
  }
}

// 正确: 使用命名函数进行正确移除
const vClickOutside = {
  mounted(el, binding) {
    el._clickOutsideHandler = (e) => {
      if (!el.contains(e.target)) {
        binding.value()
      }
    }
    document.addEventListener('click', el._clickOutsideHandler)
  },
  unmounted(el) {
    if (el._clickOutsideHandler) {
      document.removeEventListener('click', el._clickOutsideHandler)
      delete el._clickOutsideHandler
    }
  }
}
```

## 使用 WeakMap 进行更清晰的状态管理

```javascript
// 最佳: 使用 WeakMap 避免污染元素属性
const pollIntervals = new WeakMap()
const clickHandlers = new WeakMap()

const vPoll = {
  mounted(el, binding) {
    const intervalId = setInterval(() => {
      binding.value?.()
    }, binding.arg || 1000)
    pollIntervals.set(el, intervalId)
  },
  unmounted(el) {
    const intervalId = pollIntervals.get(el)
    if (intervalId) {
      clearInterval(intervalId)
      pollIntervals.delete(el)
    }
  }
}

const vClickOutside = {
  mounted(el, binding) {
    const handler = (e) => {
      if (!el.contains(e.target)) {
        binding.value()
      }
    }
    clickHandlers.set(el, handler)
    document.addEventListener('click', handler)
  },
  unmounted(el) {
    const handler = clickHandlers.get(el)
    if (handler) {
      document.removeEventListener('click', handler)
      clickHandlers.delete(el)
    }
  }
}
```

## 带有多个资源的完整示例

```javascript
const vAutoScroll = {
  mounted(el, binding) {
    const state = {
      intervalId: null,
      resizeObserver: null,
      scrollHandler: null
    }

    // 设置轮询
    state.intervalId = setInterval(() => {
      el.scrollTop = el.scrollHeight
    }, binding.value?.interval || 100)

    // 设置 resize 观察器
    state.resizeObserver = new ResizeObserver(() => {
      el.scrollTop = el.scrollHeight
    })
    state.resizeObserver.observe(el)

    // 设置滚动监听器
    state.scrollHandler = () => {
      // 跟踪用户滚动
    }
    el.addEventListener('scroll', state.scrollHandler)

    // 存储所有状态以便清理
    el._autoScrollState = state
  },

  unmounted(el) {
    const state = el._autoScrollState
    if (!state) return

    // 清理所有内容
    if (state.intervalId) {
      clearInterval(state.intervalId)
    }
    if (state.resizeObserver) {
      state.resizeObserver.disconnect()
    }
    if (state.scrollHandler) {
      el.removeEventListener('scroll', state.scrollHandler)
    }

    delete el._autoScrollState
  }
}
```

## 测试指令清理

```javascript
// 测试清理是否正常工作
import { mount } from '@vue/test-utils'
import { ref, nextTick } from 'vue'

const vTrackInterval = {
  mounted(el) {
    el._interval = setInterval(() => {}, 100)
    window.__activeIntervals = (window.__activeIntervals || 0) + 1
  },
  unmounted(el) {
    clearInterval(el._interval)
    window.__activeIntervals--
  }
}

test('指令在 unmount 时清理 interval', async () => {
  const show = ref(true)
  const wrapper = mount({
    template: `<div v-if="show" v-track-interval></div>`,
    directives: { 'track-interval': vTrackInterval },
    setup: () => ({ show })
  })

  expect(window.__activeIntervals).toBe(1)

  show.value = false
  await nextTick()

  expect(window.__activeIntervals).toBe(0)
})
```

## 参考
- [Vue.js 自定义指令 - 指令钩子](https://vuejs.org/guide/reusability/custom-directives#directive-hooks)
- [Vue School - 指令的 unmounted 钩子](https://vueschool.io/lessons/vue-3-the-directive-s-unmounted-hook)
