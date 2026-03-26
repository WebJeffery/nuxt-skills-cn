---
name: mocking
description: 使用 vi 工具模拟函数、模块、计时器和日期
---

# 模拟

## 模拟函数

```ts
import { expect, vi } from 'vitest'

// 创建模拟函数
const fn = vi.fn()
fn('hello')

expect(fn).toHaveBeenCalled()
expect(fn).toHaveBeenCalledWith('hello')

// 使用实现
const add = vi.fn((a, b) => a + b)
expect(add(1, 2)).toBe(3)

// 模拟返回值
fn.mockReturnValue(42)
fn.mockReturnValueOnce(1).mockReturnValueOnce(2)
fn.mockResolvedValue({ data: true })
fn.mockRejectedValue(new Error('fail'))

// 模拟实现
fn.mockImplementation((x) => x * 2)
fn.mockImplementationOnce(() => 'first call')
```

## 监视对象

```ts
const cart = {
  getTotal: () => 100,
}

const spy = vi.spyOn(cart, 'getTotal')
cart.getTotal()

expect(spy).toHaveBeenCalled()

// 模拟实现
spy.mockReturnValue(200)
expect(cart.getTotal()).toBe(200)

// 恢复原始
spy.mockRestore()
```

## 模块模拟

```ts
// vi.mock 被提升到文件顶部
vi.mock('./api', () => ({
  fetchUser: vi.fn(() => ({ id: 1, name: 'Mock' })),
}))

import { fetchUser } from './api'

test('mocked module', () => {
  expect(fetchUser()).toEqual({ id: 1, name: 'Mock' })
})
```

### 部分模拟

```ts
vi.mock('./utils', async (importOriginal) => {
  const actual = await importOriginal()
  return {
    ...actual,
    specificFunction: vi.fn(),
  }
})
```

### 使用 Spy 自动模拟

```ts
// 保留实现但监视调用
vi.mock('./calculator', { spy: true })

import { add } from './calculator'

test('spy on module', () => {
  const result = add(1, 2) // 真实实现
  expect(result).toBe(3)
  expect(add).toHaveBeenCalledWith(1, 2)
})
```

### 手动模拟 (__mocks__)

```
src/
  __mocks__/
    axios.ts      # 模拟 'axios'
  api/
    __mocks__/
      client.ts   # 模拟 './client'
    client.ts
```

```ts
// 只需调用 vi.mock 而无需工厂
vi.mock('axios')
vi.mock('./api/client')
```

## 动态模拟 (vi.doMock)

不提升 - 用于动态导入:

```ts
test('dynamic mock', async () => {
  vi.doMock('./config', () => ({
    apiUrl: 'http://test.local',
  }))
  
  const { apiUrl } = await import('./config')
  expect(apiUrl).toBe('http://test.local')
  
  vi.doUnmock('./config')
})
```

## 模拟计时器

```ts
import { afterEach, beforeEach, vi } from 'vitest'

beforeEach(() => {
  vi.useFakeTimers()
})

afterEach(() => {
  vi.useRealTimers()
})

test('timers', () => {
  const fn = vi.fn()
  setTimeout(fn, 1000)
  
  expect(fn).not.toHaveBeenCalled()
  
  vi.advanceTimersByTime(1000)
  expect(fn).toHaveBeenCalled()
})

// 其他计时器方法
vi.runAllTimers()           // 运行所有挂起的计时器
vi.runOnlyPendingTimers()   // 仅运行当前挂起的
vi.advanceTimersToNextTimer() // 前进到下一个计时器
```

### 异步计时器方法

```ts
test('async timers', async () => {
  vi.useFakeTimers()
  
  let resolved = false
  setTimeout(() => Promise.resolve().then(() => { resolved = true }), 100)
  
  await vi.advanceTimersByTimeAsync(100)
  expect(resolved).toBe(true)
})
```

## 模拟日期

```ts
vi.setSystemTime(new Date('2024-01-01'))
expect(new Date().getFullYear()).toBe(2024)

vi.useRealTimers() // 恢复
```

## 模拟全局变量

```ts
vi.stubGlobal('fetch', vi.fn(() => 
  Promise.resolve({ json: () => ({ data: 'mock' }) })
))

// 恢复
vi.unstubAllGlobals()
```

## 模拟环境变量

```ts
vi.stubEnv('API_KEY', 'test-key')
expect(import.meta.env.API_KEY).toBe('test-key')

// 恢复
vi.unstubAllEnvs()
```

## 清除模拟

```ts
const fn = vi.fn()
fn()

fn.mockClear()       // 清除调用历史
fn.mockReset()       // 清除历史 + 实现
fn.mockRestore()     // 恢复原始(用于 spies)

// 全局
vi.clearAllMocks()
vi.resetAllMocks()
vi.restoreAllMocks()
```

## 配置自动重置

```ts
// vitest.config.ts
defineConfig({
  test: {
    clearMocks: true,    // 在每个测试之前清除
    mockReset: true,     // 在每个测试之前重置
    restoreMocks: true,  // 在每个测试之后恢复
    unstubEnvs: true,    // 恢复环境变量
    unstubGlobals: true, // 恢复全局变量
  },
})
```

## 模拟的提升变量

```ts
const mockFn = vi.hoisted(() => vi.fn())

vi.mock('./module', () => ({
  getData: mockFn,
}))

import { getData } from './module'

test('hoisted mock', () => {
  mockFn.mockReturnValue('test')
  expect(getData()).toBe('test')
})
```

## 关键点

- `vi.mock` 被提升 - 在导入之前调用
- 使用 `vi.doMock` 进行动态、非提升模拟
- 始终恢复模拟以避免测试污染
- 使用 `{ spy: true }` 保留实现但跟踪调用
- `vi.hoisted` 允许您在模拟工厂中引用变量

<!-- 
Source references:
- https://vitest.dev/guide/mocking.html
- https://vitest.dev/api/vi.html
-->
