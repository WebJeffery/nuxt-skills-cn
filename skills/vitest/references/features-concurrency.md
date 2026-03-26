---
name: concurrency-parallelism
description: 并发测试、并行执行和分片
---

# 并发与并行

## 文件并行

默认情况下,Vitest 在工作器之间并行运行测试文件:

```ts
defineConfig({
  test: {
    // 并行运行文件(默认: true)
    fileParallelism: true,
    
    // 工作器线程数
    maxWorkers: 4,
    minWorkers: 1,
    
    // 池类型: 'threads'、'forks'、'vmThreads'
    pool: 'threads',
  },
})
```

## 并发测试

在文件内并行运行测试:

```ts
// 单个并发测试
test.concurrent('test 1', async ({ expect }) => {
  expect(await fetch1()).toBe('result')
})

test.concurrent('test 2', async ({ expect }) => {
  expect(await fetch2()).toBe('result')
})

// 套件中的所有测试并发
describe.concurrent('parallel suite', () => {
  test('test 1', async ({ expect }) => {})
  test('test 2', async ({ expect }) => {})
})
```

**重要:** 对并发测试使用 `{ expect }` 上下文。

## 并发上下文中的顺序

强制顺序执行:

```ts
describe.concurrent('mostly parallel', () => {
  test('parallel 1', async () => {})
  test('parallel 2', async () => {})
  
  test.sequential('must run alone 1', async () => {})
  test.sequential('must run alone 2', async () => {})
})

// 或整个套件
describe.sequential('sequential suite', () => {
  test('first', () => {})
  test('second', () => {})
})
```

## 最大并发

限制并发测试:

```ts
defineConfig({
  test: {
    maxConcurrency: 5, // 每个文件的最大并发测试数
  },
})
```

## 隔离

默认情况下,每个文件在隔离环境中运行:

```ts
defineConfig({
  test: {
    // 禁用隔离以加快运行速度(不太安全)
    isolate: false,
  },
})
```

## 分片

跨机器拆分测试:

```bash
# 机器 1
vitest run --shard=1/3

# 机器 2
vitest run --shard=2/3

# 机器 3
vitest run --shard=3/3
```

### CI 示例 (GitHub Actions)

```yaml
jobs:
  test:
    strategy:
      matrix:
        shard: [1, 2, 3]
    steps:
      - run: vitest run --shard=${{ matrix.shard }}/3 --reporter=blob
      
  merge:
    needs: test
    steps:
      - run: vitest --merge-reports --reporter=junit
```

### 合并报告

```bash
# 每个 shard 输出 blob
vitest run --shard=1/3 --reporter=blob --coverage
vitest run --shard=2/3 --reporter=blob --coverage

# 合并所有 blob
vitest --merge-reports --reporter=json --coverage
```

## 测试顺序

控制测试顺序:

```ts
defineConfig({
  test: {
    sequence: {
      // 以随机顺序运行测试
      shuffle: true,
      
      // 用于可重现随机化的种子
      seed: 12345,
      
      // 钩子执行顺序
      hooks: 'stack', // 'stack'、'list'、'parallel'
      
      // 默认情况下所有测试并发
      concurrent: true,
    },
  },
})
```

## 随机化测试

随机化以发现隐藏的依赖关系:

```ts
// 通过 CLI
vitest --sequence.shuffle

// 每个套件
describe.shuffle('random order', () => {
  test('test 1', () => {})
  test('test 2', () => {})
  test('test 3', () => {})
})
```

## 池选项

### 线程(默认)

```ts
defineConfig({
  test: {
    pool: 'threads',
    poolOptions: {
      threads: {
        maxThreads: 8,
        minThreads: 2,
        isolate: true,
      },
    },
  },
})
```

### Forks

更好的隔离,更慢:

```ts
defineConfig({
  test: {
    pool: 'forks',
    poolOptions: {
      forks: {
        maxForks: 4,
        isolate: true,
      },
    },
  },
})
```

### VM 线程

每个文件完全 VM 隔离:

```ts
defineConfig({
  test: {
    pool: 'vmThreads',
  },
})
```

## 失败时停止

第一次失败后停止:

```bash
vitest --bail 1    # 1 次失败后停止
vitest --bail      # 第一次失败时停止(与 --bail 1 相同)
```

## 关键点

- 文件默认并行运行
- 对文件内的并行测试使用 `.concurrent`
- 始终在并发测试中使用上下文的 `expect`
- 分片跨 CI 机器拆分测试
- 使用 `--merge-reports` 合并分片结果
- 随机化测试以发现隐藏的依赖关系

<!-- 
Source references:
- https://vitest.dev/guide/features.html#running-tests-concurrently
- https://vitest.dev/guide/improving-performance.html
-->
