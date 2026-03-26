---
name: test-api
description: test/it 函数用于使用修饰符定义测试
---

# 测试 API

## 基本测试

```ts
import { expect, test } from 'vitest'

test('adds numbers', () => {
  expect(1 + 1).toBe(2)
})

// 别名: it
import { it } from 'vitest'

it('works the same', () => {
  expect(true).toBe(true)
})
```

## 异步测试

```ts
test('async test', async () => {
  const result = await fetchData()
  expect(result).toBeDefined()
})

// Promise 自动等待
test('returns promise', () => {
  return fetchData().then(result => {
    expect(result).toBeDefined()
  })
})
```

## 测试选项

```ts
// 超时(默认: 5000ms)
test('slow test', async () => {
  // ...
}, 10_000)

// 或使用选项对象
test('with options', { timeout: 10_000, retry: 2 }, async () => {
  // ...
})
```

## 测试修饰符

### 跳过测试

```ts
test.skip('skipped test', () => {
  // 不会运行
})

// 条件跳过
test.skipIf(process.env.CI)('not in CI', () => {})
test.runIf(process.env.CI)('only in CI', () => {})

// 通过上下文动态跳过
test('dynamic skip', ({ skip }) => {
  skip(someCondition, 'reason')
  // ...
})
```

### 聚焦测试

```ts
test.only('only this runs', () => {
  // 文件中的其他测试被跳过
})
```

### 待办测试

```ts
test.todo('implement later')

test.todo('with body', () => {
  // 不运行,显示在报告中
})
```

### 失败测试

```ts
test.fails('expected to fail', () => {
  expect(1).toBe(2) // 测试通过,因为断言失败
})
```

### 并发测试

```ts
// 并行运行测试
test.concurrent('test 1', async ({ expect }) => {
  // 对并发测试使用 context.expect
  expect(await fetch1()).toBe('result')
})

test.concurrent('test 2', async ({ expect }) => {
  expect(await fetch2()).toBe('result')
})
```

### 顺序测试

```ts
// 在并发上下文中强制顺序
test.sequential('must run alone', async () => {})
```

## 参数化测试

### test.each

```ts
test.each([
  [1, 1, 2],
  [1, 2, 3],
  [2, 1, 3],
])('add(%i, %i) = %i', (a, b, expected) => {
  expect(a + b).toBe(expected)
})

// 使用对象
test.each([
  { a: 1, b: 1, expected: 2 },
  { a: 1, b: 2, expected: 3 },
])('add($a, $b) = $expected', ({ a, b, expected }) => {
  expect(a + b).toBe(expected)
})

// 模板字面量
test.each`
  a    | b    | expected
  ${1} | ${1} | ${2}
  ${1} | ${2} | ${3}
`('add($a, $b) = $expected', ({ a, b, expected }) => {
  expect(a + b).toBe(expected)
})
```

### test.for

优先于 `.each` - 不展开数组:

```ts
test.for([
  [1, 1, 2],
  [1, 2, 3],
])('add(%i, %i) = %i', ([a, b, expected], { expect }) => {
  // 第二个参数是 TestContext
  expect(a + b).toBe(expected)
})
```

## 测试上下文

第一个参数提供上下文工具:

```ts
test('with context', ({ expect, skip, task }) => {
  console.log(task.name)   // 测试名称
  skip(someCondition)      // 动态跳过
  expect(1).toBe(1)        // 上下文绑定的 expect
})
```

## 使用 Fixtures 的自定义测试

```ts
import { test as base } from 'vitest'

const test = base.extend({
  db: async ({}, use) => {
    const db = await createDb()
    await use(db)
    await db.close()
  },
})

test('query', async ({ db }) => {
  const users = await db.query('SELECT * FROM users')
  expect(users).toBeDefined()
})
```

## 重试配置

```ts
test('flaky test', { retry: 3 }, async () => {
  // 失败时重试最多 3 次
})

// 高级重试选项
test('with delay', {
  retry: {
    count: 3,
    delay: 1000,
    condition: /timeout/i, // 仅在超时错误时重试
  },
}, async () => {})
```

## 标签

```ts
test('database test', { tags: ['db', 'slow'] }, async () => {})

// 使用以下命令运行: vitest --tags db
```

## 关键点

- 没有主体的测试被标记为 `todo`
- 除非 `allowOnly: true`,否则 `test.only` 在 CI 中抛出
- 对并发测试和快照使用上下文的 `expect`
- 如果作为第一个参数传递,函数名称用作测试名称

<!-- 
Source references:
- https://vitest.dev/api/test.html
-->
