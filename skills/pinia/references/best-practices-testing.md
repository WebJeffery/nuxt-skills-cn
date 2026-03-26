---
name: testing
description: 使用 @pinia/testing 进行单元测试 store 和组件
---

# 测试 Stores

## 单元测试 Stores

为每个测试创建新的 pinia 实例:

```ts
import { setActivePinia, createPinia } from 'pinia'
import { useCounterStore } from '../src/stores/counter'

describe('Counter Store', () => {
  beforeEach(() => {
    setActivePinia(createPinia())
  })

  it('increments', () => {
    const counter = useCounterStore()
    expect(counter.n).toBe(0)
    counter.increment()
    expect(counter.n).toBe(1)
  })
})
```

### 使用插件

```ts
import { setActivePinia, createPinia } from 'pinia'
import { createApp } from 'vue'
import { somePlugin } from '../src/stores/plugin'

const app = createApp({})

beforeEach(() => {
  const pinia = createPinia().use(somePlugin)
  app.use(pinia)
  setActivePinia(pinia)
})
```

## 测试组件

安装 `@pinia/testing`:

```bash
npm i -D @pinia/testing
```

使用 `createTestingPinia()`:

```ts
import { mount } from '@vue/test-utils'
import { createTestingPinia } from '@pinia/testing'
import { useSomeStore } from '@/stores/myStore'

const wrapper = mount(Counter, {
  global: {
    plugins: [createTestingPinia()],
  },
})

const store = useSomeStore()

// 直接操作状态
store.name = 'new name'
store.$patch({ name: 'new name' })

// Actions 默认被 stub
store.someAction()
expect(store.someAction).toHaveBeenCalledTimes(1)
```

## 初始状态

为测试设置初始状态:

```ts
const wrapper = mount(Counter, {
  global: {
    plugins: [
      createTestingPinia({
        initialState: {
          counter: { n: 20 }, // Store 名称 → 初始状态
        },
      }),
    ],
  },
})
```

## Action Stubbing

### 执行真实 Actions

```ts
createTestingPinia({ stubActions: false })
```

### 选择性 Stubbing

```ts
// 仅 stub 特定 actions
createTestingPinia({
  stubActions: ['increment', 'reset'],
})

// 或使用函数
createTestingPinia({
  stubActions: (actionName, store) => {
    if (actionName.startsWith('set')) return true
    return false
  },
})
```

### Mock Action 返回值

```ts
import type { Mock } from 'vitest'

// 获取 store 后
store.someAction.mockResolvedValue('mocked value')
```

## Mocking Getters

Getters 在测试中是可写的:

```ts
const pinia = createTestingPinia()
const counter = useCounterStore(pinia)

counter.double = 3 // 覆盖计算值

// 重置为默认行为
counter.double = undefined
counter.double // 现在正常计算
```

## 自定义 Spy 函数

如果不使用带有全局的 Jest/Vitest:

```ts
import { vi } from 'vitest'

createTestingPinia({
  createSpy: vi.fn,
})
```

使用 Sinon:

```ts
import sinon from 'sinon'

createTestingPinia({
  createSpy: sinon.spy,
})
```

## 测试中的 Pinia 插件

将插件传递给 `createTestingPinia()`:

```ts
import { somePlugin } from '../src/stores/plugin'

createTestingPinia({
  stubActions: false,
  plugins: [somePlugin],
})
```

**不要使用** `testingPinia.use(MyPlugin)` - 在选项中传递插件。

## 类型安全的 Mocked Store

```ts
import type { Mock } from 'vitest'
import type { Store, StoreDefinition } from 'pinia'

function mockedStore<TStoreDef extends () => unknown>(
  useStore: TStoreDef
): TStoreDef extends StoreDefinition<infer Id, infer State, infer Getters, infer Actions>
  ? Store<Id, State, Record<string, never>, {
      [K in keyof Actions]: Actions[K] extends (...args: any[]) => any
        ? Mock<Actions[K]>
        : Actions[K]
    }>
  : ReturnType<TStoreDef> {
  return useStore() as any
}

// 使用
const store = mockedStore(useSomeStore)
store.someAction.mockResolvedValue('value') // 类型安全!
```

## E2E 测试

无需特殊处理 - Pinia 正常工作。

<!--
源引用:
- https://pinia.vuejs.org/cookbook/testing.html
-->
