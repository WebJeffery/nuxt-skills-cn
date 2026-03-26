---
title: 使用 createTestingPinia 和 setActivePinia 配置 Pinia 测试
impact: HIGH
impactDescription: 缺少 Pinia 配置会导致 'injection Symbol(pinia) not found' 错误和测试失败
type: gotcha
tags: [vue3, testing, pinia, vitest, store, mocking, createTestingPinia]
---

# 使用 createTestingPinia 和 setActivePinia 配置 Pinia 测试

**影响: HIGH** - 测试使用 Pinia stores 的组件或 composables 而没有正确配置会导致 "[Vue warn]: injection Symbol(pinia) not found" 错误。测试将失败或行为异常。

对组件测试使用 `@pinia/testing` 包配合 `createTestingPinia`,对直接单元测试 stores 使用 `setActivePinia(createPinia())`。

## 任务清单

- [ ] 安装 `@pinia/testing` 作为开发依赖
- [ ] 在组件测试中使用 `createTestingPinia` 配合 `global.plugins`
- [ ] 在 store 单元测试的 `beforeEach` 中使用 `setActivePinia(createPinia())`
- [ ] 当在 Vitest 中不使用 `globals: true` 时配置 `createSpy: vi.fn`
- [ ] 在每个测试中初始化 store 以获得新鲜状态
- [ ] 当需要真实 action 执行时使用 `stubActions: false`

**错误:**
```javascript
import { mount } from '@vue/test-utils'
import UserProfile from './UserProfile.vue'

// 错误: 缺少 Pinia - 导致注入错误
test('显示用户名', () => {
  const wrapper = mount(UserProfile)  // 错误: injection "Symbol(pinia)" not found
  expect(wrapper.text()).toContain('John')
})
```

```javascript
import { useUserStore } from '@/stores/user'

// 错误: 没有活动的 Pinia 实例
test('user store actions', () => {
  const store = useUserStore()  // 错误: no active Pinia
  store.login('john', 'password')
})
```

**正确 - 组件测试:**
```javascript
import { mount } from '@vue/test-utils'
import { createTestingPinia } from '@pinia/testing'
import { vi } from 'vitest'
import UserProfile from './UserProfile.vue'
import { useUserStore } from '@/stores/user'

// 正确: 提供带有存根 actions 的测试 pinia
test('显示用户名', () => {
  const wrapper = mount(UserProfile, {
    global: {
      plugins: [
        createTestingPinia({
          createSpy: vi.fn,  // 如果不使用 globals: true 则必需
          initialState: {
            user: { name: 'John', email: 'john@example.com' }
          }
        })
      ]
    }
  })

  expect(wrapper.text()).toContain('John')
})

// 正确: 使用存根 actions 测试(默认行为)
test('调用 logout action', async () => {
  const wrapper = mount(UserProfile, {
    global: {
      plugins: [createTestingPinia({ createSpy: vi.fn })]
    }
  })

  // 在使用 createTestingPinia 挂载后获取 store
  const store = useUserStore()

  await wrapper.find('[data-testid="logout"]').trigger('click')

  // Actions 被存根并包装在 spies 中
  expect(store.logout).toHaveBeenCalled()
})
```

**正确 - Store 单元测试:**
```javascript
import { describe, it, expect, beforeEach, vi } from 'vitest'
import { setActivePinia, createPinia } from 'pinia'
import { useUserStore } from '@/stores/user'

describe('User Store', () => {
  beforeEach(() => {
    // 为每个测试创建新的 Pinia 实例
    setActivePinia(createPinia())
  })

  it('使用空用户初始化', () => {
    const store = useUserStore()
    expect(store.user).toBeNull()
    expect(store.isLoggedIn).toBe(false)
  })

  it('登录时更新用户', async () => {
    const store = useUserStore()

    // 真实 action 执行 - 未存根
    await store.login('john', 'password')

    expect(store.user).toEqual({ name: 'John' })
    expect(store.isLoggedIn).toBe(true)
  })

  it('登出时清除用户', () => {
    const store = useUserStore()
    store.user = { name: 'John' }  // 设置初始状态

    store.logout()

    expect(store.user).toBeNull()
  })
})
```

## 使用真实 Actions vs 存根 Actions 测试

```javascript
import { createTestingPinia } from '@pinia/testing'

// 存根 actions(默认) - 用于隔离
const wrapper = mount(Component, {
  global: {
    plugins: [
      createTestingPinia({
        createSpy: vi.fn,
        // stubActions: true (默认) - actions 被模拟
      })
    ]
  }
})

// 真实 actions - 用于集成测试
const wrapper = mount(Component, {
  global: {
    plugins: [
      createTestingPinia({
        createSpy: vi.fn,
        stubActions: false  // Actions 正常执行
      })
    ]
  }
})
```

## 模拟特定 Action 实现

```javascript
import { mount } from '@vue/test-utils'
import { createTestingPinia } from '@pinia/testing'
import { vi } from 'vitest'
import { useCartStore } from '@/stores/cart'

test('处理结账失败', async () => {
  const wrapper = mount(Checkout, {
    global: {
      plugins: [createTestingPinia({ createSpy: vi.fn })]
    }
  })

  const cartStore = useCartStore()

  // 模拟特定 action 行为
  cartStore.checkout.mockRejectedValue(new Error('Payment failed'))

  await wrapper.find('[data-testid="checkout"]').trigger('click')
  await flushPromises()

  expect(wrapper.find('.error').text()).toContain('Payment failed')
})
```

## 使用 vi.spyOn 监视 Actions

```javascript
import { setActivePinia, createPinia } from 'pinia'
import { vi } from 'vitest'
import { useUserStore } from '@/stores/user'

test('跟踪 action 调用', async () => {
  setActivePinia(createPinia())
  const store = useUserStore()

  const loginSpy = vi.spyOn(store, 'login')
  loginSpy.mockResolvedValue({ success: true })

  await store.login('john', 'password')

  expect(loginSpy).toHaveBeenCalledWith('john', 'password')
})
```

## 测试 Store $subscribe

```javascript
import { setActivePinia, createPinia } from 'pinia'
import { useUserStore } from '@/stores/user'

test('状态更改时触发订阅', () => {
  setActivePinia(createPinia())
  const store = useUserStore()

  const callback = vi.fn()
  store.$subscribe(callback)

  store.user = { name: 'John' }

  expect(callback).toHaveBeenCalled()
})
```

## 参考
- [Pinia 测试指南](https://pinia.vuejs.org/cookbook/testing.html)
- [@pinia/testing 包](https://www.npmjs.com/package/@pinia/testing)
- [Vue Test Utils - 插件](https://test-utils.vuejs.org/guide/advanced/plugins.html)
