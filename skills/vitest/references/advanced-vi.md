---
name: vi-utilities
description: vi 模拟、计时器、工具的辅助函数
---

# Vi 工具

`vi` 辅助函数提供模拟和实用函数。

```ts
import { vi } from 'vitest'
```

## 模拟函数

```ts
// 创建模拟
const fn = vi.fn()
const fnWithImpl = vi.fn((x) => x * 2)

// 检查是否为模拟
vi.isMockFunction(fn) // true

// 模拟方法
fn.mockReturnValue(42)
fn.mockReturnValueOnce(1)
fn.mockResolvedValue(data)
fn.mockRejectedValue(error)
fn.mockImplementation(() => 'result')
fn.mockImplementationOnce(() => 'once')

// 清除/重置
fn.mockClear()    // 清除调用历史
fn.mockReset()    // 清除历史 + 实现
fn.mockRestore()  // 恢复原始(用于 spies)
```

## 监视

```ts
const obj = { method: () => 'original' }

const spy = vi.spyOn(obj, 'method')
obj.method()

expect(spy).toHaveBeenCalled()

// 模拟实现
spy.mockReturnValue('mocked')

// 监视 getter/setter
vi.spyOn(obj, 'prop', 'get').mockReturnValue('value')
```

## 模块模拟

```ts
// 提升到文件顶部
vi.mock('./module', () => ({
  fn: vi.fn(),
}))

// 部分模拟
vi.mock('./module', async (importOriginal) => ({
  ...(await importOriginal()),
  specificFn: vi.fn(),
}))

// Spy 模式 - 保留实现
vi.mock('./module', { spy: true })

// 在模拟中导入实际模块
const actual = await vi.importActual('./module')

// 作为模拟导入
const mocked = await vi.importMock('./module')
```

## 动态模拟

```ts
// 不提升 - 与动态导入一起使用
vi.doMock('./config', () => ({ key: 'value' }))
const config = await import('./config')

// 取消模拟
vi.doUnmock('./config')
vi.unmock('./module') // 提升
```

## 重置模块

```ts
// 清除模块缓存
vi.resetModules()

// 等待动态导入
await vi.dynamicImportSettled()
```

## 假计时器

```ts
vi.useFakeTimers()

setTimeout(() => console.log('done'), 1000)

// 前进时间
vi.advanceTimersByTime(1000)
vi.advanceTimersByTimeAsync(1000)  // 用于异步回调
vi.advanceTimersToNextTimer()
vi.advanceTimersToNextFrame()      // requestAnimationFrame

// 运行所有计时器
vi.runAllTimers()
vi.runAllTimersAsync()
vi.runOnlyPendingTimers()

// 清除计时器
vi.clearAllTimers()

// 检查状态
vi.getTimerCount()
vi.isFakeTimers()

// 恢复
vi.useRealTimers()
```

## 模拟日期/时间

```ts
vi.setSystemTime(new Date('2024-01-01'))
expect(new Date().getFullYear()).toBe(2024)

vi.getMockedSystemTime()  // 获取模拟的日期
vi.getRealSystemTime()    // 获取真实时间(毫秒)
```

## 全局/环境模拟

```ts
// 存根全局变量
vi.stubGlobal('fetch', vi.fn())
vi.unstubAllGlobals()

// 存根环境变量
vi.stubEnv('API_KEY', 'test')
vi.stubEnv('NODE_ENV', 'test')
vi.unstubAllEnvs()
```

## 提升的代码

在导入之前运行代码:

```ts
const mock = vi.hoisted(() => vi.fn())

vi.mock('./module', () => ({
  fn: mock, // 可以引用提升的变量
}))
```

## 等待工具

```ts
// 等待回调成功
await vi.waitFor(async () => {
  const el = document.querySelector('.loaded')
  expect(el).toBeTruthy()
}, { timeout: 5000, interval: 100 })

// 等待真值
const element = await vi.waitUntil(
  () => document.querySelector('.loaded'),
  { timeout: 5000 }
)
```

## 模拟对象

模拟对象的所有方法:

```ts
const original = {
  method: () => 'real',
  nested: { fn: () => 'nested' },
}

const mocked = vi.mockObject(original)
mocked.method()  // undefined(已模拟)
mocked.method.mockReturnValue('mocked')

// Spy 模式
const spied = vi.mockObject(original, { spy: true })
spied.method()  // 'real'
expect(spied.method).toHaveBeenCalled()
```

## 测试配置

```ts
vi.setConfig({
  testTimeout: 10_000,
  hookTimeout: 10_000,
})

vi.resetConfig()
```

## 全局模拟管理

```ts
vi.clearAllMocks()   // 清除所有模拟调用历史
vi.resetAllMocks()   // 重置 + 清除实现
vi.restoreAllMocks() // 恢复原始(spies)
```

## vi.mocked 类型助手

模拟值的 TypeScript 助手:

```ts
import { myFn } from './module'
vi.mock('./module')

// 类型化为模拟
vi.mocked(myFn).mockReturnValue('typed')

// 深度模拟
vi.mocked(myModule, { deep: true })

// 部分模拟类型化
vi.mocked(fn, { partial: true }).mockResolvedValue({ ok: true })
```

## 关键点

- `vi.mock` 被提升 - 使用 `vi.doMock` 进行动态模拟
- `vi.hoisted` 允许您在模拟工厂中引用变量
- 使用 `vi.spyOn` 监视现有方法
- 假计时器需要显式设置和清理
- `vi.waitFor` 重试直到断言通过

<!-- 
Source references:
- https://vitest.dev/api/vi.html
-->
