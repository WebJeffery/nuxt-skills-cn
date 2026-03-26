# 日志级别

控制构建输出的详细程度。

## 概述

`logLevel` 选项控制 tsdown 在构建过程中显示多少信息。

## 类型

```ts
logLevel?: 'silent' | 'error' | 'warn' | 'info'
```

**默认：** `'info'`

## 基本用法

### CLI

```bash
# 抑制所有输出
tsdown --log-level silent

# 仅显示错误
tsdown --log-level error

# 显示警告和错误
tsdown --log-level warn

# 显示所有信息（默认）
tsdown --log-level info
```

### 配置文件

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  logLevel: 'error',
})
```

## 可用级别

| 级别 | 显示 | 用例 |
|-------|-------|----------|
| `silent` | 无 | CI/CD 管道、脚本 |
| `error` | 仅错误 | 最小输出 |
| `warn` | 警告 + 错误 | 标准 CI/CD |
| `info` | 所有消息 | 开发（默认）|

## 常见模式

### CI/CD 管道

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  logLevel: 'error',  // 在 CI 中仅显示错误
})
```

### 脚本

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  logLevel: 'silent',  // 自动化无输出
})
```

## 警告时失败

`failOnWarn` 选项控制警告是否导致构建以非零代码退出。默认为 `false` — 警告永远不会导致构建失败。

```ts
export default defineConfig({
  failOnWarn: false,     // 默认：警告从不失败
  // failOnWarn: true,   // 警告始终失败
  // failOnWarn: 'ci-only', // 仅在 CI 中警告失败
})
```

请参阅 [CI 环境](advanced-ci.md) 了解有关 CI 感知选项的更多信息。

## 相关选项

- [CI 环境](advanced-ci.md) - CI 感知选项详细信息
- [CLI 参考](reference-cli.md) - 所有 CLI 选项
- [配置文件](option-config-file.md) - 配置设置
