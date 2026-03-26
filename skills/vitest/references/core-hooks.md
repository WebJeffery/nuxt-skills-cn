---
name: lifecycle-hooks
description: beforeEach、afterEach、beforeAll、afterAll 和 around 钩子
---

# 生命周期钩子

## 基本钩子

```ts
import { afterAll, afterEach, beforeAll, beforeEach, test } from 'vitest'

beforeAll(async () => {
  // 在文件/套件中的所有测试之前运行一次
  await setupDatabase()
})

afterAll(async () => {
  // 在文件/套件中的所有测试之后运行一次
  await teardownDatabase()
})

beforeEach(async () => {
  // 在每个测试之前运行
  await clearTestData()
})

afterEach(async () => {
  // 在每个测试之后运行
  await cleanupMocks()
})
```

## 清理返回模式

从 `before*` 钩子返回清理函数:

```ts
beforeAll(async () => {
  const server = await startServer()
  
  // 返回的函数作为 afterAll 运行
  return async () => {
    await server.close()
  }
})

beforeEach(async () => {
  const connection = await connect()
  
  // 作为 afterEach 运行
  return () => connection.close()
})
```

## 作用域钩子

钩子应用于当前套件和嵌套套件:

```ts
describe('outer', () => {
  beforeEach(() => console.log('outer before'))
  
  test('test 1', () => {}) // outer before → test
  
  describe('inner', () => {
    beforeEach(() => console.log('inner before'))
    
    test('test 2', () => {}) // outer before → inner before → test
  })
})
```

## 钩子超时

```ts
beforeAll(async () => {
  await slowSetup()
}, 30_000) // 30 秒超时
```

## Around 钩子

使用设置/清理上下文包装测试:

```ts
import { aroundEach, test } from 'vitest'

// 在数据库事务中包装每个测试
aroundEach(async (runTest) => {
  await db.beginTransaction()
  await runTest() // 必须调用!
  await db.rollback()
})

test('insert user', async () => {
  await db.insert({ name: 'Alice' })
  // 测试后自动回滚
})
```

### aroundAll

包装整个套件:

```ts
import { aroundAll, test } from 'vitest'

aroundAll(async (runSuite) => {
  console.log('before all tests')
  await runSuite() // 必须调用!
  console.log('after all tests')
})
```

### 多个 Around 钩子

像洋葱层一样嵌套:

```ts
aroundEach(async (runTest) => {
  console.log('outer before')
  await runTest()
  console.log('outer after')
})

aroundEach(async (runTest) => {
  console.log('inner before')
  await runTest()
  console.log('inner after')
})

// 顺序: outer before → inner before → test → inner after → outer after
```

## 测试钩子

在测试体内:

```ts
import { onTestFailed, onTestFinished, test } from 'vitest'

test('with cleanup', () => {
  const db = connect()
  
  // 测试完成后运行(通过或失败)
  onTestFinished(() => db.close())
  
  // 仅在测试失败时运行
  onTestFailed(({ task }) => {
    console.log('Failed:', task.result?.errors)
  })
  
  db.query('SELECT * FROM users')
})
```

### 可重用清理模式

```ts
function useTestDb() {
  const db = connect()
  onTestFinished(() => db.close())
  return db
}

test('query users', () => {
  const db = useTestDb()
  expect(db.query('SELECT * FROM users')).toBeDefined()
})

test('query orders', () => {
  const db = useTestDb() // 新连接,自动关闭
  expect(db.query('SELECT * FROM orders')).toBeDefined()
})
```

## 并发测试钩子

对于并发测试,使用上下文的钩子:

```ts
test.concurrent('concurrent', ({ onTestFinished }) => {
  const resource = allocate()
  onTestFinished(() => resource.release())
})
```

## 扩展测试钩子

使用 `test.extend`,钩子是类型感知的:

```ts
const test = base.extend<{ db: Database }>({
  db: async ({}, use) => {
    const db = await createDb()
    await use(db)
    await db.close()
  },
})

// 这些钩子知道 `db` fixture
test.beforeEach(({ db }) => {
  db.seed()
})

test.afterEach(({ db }) => {
  db.clear()
})
```

## 钩子执行顺序

默认顺序(堆栈):
1. `beforeAll`(按顺序)
2. `beforeEach`(按顺序)
3. 测试
4. `afterEach`(反向顺序)
5. `afterAll`(反向顺序)

使用 `sequence.hooks` 配置:

```ts
defineConfig({
  test: {
    sequence: {
      hooks: 'list', // 'stack'(默认)、'list'、'parallel'
    },
  },
})
```

## 关键点

- 钩子在类型检查期间不调用
- 从 `before*` 返回清理函数以避免 `after*` 重复
- `aroundEach`/`aroundAll` 必须调用 `runTest()`/`runSuite()`
- `onTestFinished` 始终运行,即使测试失败
- 对并发测试使用上下文钩子

<!-- 
Source references:
- https://vitest.dev/api/hooks.html
-->
