---
name: test-context-fixtures
description: 测试上下文,使用 test.extend 的自定义 fixtures
---

# 测试上下文 & Fixtures

## 内置上下文

每个测试接收上下文作为第一个参数:

```ts
test('context', ({ task, expect, skip }) => {
  console.log(task.name)  // 测试名称
  expect(1).toBe(1)       // 上下文绑定的 expect
  skip()                  // 动态跳过测试
})
```

### 上下文属性

- `task` - 测试元数据(名称、文件等)
- `expect` - 绑定到此测试的 expect(对并发测试很重要)
- `skip(condition?, message?)` - 跳过测试
- `onTestFinished(fn)` - 测试后清理
- `onTestFailed(fn)` - 仅在失败时运行

## 使用 test.extend 的自定义 Fixtures

创建可重用的测试工具:

```ts
import { test as base } from 'vitest'

// 定义 fixture 类型
interface Fixtures {
  db: Database
  user: User
}

// 创建扩展的测试
export const test = base.extend<Fixtures>({
  // 带设置/清理的 fixture
  db: async ({}, use) => {
    const db = await createDatabase()
    await use(db)           // 提供给测试
    await db.close()        // 清理
  },
  
  // 依赖于另一个 fixture 的 fixture
  user: async ({ db }, use) => {
    const user = await db.createUser({ name: 'Test' })
    await use(user)
    await db.deleteUser(user.id)
  },
})
```

使用 fixtures:

```ts
test('query user', async ({ db, user }) => {
  const found = await db.findUser(user.id)
  expect(found).toEqual(user)
})
```

## Fixture 初始化

Fixtures 仅在访问时初始化:

```ts
const test = base.extend({
  expensive: async ({}, use) => {
    console.log('initializing')  // 仅在测试使用时运行
    await use('value')
  },
})

test('no fixture', () => {})           // expensive 未调用
test('uses fixture', ({ expensive }) => {}) // expensive 被调用
```

## 自动 Fixtures

为每个测试运行 fixture:

```ts
const test = base.extend({
  setup: [
    async ({}, use) => {
      await globalSetup()
      await use()
      await globalTeardown()
    },
    { auto: true }  // 始终运行
  ],
})
```

## 作用域 Fixtures

### 文件作用域

每个文件初始化一次:

```ts
const test = base.extend({
  connection: [
    async ({}, use) => {
      const conn = await connect()
      await use(conn)
      await conn.close()
    },
    { scope: 'file' }
  ],
})
```

### 工作器作用域

每个工作器初始化一次:

```ts
const test = base.extend({
  sharedResource: [
    async ({}, use) => {
      await use(globalResource)
    },
    { scope: 'worker' }
  ],
})
```

## 注入的 Fixtures(来自配置)

每个项目覆盖 fixtures:

```ts
// 测试文件
const test = base.extend({
  apiUrl: ['/default', { injected: true }],
})

// vitest.config.ts
defineConfig({
  test: {
    projects: [
      {
        test: {
          name: 'prod',
          provide: { apiUrl: 'https://api.prod.com' },
        },
      },
    ],
  },
})
```

## 每个套件的作用域值

为特定套件覆盖 fixture:

```ts
const test = base.extend({
  environment: 'development',
})

describe('production tests', () => {
  test.scoped({ environment: 'production' })
  
  test('uses production', ({ environment }) => {
    expect(environment).toBe('production')
  })
})

test('uses default', ({ environment }) => {
  expect(environment).toBe('development')
})
```

## 扩展测试钩子

使用 fixtures 的类型感知钩子:

```ts
const test = base.extend<{ db: Database }>({
  db: async ({}, use) => {
    const db = await createDb()
    await use(db)
    await db.close()
  },
})

// 钩子知道 fixtures
test.beforeEach(({ db }) => {
  db.seed()
})

test.afterEach(({ db }) => {
  db.clear()
})
```

## 组合 Fixtures

从另一个扩展的测试扩展:

```ts
// base-test.ts
export const test = base.extend<{ db: Database }>({
  db: async ({}, use) => { /* ... */ },
})

// admin-test.ts
import { test as dbTest } from './base-test'

export const test = dbTest.extend<{ admin: User }>({
  admin: async ({ db }, use) => {
    const admin = await db.createAdmin()
    await use(admin)
  },
})
```

## 关键点

- 使用 `{ }` 解构访问 fixtures
- Fixtures 是惰性的 - 仅在访问时初始化
- 从 fixtures 返回清理函数
- 使用 `{ auto: true }` 进行设置 fixtures
- 使用 `{ scope: 'file' }` 进行昂贵的共享资源
- Fixtures 组合 - 从扩展的测试扩展

<!-- 
Source references:
- https://vitest.dev/guide/test-context.html
-->
