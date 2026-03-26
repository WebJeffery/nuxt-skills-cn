---
title: 使用 flushPromises 测试异步组件
impact: HIGH
impactDescription: 不等待异步操作会导致测试在组件渲染之前进行断言,导致假阴性
type: gotcha
tags: [vue3, testing, async, defineAsyncComponent, flushPromises, vitest]
---

# 使用 flushPromises 测试异步组件

**影响: HIGH** - 测试使用 `defineAsyncComponent` 创建的异步组件时,必须使用 `await flushPromises()` 确保组件在断言之前已加载。Vue 异步更新,因此不考虑这一点的测试将在组件渲染之前进行断言。

## 任务清单

- [ ] 在测试函数中使用 `async/await` 测试异步组件
- [ ] 在挂载异步组件后调用 `await flushPromises()`
- [ ] 通过在 `flushPromises()` 之前进行断言来测试加载状态
- [ ] 在 `defineAsyncComponent` 中使用被拒绝的 Promise 测试错误状态
- [ ] 使用 `trigger()` 时带上 `await`,因为它返回一个 Promise

**错误:**

```javascript
import { mount } from '@vue/test-utils'
import { defineAsyncComponent } from 'vue'

const AsyncWidget = defineAsyncComponent(() =>
  import('./Widget.vue')
)

test('渲染异步组件', () => {
  const wrapper = mount(AsyncWidget)

  // 失败: 组件尚未加载
  expect(wrapper.text()).toContain('Widget Content')
})
```

**正确:**

```javascript
import { mount, flushPromises } from '@vue/test-utils'
import { defineAsyncComponent, nextTick } from 'vue'

const AsyncWidget = defineAsyncComponent(() =>
  import('./Widget.vue')
)

test('渲染异步组件', async () => {
  const wrapper = mount(AsyncWidget)

  // 等待异步组件加载
  await flushPromises()

  expect(wrapper.text()).toContain('Widget Content')
})

test('初始显示加载状态', async () => {
  const AsyncWithLoading = defineAsyncComponent({
    loader: () => import('./Widget.vue'),
    loadingComponent: { template: '<div>Loading...</div>' },
    delay: 0
  })

  const wrapper = mount(AsyncWithLoading)

  // 立即检查加载状态
  expect(wrapper.text()).toContain('Loading...')

  // 等待组件加载
  await flushPromises()

  // 检查最终状态
  expect(wrapper.text()).toContain('Widget Content')
})
```

## 使用 Suspense 测试

```javascript
import { mount, flushPromises } from '@vue/test-utils'
import { Suspense, defineAsyncComponent, h } from 'vue'

const AsyncWidget = defineAsyncComponent(() =>
  import('./Widget.vue')
)

test('使用 Suspense 渲染异步组件', async () => {
  const wrapper = mount({
    components: { AsyncWidget },
    template: `
      <Suspense>
        <AsyncWidget />
        <template #fallback>
          <div>Loading...</div>
        </template>
      </Suspense>
    `
  })

  // 初始显示 fallback
  expect(wrapper.text()).toContain('Loading...')

  // 等待异步解析
  await flushPromises()

  // 现在显示实际内容
  expect(wrapper.text()).toContain('Widget Content')
})
```

## 测试错误状态

```javascript
import { mount, flushPromises } from '@vue/test-utils'
import { defineAsyncComponent } from 'vue'

test('加载失败时显示错误组件', async () => {
  const AsyncWithError = defineAsyncComponent({
    loader: () => Promise.reject(new Error('Failed to load')),
    errorComponent: { template: '<div>Error loading component</div>' }
  })

  const wrapper = mount(AsyncWithError)

  await flushPromises()

  expect(wrapper.text()).toContain('Error loading component')
})
```

## 工具参考

| 工具 | 用途 |
|---------|---------|
| `await flushPromises()` | 解析所有挂起的 Promise |
| `await nextTick()` | 等待 Vue 的下一个 DOM 更新周期 |
| `await wrapper.trigger('click')` | 触发事件并等待更新 |

## 动态导入处理

**注意:** 动态导入(`import('./File.vue')`)可能需要在测试环境中进行额外处理,而不仅仅是 `flushPromises()`。像 Vitest 这样的测试运行器与运行时打包器处理模块解析的方式不同,这可能导致动态导入出现时序问题。如果仅使用 `flushPromises()` 无法解析组件,请考虑:

- 模拟动态导入以同步返回组件
- 按顺序使用多个 `await flushPromises()` 调用
- 将断言包装在 `waitFor()` 或重试工具中
- 配置测试运行器的模块解析设置

```javascript
// 如果 flushPromises() 不够,模拟导入
vi.mock('./Widget.vue', () => ({
  default: { template: '<div>Widget Content</div>' }
}))

// 或对嵌套异步操作使用多个 flush 调用
await flushPromises()
await flushPromises()
```

## 参考
- [Vue Test Utils - 异步行为](https://test-utils.vuejs.org/guide/advanced/async-suspense)
- [Vue.js 异步组件文档](https://vuejs.org/guide/components/async)
