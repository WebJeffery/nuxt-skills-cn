# CLI 参考

tsdown 命令行界面的完整参考。

## 概述

所有 CLI 标志也可以在配置文件中设置。CLI 标志覆盖配置文件选项。

## 标志模式

CLI 标志映射规则：
- `--foo` 设置 `foo: true`
- `--no-foo` 设置 `foo: false`
- `--foo.bar` 设置 `foo: { bar: true }`
- `--format esm --format cjs` 设置 `format: ['esm', 'cjs']`

CLI 标志支持 camelCase 和 kebab-case。例如，`--outDir` 和 `--out-dir` 是等效的。

## 基本命令

### 构建

```bash
# 使用默认配置构建
tsdown

# 构建特定文件
tsdown src/index.ts src/cli.ts

# 使用监视模式构建
tsdown --watch
```

## 配置

### `--config, -c <filename>`

指定自定义配置文件：

```bash
tsdown --config build.config.ts
tsdown -c custom-config.js
```

### `--no-config`

禁用配置文件加载：

```bash
tsdown --no-config src/index.ts
```

### `--config-loader <loader>`

选择配置加载器（`auto`、`native`、`unrun`）：

```bash
tsdown --config-loader unrun
```

### `--tsconfig <file>`

指定 TypeScript 配置文件：

```bash
tsdown --tsconfig tsconfig.build.json
```

## 入口点

### `[...files]`

将入口文件指定为参数：

```bash
tsdown src/index.ts src/utils.ts
```

## 输出选项

### `--format <format>`

输出格式（`esm`、`cjs`、`iife`、`umd`）：

```bash
tsdown --format esm
tsdown --format esm --format cjs
```

### `--out-dir, -d <dir>`

输出目录：

```bash
tsdown --out-dir lib
tsdown -d dist
```

### `--dts`

生成 TypeScript 声明：

```bash
tsdown --dts
```

### `--clean`

构建前清理输出目录：

```bash
tsdown --clean
```

## 构建选项

### `--target <target>`

JavaScript 目标版本：

```bash
tsdown --target es2020
tsdown --target node18
tsdown --target chrome100
tsdown --no-target  # 禁用转换
```

### `--platform <platform>`

目标平台（`node`、`browser`、`neutral`）：

```bash
tsdown --platform node
tsdown --platform browser
```

### `--minify`

启用压缩：

```bash
tsdown --minify
tsdown --no-minify
```

### `--sourcemap`

生成源映射：

```bash
tsdown --sourcemap
tsdown --sourcemap inline
```

### `--treeshake`

启用/禁用 tree shaking：

```bash
tsdown --treeshake
tsdown --no-treeshake
```

## 依赖

### `--deps.never-bundle <module>`

将模块标记为外部（不打包）：

```bash
tsdown --deps.never-bundle react --deps.never-bundle react-dom
```

### `--deps.skip-node-modules-bundle`

跳过解析和打包所有 node_modules：

```bash
tsdown --deps.skip-node-modules-bundle
```

### `--shims`

添加 ESM/CJS 兼容性垫片：

```bash
tsdown --shims
```

## 开发

### `--watch, -w [path]`

启用监视模式：

```bash
tsdown --watch
tsdown -w
tsdown --watch src  # 监视特定目录
```

### `--ignore-watch <path>`

在监视模式下忽略路径：

```bash
tsdown --watch --ignore-watch test
```

### `--on-success <command>`

成功构建后运行命令：

```bash
tsdown --watch --on-success "echo Build complete!"
```

## 环境变量

### `--env.* <value>`

设置编译时环境变量：

```bash
tsdown --env.NODE_ENV=production --env.API_URL=https://api.example.com
```

作为 `import.meta.env.*` 或 `process.env.*` 访问。

### `--env-file <file>`

从文件加载环境变量：

```bash
tsdown --env-file .env.production
```

### `--env-prefix <prefix>`

按前缀过滤环境变量（默认：`TSDOWN_`）：

```bash
tsdown --env-file .env --env-prefix APP_ --env-prefix TSDOWN_
```

## 资源

### `--copy <dir>`

将目录复制到输出：

