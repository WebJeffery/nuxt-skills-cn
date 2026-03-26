# CI 环境支持

自动检测 CI 环境并根据本地与 CI 构建切换功能。

## 概述

tsdown 使用 [`is-in-ci`](https://www.npmjs.com/package/is-in-ci) 包来检测 CI 环境。这涵盖了 GitHub Actions、GitLab CI、Jenkins、CircleCI、Travis CI 等。

## CI 感知值

几个选项接受 CI 感知字符串值：

| 值 | 行为 |
|-------|----------|
| `true` | 始终启用 |
| `false` | 始终禁用 |
| `'ci-only'` | 仅在 CI 中启用，本地禁用 |
| `'local-only'` | 仅在本地启用，在 CI 中禁用 |

## 支持的选项

这些选项接受 CI 感知值：

- `dts` - TypeScript 声明文件生成
- `publint` - 包 lint 验证
- `attw` - "Are you types wrong" 验证
- `report` - 包大小报告
- `exports` - 自动生成 `package.json` exports
- `unused` - 未使用的依赖检查
- `devtools` - DevTools 集成
- `failOnWarn` - 在警告时失败（默认为 `false`）

## 使用

### 字符串形式

```ts
export default defineConfig({
  dts: 'local-only',        // 在 CI 中跳过 DTS 以加快构建
  publint: 'ci-only',       // 仅在 CI 中运行 publint
  failOnWarn: 'ci-only',    // 仅在 CI 中在警告时失败（可选）
})
```

### 对象形式

当选项接受配置对象时，将 `enabled` 设置为 CI 感知值：

```ts
export default defineConfig({
  publint: {
    enabled: 'ci-only',
    level: 'error',
  },
  attw: {
    enabled: 'ci-only',
    profile: 'node16',
  },
})
```

### 配置函数

配置函数在其上下文中接收一个 `ci` 布尔值：

```ts
export default defineConfig((_, { ci }) => ({
  minify: ci,
  sourcemap: !ci,
}))
```

## 典型 CI 配置

```ts
export default defineConfig({
  entry: 'src/index.ts',
  format: ['esm', 'cjs'],
  dts: true,
  failOnWarn: 'ci-only',
  publint: 'ci-only',
  attw: 'ci-only',
})
```

## 相关选项

- [包验证](option-lint.md) - publint 和 attw 配置
- [日志级别](option-log-level.md) - `failOnWarn` 选项详细信息