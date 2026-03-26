---
title: 在测试中使用 Suspense 包装异步 Setup 组件
impact: HIGH
impactDescription: 具有异步 setup() 的组件在没有 Suspense 包装器的情况下在测试中无法渲染,导致难以理解的错误
type: gotcha
tags: [vue3, testing, suspense, async-setup, vue-test-utils, vitest]
---

# 在测试中使用 Suspense 包装异步 Setup 组件

**影响: HIGH** - 使用 `async setup()` 的组件需要一个 `<Suspense>` 包装器才能正常工作。在没有 Suspense 的情况下测试它们会导致组件永远不会渲染,从而导致测试失败和令人困惑的错误。

创建一个带有 Suspense 的测试包装器组件,或使用 `mountSuspense` 辅助函数来测试异步组件。

## 任务清单

- [ ] 识别具有异步 setup 的组件(在 `<script setup>` 或 `async setup()` 中使用 `await`)
- [ ] 创建一个带有 `<Suspense>` 的包装器组件用于测试
- [ ] 在挂载后使用 `flushPromises()` 等待异步解析
- [ ] 通过 `findComponent()` 访问实际组件进行断言
- [ ] 谨慎使用 `@testing-library/vue`(有 Suspense 问题)

**错误:**
```javascript
import { mount } from '@vue/test-utils'
import AsyncUserProfile from './AsyncUserProfile.vue'

// 错误: 没有 Suspense 包装器的异步组件
test('显示用户数据', async () => {
  // 这不会渲染 - Vue 期望异步 setup 有 Suspense 包装器
  const wrapper = mount(AsyncUserProfile, {
    props: { userId: 1 }
  })

  await flushPromises()

  // 这会失败 - 组件从未渲染
  expect(wrapper.find('.username').text()).toBe('John')
})
```

**正确 - 手动包装器组件:**
```javascript
import { mount, flushPromises } from '@vue/test-utils'
import { defineComponent, Suspense } from 'vue'
import AsyncUserProfile from './AsyncUserProfile.vue'

test('显示用户数据', async () => {
  // 创建带有 Suspense 的包装器组件
  const TestWrapper = defineComponent({
    components: { AsyncUserProfile },
    template: `
      <Suspense>
        <AsyncUserProfile :user-id="1" />
        <template #fallback>Loading...</template>
      </Suspense>
    `
  })

  const wrapper = mount(TestWrapper)

  // 最初显示 fallback
  expect(wrapper.text()).toContain('Loading...')

  // 等待异步 setup 完成
  await flushPromises()

  // 查找实际组件进行详细断言
  const profile = wrapper.findComponent(AsyncUserProfile)
  expect(profile.find('.username').text()).toBe('John')
})
```

**正确 - 可重用的辅助函数:**
```javascript
// test-utils.js
import { mount, flushPromises } from '@vue/test-utils'
import { defineComponent, Suspense, h } from 'vue'

export async function mountSuspense(component, options = {}) {
  const { props, slots, ...mountOptions } = options

  const wrapper = mount(
    defineComponent({
      render() {
        return h(
          Suspense,
          null,
          {
            default: () => h(component, props, slots),
            fallback: () => h('div', 'Loading...')
          }
        )
      }
    }),
    mountOptions
  )

  // 等待异步组件解析
  await flushPromises()

  return {
    wrapper,
    // 提供对实际组件的轻松访问
    component: wrapper.findComponent(component)
  }
}
```

```javascript
// AsyncUserProfile.test.js
import { mountSuspense } from './test-utils'
import AsyncUserProfile from './AsyncUserProfile.vue'

test('显示用户数据', async () => {
  const { component } = await mountSuspense(AsyncUserProfile, {
    props: { userId: 1 },
    global: {
      stubs: {
        // 如果需要,存根任何子组件
      }
    }
  })

  expect(component.find('.username').text()).toBe('John')
})

test('优雅地处理错误', async () => {
  const { component } = await mountSuspense(AsyncUserProfile, {
    props: { userId: 'invalid' }
  })

  expect(component.find('.error').exists()).toBe(true)
})
```

## 使用 onErrorCaptured 测试

```javascript
import { mount, flushPromises } from '@vue/test-utils'
import { defineComponent, Suspense, h, ref, onErrorCaptured } from 'vue'
import AsyncComponent from './AsyncComponent.vue'

test('捕获异步错误', async () => {
  const capturedError = ref(null)

  const TestWrapper = defineComponent({
    setup() {
      onErrorCaptured((error) => {
        capturedError.value = error
        return true // 防止错误传播
      })
      return { capturedError }
    },
    render() {
      return h(Suspense, null, {
        default: () => h(AsyncComponent, { shouldFail: true }),
        fallback: () => h('div', 'Loading...')
      })
    }
  })

  const wrapper = mount(TestWrapper)
  await flushPromises()

  expect(capturedError.value).toBeTruthy()
  expect(capturedError.value.message).toContain('Failed to load')
})
```

## 使用 Nuxt 的 mountSuspended

```javascript
// 如果使用 Nuxt,使用内置的 mountSuspended 辅助函数
import { mountSuspended } from '@nuxt/test-utils/runtime'
import AsyncPage from './AsyncPage.vue'

test('渲染异步页面', async () => {
  const wrapper = await mountSuspended(AsyncPage, {
    props: { id: 1 }
  })

  expect(wrapper.find('h1').text()).toBe('Page Title')
})
```

## 重要注意事项

### @testing-library/vue 限制
```javascript
// 注意: @testing-library/vue 有 Suspense 问题
// 改用 @vue/test-utils 测试异步组件

// 如果必须使用 Testing Library,创建手动包装器:
import { render, waitFor } from '@testing-library/vue'

test('带有 testing library 的异步组件', async () => {
  const TestWrapper = {
    template: `
      <Suspense>
        <AsyncComponent />
      </Suspense>
    `,
    components: { AsyncComponent }
  }

  const { getByText } = render(TestWrapper)

  await waitFor(() => {
    expect(getByText('Loaded content')).toBeInTheDocument()
  })
})
```

### 访问组件实例
```javascript
test('在异步组件上访问 vm', async () => {
  const { wrapper, component } = await mountSuspense(AsyncComponent)

  // wrapper.vm 是 Suspense 包装器 - 没有用
  // 使用 component.vm 获取实际的异步组件
  expect(component.vm.someData).toBe('value')
})
```

## 参考
- [Vue Test Utils - 异步 Suspense](https://test-utils.vuejs.org/guide/advanced/async-suspense)
- [Vue.js Suspense 文档](https://vuejs.org/guide/built-ins/suspense.html)
- [Testing Library Vue Suspense 问题](https://github.com/testing-library/vue-testing-library/issues/230)
