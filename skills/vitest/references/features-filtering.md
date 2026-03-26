---
name: test-filtering
description: 按名称、文件模式和标签过滤测试
---

# 测试过滤

## CLI 过滤

### 按文件路径

```bash
# 运行包含 "user" 的文件
vitest user

# 多个模式
vitest user auth

# 特定文件
vitest src/user.test.ts

# 按行号
vitest src/user.test.ts:25
```

### 按测试名称

```bash
# 匹配模式的测试
vitest -t "login"
vitest --testNamePattern "should.*work"

# 正则表达式模式
vitest -t "/user|auth/"
```

## 更改的文件

```bash
# 未提交的更改
vitest --changed

# 自特定提交以来
vitest --changed HEAD~1
vitest --changed abc123

# 自分支以来
vitest --changed origin/main
```

## 相关文件

运行导入特定文件的测试:

```bash
vitest related src/utils.ts src/api.ts --run
```

与 lint-staged 一起使用:

```js
// .lintstagedrc.js
export default {
  '*.{ts,tsx}': 'vitest related --run',
}
```

## 聚焦测试 (.only)

```ts
test.only('only this runs', () => {})

describe.only('only this suite', () => {
  test('runs', () => {})
})
```

在 CI 中,`.only` 抛出错误,除非配置:

```ts
defineConfig({
  test: {
    allowOnly: true, // 在 CI 中允许 .only
  },
})
```

## 跳过测试

```ts
test.skip('skipped', () => {})

// 条件
test.skipIf(process.env.CI)('not in CI', () => {})
test.runIf(!process.env.CI)('local only', () => {})

// 动态跳过
test('dynamic', ({ skip }) => {
  skip(someCondition, 'reason')
})
```

## 标签

按自定义标签过滤:

```ts
test('database test', { tags: ['db'] }, () => {})
test('slow test', { tags: ['slow', 'integration'] }, () => {})
```

运行带标签的测试:

```bash
vitest --tags db
vitest --tags "db,slow"      # OR
vitest --tags db --tags slow # OR
```

配置允许的标签:

```ts
defineConfig({
  test: {
    tags: ['db', 'slow', 'integration'],
    strictTags: true, // 在未知标签上失败
  },
})
```

## 包含/排除模式

```ts
defineConfig({
  test: {
    // 测试文件模式
    include: ['**/*.{test,spec}.{ts,tsx}'],
    
    // 排除模式
    exclude: [
      '**/node_modules/**',
      '**/e2e/**',
      '**/*.skip.test.ts',
    ],
    
    // 包含源代码进行源内测试
    includeSource: ['src/**/*.ts'],
  },
})
```

## 监视模式过滤

在监视模式下,按:
- `p` - 按文件名模式过滤
- `t` - 按测试名模式过滤
- `a` - 运行所有测试
- `f` - 仅运行失败的测试

## 项目过滤

运行特定项目:

```bash
vitest --project unit
vitest --project integration --project e2e
```

## 基于环境的过滤

```ts
const isDev = process.env.NODE_ENV === 'development'
const isCI = process.env.CI

describe.skipIf(isCI)('local only tests', () => {})
describe.runIf(isDev)('dev tests', () => {})
```

## 组合过滤器

```bash
# 文件模式 + 测试名称 + 更改
vitest user -t "login" --changed

# 相关文件 + 运行模式
vitest related src/auth.ts --run
```

## 列出测试而不运行

```bash
vitest list                 # 显示所有测试名称
vitest list -t "user"       # 按名称过滤
vitest list --filesOnly     # 仅显示文件路径
vitest list --json          # JSON 输出
```

## 关键点

- 使用 `-t` 进行测试名称模式过滤
- `--changed` 仅运行受更改影响的测试
- `--related` 运行导入特定文件的测试
- 标签提供语义测试分组
- 使用 `.only` 进行调试,但配置 CI 拒绝它
- 监视模式具有交互式过滤

<!-- 
Source references:
- https://vitest.dev/guide/filtering.html
- https://vitest.dev/guide/cli.html
-->
