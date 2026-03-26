# 可执行文件 - `exe`

**[实验性]** 使用 [Node.js 单一可执行应用程序](https://nodejs.org/api/single-executable-applications.html) 打包为独立可执行文件。

## 要求

- Node.js >= 25.5.0（ESM 支持需要 >= 25.7.0）
- 不支持 Bun 或 Deno

## 基本用法

```ts
export default defineConfig({
  entry: ['src/cli.ts'],
  exe: true,
})
```

## 启用时的行为

- 默认输出格式从 `esm` 更改为 `cjs`（除非 Node.js >= 25.7.0）
- 默认禁用声明文件生成（`dts`）
- 禁用代码分割
- 仅支持单个入口点
- 抑制传统 CJS 警告

## 高级配置

```ts
export default defineConfig({
  entry: ['src/cli.ts'],
  exe: {
    fileName: 'my-tool',
    seaConfig: {
      disableExperimentalSEAWarning: true,
      useCodeCache: true,
      useSnapshot: false,
    },
  },
})
```

## `ExeOptions`

| 选项 | 类型 | 描述 |
|--------|------|-------------|
| `seaConfig` | `Omit<SeaConfig, 'main' \| 'output' \| 'mainFormat'>` | Node.js 配置选项 |
| `fileName` | `string \| ((chunk) => string)` | 自定义输出文件名（不带 `.exe` 或平台后缀）|
| `targets` | `ExeTarget[]` | 跨平台构建目标（需要 `@tsdown/exe`）|

## `SeaConfig`

请参阅 [Node.js 单一可执行应用程序文档](https://nodejs.org/api/single-executable-applications.html)。

| 选项 | 类型 | 默认值 | 描述 |
|--------|------|---------|-------------|
| `disableExperimentalSEAWarning` | `boolean` | `true` | 禁用实验性警告 |
| `useSnapshot` | `boolean` | `false` | 使用 V8 快照 |
| `useCodeCache` | `boolean` | `false` | 使用 V8 代码缓存 |
| `execArgv` | `string[]` | - | 额外的 Node.js 参数 |
| `execArgvExtension` | `'none' \| 'env' \| 'cli'` | `'env'` | 如何扩展 execArgv |
| `assets` | `Record<string, string>` | - | 要嵌入的资源 |

## 跨平台构建

安装 `@tsdown/exe` 以从单台机器为多个平台构建可执行文件：

```bash
pnpm add -D @tsdown/exe
```

```ts
export default defineConfig({
  entry: ['src/cli.ts'],
  exe: {
    targets: [
      { platform: 'linux', arch: 'x64', nodeVersion: '25.7.0' },
      { platform: 'darwin', arch: 'arm64', nodeVersion: '25.7.0' },
      { platform: 'win', arch: 'x64', nodeVersion: '25.7.0' },
    ],
  },
})
```

这会下载目标平台的 Node.js 二进制文件，在本地缓存它，并生成带平台后缀的输出：

```
dist/
  cli-linux-x64
  cli-darwin-arm64
  cli-win-x64.exe
```

### `ExeTarget`

| 字段 | 类型 | 描述 |
|-------|------|-------------|
| `platform` | `'win' \| 'darwin' \| 'linux'` | 目标操作系统（nodejs.org 命名）|
| `arch` | `'x64' \| 'arm64'` | 目标 CPU 架构 |
| `nodeVersion` | `string` | Node.js 版本（必须 `>=25.7.0`）|

### 缓存

下载的 Node.js 二进制文件缓存在系统缓存目录中：
- **macOS:** `~/Library/Caches/tsdown/node/`
- **Linux:** `~/.cache/tsdown/node/`
- **Windows:** `%LOCALAPPDATA%/tsdown/Caches/node/`

## 平台说明

- 在 macOS 上，可执行文件会自动进行代码签名（临时）以兼容 Gatekeeper
- 在 Windows 上，会自动附加 `.exe` 扩展名
- 指定 `targets` 时，会忽略 `seaConfig.executable`

## CLI

```bash
tsdown --exe
tsdown src/cli.ts --exe
```