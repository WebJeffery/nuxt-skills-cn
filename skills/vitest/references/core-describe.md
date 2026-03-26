---
name: describe-api
description: describe/suite 用于将测试分组为逻辑块
---

# Describe API

将相关测试分组为套件,以便组织和共享设置。

## 基本用法

```ts
import { describe, expect, test } from 'vitest'

describe('Math', () => {
  test('adds numbers', () => {
    expect(1 + 1).toBe(2)
  })

  test('subtracts numbers', () => {
    expect(3 - 1).toBe(2)
  })
})

// 别名: suite
import { suite } from 'vitest'
suite('equivalent to describe', () => {})
```

## 嵌套套件

```ts
describe('User', () => {
  describe('when logged in', () => {
    test('shows dashboard', () => {})
    test('can update profile', () => {})
  })

  describe('when logged out', () => {
    test('shows login page', () => {})
  })
})
```

## 套件选项

```ts
// 所有测试继承选项
describe('slow tests', { timeout: 30_000 }, () => {
  test('test 1', () => {}) // 30s 超时
  test('test 2', () => {}) // 30s 超时
})
```

## 套件修饰符

### 跳过套件

```ts
describe.skip('skipped suite', () => {
  test('wont run', () => {})
})

// 条件
describe.skipIf(process.env.CI)('not in CI', () => {})
describe.runIf(!process.env.CI)('only local', () => {})
```

### 聚焦套件

```ts
describe.only('only this suite runs', () => {
  test('runs', () => {})
})
```

### 待办套件

```ts
describe.todo('implement later')
```

### 并发套件

```ts
// 所有测试并行运行
describe.concurrent('parallel tests', () => {
  test('test 1', async ({ expect }) => {})
  test('test 2', async ({ expect }) => {})
})
```

### 并发中的顺序

```ts
describe.concurrent('parallel', () => {
  test('concurrent 1', async () => {})
  
  describe.sequential('must be sequential', () => {
    test('step 1', async () => {})
    test('step 2', async () => {})
  })
})
```

### 随机化测试

```ts
describe.shuffle('random order', () => {
  test('test 1', () => {})
  test('test 2', () => {})
  test('test 3', () => {})
})

// 或使用选项
describe('random', { shuffle: true }, () => {})
```

## 参数化套件

### describe.each

```ts
describe.each([
  { name: 'Chrome', version: 100 },
  { name: 'Firefox', version: 90 },
])('$name browser', ({ name, version }) => {
  test('has version', () => {
    expect(version).toBeGreaterThan(0)
  })
})
```

### describe.for

```ts
describe.for([
  ['Chrome', 100],
  ['Firefox', 90],
])('%s browser', ([name, version]) => {
  test('has version', () => {
    expect(version).toBeGreaterThan(0)
  })
})
```

## 套件中的钩子

```ts
describe('Database', () => {
  let db

  beforeAll(async () => {
    db = await createDb()
  })

  afterAll(async () => {
    await db.close()
  })

  beforeEach(async () => {
    await db.clear()
  })

  test('insert works', async () => {
    await db.insert({ name: 'test' })
    expect(await db.count()).toBe(1)
  })
})
```

## 修饰符组合

所有修饰符都可以链接:

```ts
describe.skip.concurrent('skipped concurrent', () => {})
describe.only.shuffle('only and shuffled', () => {})
describe.concurrent.skip('equivalent', () => {})
```

## 关键点

- 顶级测试属于隐式文件套件
- 嵌套套件继承父级的选项(超时、重试等)
- 钩子作用于其套件和嵌套套件
- 使用 `describe.concurrent` 与上下文的 `expect` 进行快照
- 随机顺序取决于 `sequence.seed` 配置

<!-- 
Source references:
- https://vitest.dev/api/describe.html
-->
