---
name: code-coverage
description: 使用 V8 或 Istanbul 提供程序的代码覆盖率
---

# 代码覆盖率

## 设置

```bash
# 使用覆盖率运行测试
vitest run --coverage
```

## 配置

```ts
// vitest.config.ts
defineConfig({
  test: {
    coverage: {
      // 提供程序: 'v8'(默认,更快)或 'istanbul'(更兼容)
      provider: 'v8',
      
      // 启用覆盖率
      enabled: true,
      
      // 报告器
      reporter: ['text', 'json', 'html'],
      
      // 包含的文件
      include: ['src/**/*.{ts,tsx}'],
      
      // 排除的文件
      exclude: [
        'node_modules/',
        'tests/',
        '**/*.d.ts',
        '**/*.test.ts',
      ],
      
      // 报告未覆盖的文件
      all: true,
      
      // 阈值
      thresholds: {
        lines: 80,
        functions: 80,
        branches: 80,
        statements: 80,
      },
    },
  },
})
```

## 提供程序

### V8 (默认)

```bash
npm i -D @vitest/coverage-v8
```

- 更快,无需预插桩
- 使用 V8 的原生覆盖率
- 推荐用于大多数项目

### Istanbul

```bash
npm i -D @vitest/coverage-istanbul
```

- 预插桩代码
- 在任何 JS 运行时中工作
- 开销更大但广泛兼容

## 报告器

```ts
coverage: {
  reporter: [
    'text',           // 终端输出
    'text-summary',   // 仅摘要
    'json',           // JSON 文件
    'html',           // HTML 报告
    'lcov',           // 用于 CI 工具
    'cobertura',      // XML 格式
  ],
  reportsDirectory: './coverage',
}
```

## 阈值

如果覆盖率低于阈值,则测试失败:

```ts
coverage: {
  thresholds: {
    // 全局阈值
    lines: 80,
    functions: 75,
    branches: 70,
    statements: 80,
    
    // 每文件阈值
    perFile: true,
    
    // 自动更新阈值(用于逐步改进)
    autoUpdate: true,
  },
}
```

## 忽略代码

### V8

```ts
/* v8 ignore next -- @preserve */
function ignored() {
  return 'not covered'
}

/* v8 ignore start -- @preserve */
// 此处的所有代码都被忽略
/* v8 ignore stop -- @preserve */
```

### Istanbul

```ts
/* istanbul ignore next -- @preserve */
function ignored() {}

/* istanbul ignore if -- @preserve */
if (condition) {
  // 被忽略
}
```

注意: `@preserve` 使注释通过 esbuild。

## Package.json 脚本

```json
{
  "scripts": {
    "test": "vitest",
    "test:coverage": "vitest run --coverage",
    "test:coverage:watch": "vitest --coverage"
  }
}
```

## Vitest UI 覆盖率

在 Vitest UI 中启用 HTML 覆盖率:

```ts
coverage: {
  enabled: true,
  reporter: ['text', 'html'],
}
```

使用 `vitest --ui` 运行以可视化查看覆盖率。

## CI 集成

```yaml
# GitHub Actions
- name: Run tests with coverage
  run: npm run test:coverage

- name: Upload coverage to Codecov
  uses: codecov/codecov-action@v3
  with:
    files: ./coverage/lcov.info
```

## 分片覆盖率

合并分片运行的覆盖率:

```bash
vitest run --shard=1/3 --coverage --reporter=blob
vitest run --shard=2/3 --coverage --reporter=blob
vitest run --shard=3/3 --coverage --reporter=blob

vitest --merge-reports --coverage --reporter=json
```

## 关键点

- V8 更快,Istanbul 更兼容
- 使用 `--coverage` 标志或 `coverage.enabled: true`
- 包含 `all: true` 以查看未覆盖的文件
- 设置阈值以强制最低覆盖率
- 使用 `@preserve` 注释保留忽略提示

<!-- 
Source references:
- https://vitest.dev/guide/coverage.html
-->