```bash
tsdown --copy public
tsdown --copy assets --copy static
```

## 可执行文件

### `--exe`

**[实验性]** 使用 [Node.js 单一可执行应用程序](https://nodejs.org/api/single-executable-applications.html) 打包为独立可执行文件。需要 Node.js >= 25.5.0，不支持 Bun 或 Deno。通过 `@tsdown/exe` 支持跨平台构建。

```bash
tsdown --exe
```

启用时：
- 默认格式更改为 `cjs`（除非 Node.js >= 25.7.0）
- 默认禁用声明文件生成（`dts`）
- 禁用代码分割
- 仅支持单个入口点

有关高级配置和跨平台构建，请参阅[可执行文件](option-exe.md)。

## 包管理

### `--exports`

自动生成 package.json exports 字段：

```bash
tsdown --exports
```

### `--publint`

启用包验证：

```bash
tsdown --publint
```

### `--attw`

启用 "Are you types wrong" 验证：

```bash
tsdown --attw
```

### `--unused`

检查未使用的依赖：

```bash
tsdown --unused
```

## 日志记录

### `--log-level <level>`

设置日志详细程度（`silent`、`error`、`warn`、`info`）：

```bash
tsdown --log-level error
tsdown --log-level warn
```

### `--report` / `--no-report`

启用/禁用构建报告：

```bash
tsdown --no-report  # 禁用大小报告
tsdown --report     # 启用（默认）
```

### `--debug [feat]`

显示调试日志：

```bash
tsdown --debug
tsdown --debug rolldown  # 调试特定功能
```

## 集成

### `--from-vite [vitest]`

扩展 Vite 或 Vitest 配置：

```bash
tsdown --from-vite         # 使用 vite.config.*
tsdown --from-vite vitest  # 使用 vitest.config.*
```

## 工作区 / Monorepo

### `--workspace, -W [dir]`

启用工作区模式以构建多个包：

```bash
tsdown -W
tsdown -W packages/
```

### `--filter, -F <pattern>`

按名称或工作目录过滤配置。支持正则表达式：

```bash
tsdown -W -F my-package
tsdown -W -F /^pkg-/
```

### `--unbundle`

启用 unbundle（无包）模式：

```bash
tsdown --unbundle
```

### `--root <dir>`

指定输入文件的根目录（类似于 TypeScript 的 `rootDir`）。通过确定入口文件路径如何映射到输出路径来控制输出目录结构。默认为所有入口文件的公共基本目录。

```bash
tsdown --root src
tsdown --root .
```

### `--fail-on-warn`

在警告时失败（默认启用）：

```bash
tsdown --no-fail-on-warn  # 禁用
```

## 常见使用模式

### 基本构建

```bash
tsdown
```

### 库（ESM + CJS + 类型）

```bash
tsdown --format esm --format cjs --dts --clean
```

### 生产构建

```bash
tsdown --minify --clean --no-report
```

### 开发（监视）

```bash
tsdown --watch --sourcemap
```

### 浏览器包（IIFE）

```bash
tsdown --format iife --platform browser --minify
```

### Node.js CLI 工具

```bash
tsdown --format esm --platform node --shims
```

### 独立可执行文件

```bash
tsdown src/cli.ts --exe
```

### Monorepo 包

```bash
tsdown --clean --dts --exports --publint
```

### 使用环境变量

```bash
tsdown --env-file .env.production --env.BUILD_TIME=$(date +%s)
```

### 复制资源

```bash
tsdown --copy public --copy assets --clean
```

## 提示

1. **使用配置文件**进行复杂设置
2. **CLI 标志覆盖**配置文件选项
3. **链接多个格式**以进行多目标构建
4. **使用 --clean**以避免过时的文件
5. **启用 --dts**用于 TypeScript 库
6. **使用 --watch**在开发期间
7. **添加 --on-success**用于构建后任务
8. **使用 --exports**自动生成 package.json 字段

## 相关文档

- [配置文件](option-config-file.md) - 配置文件选项
- [入口](option-entry.md) - 入口点配置
- [输出格式](option-output-format.md) - 格式选项
- [监视模式](option-watch-mode.md) - 监视模式详细信息