---
name: expect-api
description: 断言,包括 matchers、非对称 matchers 和自定义 matchers
---

# Expect API

Vitest 使用 Chai 断言和 Jest 兼容的 API。

## 基本断言

```ts
import { expect, test } from 'vitest'

test('assertions', () => {
  // 相等性
  expect(1 + 1).toBe(2)              // 严格相等 (===)
  expect({ a: 1 }).toEqual({ a: 1 }) // 深度相等

  // 真值性
  expect(true).toBeTruthy()
  expect(false).toBeFalsy()
  expect(null).toBeNull()
  expect(undefined).toBeUndefined()
  expect('value').toBeDefined()

  // 数字
  expect(10).toBeGreaterThan(5)
  expect(10).toBeGreaterThanOrEqual(10)
  expect(5).toBeLessThan(10)
  expect(0.1 + 0.2).toBeCloseTo(0.3, 5)

  // 字符串
  expect('hello world').toMatch(/world/)
  expect('hello').toContain('ell')

  // 数组
  expect([1, 2, 3]).toContain(2)
  expect([{ a: 1 }]).toContainEqual({ a: 1 })
  expect([1, 2, 3]).toHaveLength(3)

  // 对象
  expect({ a: 1, b: 2 }).toHaveProperty('a')
  expect({ a: 1, b: 2 }).toHaveProperty('a', 1)
  expect({ a: { b: 1 } }).toHaveProperty('a.b', 1)
  expect({ a: 1 }).toMatchObject({ a: 1 })

  // 类型
  expect('string').toBeTypeOf('string')
  expect(new Date()).toBeInstanceOf(Date)
})
```

## 否定

```ts
expect(1).not.toBe(2)
expect({ a: 1 }).not.toEqual({ a: 2 })
```

## 错误断言

```ts
// 同步错误 - 包装在函数中
expect(() => throwError()).toThrow()
expect(() => throwError()).toThrow('message')
expect(() => throwError()).toThrow(/pattern/)
expect(() => throwError()).toThrow(CustomError)

// 异步错误 - 使用 rejects
await expect(asyncThrow()).rejects.toThrow('error')
```

## Promise 断言

```ts
// 解析
await expect(Promise.resolve(1)).resolves.toBe(1)
await expect(fetchData()).resolves.toEqual({ data: true })

// 拒绝
await expect(Promise.reject('error')).rejects.toBe('error')
await expect(failingFetch()).rejects.toThrow()
```

## Spy/Mock 断言

```ts
const fn = vi.fn()
fn('arg1', 'arg2')
fn('arg3')

expect(fn).toHaveBeenCalled()
expect(fn).toHaveBeenCalledTimes(2)
expect(fn).toHaveBeenCalledWith('arg1', 'arg2')
expect(fn).toHaveBeenLastCalledWith('arg3')
expect(fn).toHaveBeenNthCalledWith(1, 'arg1', 'arg2')

expect(fn).toHaveReturned()
expect(fn).toHaveReturnedWith(value)
```

## 非对称 Matchers

在 `toEqual`、`toHaveBeenCalledWith` 等内部使用:

```ts
expect({ id: 1, name: 'test' }).toEqual({
  id: expect.any(Number),
  name: expect.any(String),
})

expect({ a: 1, b: 2, c: 3 }).toEqual(
  expect.objectContaining({ a: 1 })
)

expect([1, 2, 3, 4]).toEqual(
  expect.arrayContaining([1, 3])
)

expect('hello world').toEqual(
  expect.stringContaining('world')
)

expect('hello world').toEqual(
  expect.stringMatching(/world$/)
)

expect({ value: null }).toEqual({
  value: expect.anything() // 匹配除 null/undefined 之外的任何内容
})

// 使用 expect.not 否定
expect([1, 2]).toEqual(
  expect.not.arrayContaining([3])
)
```

## 软断言

失败后继续测试:

```ts
expect.soft(1).toBe(2) // 标记测试失败但继续
expect.soft(2).toBe(3) // 也运行
// 所有失败在最后报告
```

## 轮询断言

重试直到通过:

```ts
await expect.poll(() => fetchStatus()).toBe('ready')

await expect.poll(
  () => document.querySelector('.element'),
  { interval: 100, timeout: 5000 }
).toBeTruthy()
```

## 断言计数

```ts
test('async assertions', async () => {
  expect.assertions(2) // 必须运行恰好 2 个断言
  
  await doAsync((data) => {
    expect(data).toBeDefined()
    expect(data.id).toBe(1)
  })
})

test('at least one', () => {
  expect.hasAssertions() // 必须运行至少 1 个断言
})
```

## 扩展 Matchers

```ts
expect.extend({
  toBeWithinRange(received, floor, ceiling) {
    const pass = received >= floor && received <= ceiling
    return {
      pass,
      message: () => 
        `expected ${received} to be within range ${floor} - ${ceiling}`,
    }
  },
})

test('custom matcher', () => {
  expect(100).toBeWithinRange(90, 110)
})
```

## 快照断言

```ts
expect(data).toMatchSnapshot()
expect(data).toMatchInlineSnapshot(`{ "id": 1 }`)
await expect(result).toMatchFileSnapshot('./expected.json')

expect(() => throw new Error('fail')).toThrowErrorMatchingSnapshot()
```

## 关键点

- 对基本类型使用 `toBe`,对对象/数组使用 `toEqual`
- `toStrictEqual` 检查未定义的属性和数组稀疏性
- 始终 `await` 异步断言(`resolves`、`rejects`、`poll`)
- 在并发测试中使用上下文的 `expect` 进行正确跟踪
- `toThrow` 需要将同步代码包装在函数中

<!-- 
Source references:
- https://vitest.dev/api/expect.html
-->
