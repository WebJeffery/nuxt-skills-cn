---
title: 使用主机组件包装器测试复杂的 Composables
impact: MEDIUM
impactDescription: 使用生命周期钩子或 provide/inject 的 composables 在没有组件上下文的情况下直接测试时会失败
type: capability
tags: [vue3, testing, composables, vitest, lifecycle-hooks, provide-inject]
---

# 使用主机组件包装器测试复杂的 Composables

**影响: MEDIUM** - 使用 Vue 生命周期钩子(`onMounted`、`onUnmounted`)或依赖注入(`inject`)的 composables 需要组件上下文才能工作。直接测试它们会导致错误或不正确的行为。

仅使用响应式 API 的简单 composables 可以直接测试。复杂的 composables 需要一个创建主机组件上下文的辅助函数。

## 任务清单

- [ ] 识别 composable 是否使用生命周期钩子或 inject
- [ ] 对于简单的 composables(仅 refs、computed): 直接测试
- [ ] 对于复杂的 composables: 使用 `withSetup` 辅助模式
- [ ] 在每个测试后通过卸载测试应用来清理
- [ ] 使用 `app.provide()` 模拟注入的依赖

**简单 Composable - 直接测试:**
```javascript
// composables/useCounter.js
import { ref, computed } from 'vue'

export function useCounter(initialValue = 0) {
  const count = ref(initialValue)
  const doubled = computed(() => count.value * 2)
  const increment = () => count.value++

  return { count, doubled, increment }
}
```

```javascript
// useCounter.test.js
import { describe, it, expect } from 'vitest'
import { useCounter } from './useCounter'

// 正确: 简单的 composable 可以直接测试
describe('useCounter', () => {
  it('使用默认值初始化', () => {
    const { count } = useCounter()
    expect(count.value).toBe(0)
  })

  it('递增计数', () => {
    const { count, increment } = useCounter()
    increment()
    expect(count.value).toBe(1)
  })

  it('计算双倍值', () => {
    const { count, doubled, increment } = useCounter(5)
    expect(doubled.value).toBe(10)
    increment()
    expect(doubled.value).toBe(12)
  })
})
```

**复杂 Composable - 使用主机包装器:**
```javascript
// composables/useFetch.js
import { ref, onMounted, onUnmounted, inject } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)
  const loading = ref(true)
  let controller = null

  // 使用 inject - 需要组件上下文
  const apiClient = inject('apiClient')

  // 使用生命周期钩子 - 需要组件上下文
  onMounted(async () => {
    controller = new AbortController()
    try {
      const response = await apiClient.get(url, { signal: controller.signal })
      data.value = response.data
    } catch (e) {
      if (e.name !== 'AbortError') error.value = e
    } finally {
      loading.value = false
    }
  })

  onUnmounted(() => {
    controller?.abort()
  })

  return { data, error, loading }
}
```

```javascript
// test-utils.js
import { createApp } from 'vue'

/**
 * 测试需要组件上下文的 composables 的辅助函数
 */
export function withSetup(composable) {
  let result

  const app = createApp({
    setup() {
      result = composable()
      // 返回渲染函数以抑制警告
      return () => {}
    }
  })

  app.mount(document.createElement('div'))

  return [result, app]
}
```

```javascript
// useFetch.test.js
import { describe, it, expect, vi, beforeEach, afterEach } from 'vitest'
import { flushPromises } from '@vue/test-utils'
import { withSetup } from './test-utils'
import { useFetch } from './useFetch'

describe('useFetch', () => {
  let app
  const mockApiClient = {
    get: vi.fn()
  }

  afterEach(() => {
    // 重要: 清理以触发 onUnmounted
    app?.unmount()
  })

  it('在挂载时获取数据', async () => {
    mockApiClient.get.mockResolvedValue({ data: { id: 1, name: 'Test' } })

    const [result, testApp] = withSetup(() => useFetch('/api/test'))
    app = testApp

    // 提供模拟的依赖
    app.provide('apiClient', mockApiClient)

    // 等待异步操作
    await flushPromises()

    expect(result.data.value).toEqual({ id: 1, name: 'Test' })
    expect(result.loading.value).toBe(false)
    expect(result.error.value).toBeNull()
  })

  it('处理错误', async () => {
    const testError = new Error('Network error')
    mockApiClient.get.mockRejectedValue(testError)

    const [result, testApp] = withSetup(() => useFetch('/api/test'))
    app = testApp
    app.provide('apiClient', mockApiClient)

    await flushPromises()

    expect(result.error.value).toBe(testError)
    expect(result.data.value).toBeNull()
  })
})
```

## 增强的 withSetup 辅助函数,支持 Provide
```javascript
// test-utils.js
export function withSetup(composable, options = {}) {
  let result

  const app = createApp({
    setup() {
      result = composable()
      return () => {}
    }
  })

  // 在挂载前应用全局 provides
  if (options.provide) {
    Object.entries(options.provide).forEach(([key, value]) => {
      app.provide(key, value)
    })
  }

  app.mount(document.createElement('div'))

  return [result, app]
}

// 使用
const [result, app] = withSetup(() => useMyComposable(), {
  provide: {
    apiClient: mockApiClient,
    currentUser: { id: 1, name: 'Test User' }
  }
})
```

## 使用 @vue/test-utils mount 测试
```javascript
import { mount } from '@vue/test-utils'
import { defineComponent } from 'vue'
import { useFetch } from './useFetch'

test('在组件上下文中的 useFetch', async () => {
  const TestComponent = defineComponent({
    setup() {
      const { data, loading } = useFetch('/api/users')
      return { data, loading }
    },
    template: '<div>{{ loading ? "Loading..." : data }}</div>'
  })

  const wrapper = mount(TestComponent, {
    global: {
      provide: {
        apiClient: mockApiClient
      }
    }
  })

  await flushPromises()
  expect(wrapper.text()).toContain('Test data')
})
```

## 参考
- [Vue.js 测试指南 - 测试 Composables](https://vuejs.org/guide/scaling-up/testing#testing-composables)
- [Vue Test Utils - 挂载组件](https://test-utils.vuejs.org/guide/)
