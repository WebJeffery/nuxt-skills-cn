# 编程用法

从 JavaScript/TypeScript 代码中使用 tsdown。

## 概述

tsdown 可以在您的 Node.js 脚本、自定义构建工具或自动化工作流中导入并以编程方式使用。

## 基本用法

### 简单构建

```ts
import { build } from 'tsdown'

await build({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
})
```

### 带选项

```ts
import { build } from 'tsdown'

await build({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  outDir: 'dist',
  dts: true,
  minify: true,
  sourcemap: true,
  clean: true,
})
```

## API 参考

### build()

运行构建的主要函数。

```ts
import { build } from 'tsdown'

await build(options)
```

**参数：**
- `options` - 构建配置对象（与配置文件相同）

**返回：**
- `Promise<void>` - 构建完成时解析

**抛出：**
- 如果编译失败则抛出构建错误

## 配置对象

所有配置文件选项都可用：

```ts
import { build, defineConfig } from 'tsdown'

const config = defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
  minify: true,
  sourcemap: true,
  deps: {
    neverBundle: ['react', 'react-dom'],
  },
  plugins: [/* 插件 */],
  hooks: {
    'build:done': async () => {
      console.log('构建完成!')
    },
  },
})

await build(config)
```

所有选项请参阅[配置参考](option-config-file.md)。

## 常见模式

### 自定义构建脚本

```ts
// scripts/build.ts
import { build } from 'tsdown'

async function main() {
  console.log('构建库...')

  await build({
    entry: ['src/index.ts'],
    format: ['esm', 'cjs'],
    dts: true,
    clean: true,
  })

  console.log('构建完成!')
}

main().catch(console.error)
```

运行：
```bash
tsx scripts/build.ts
```

### 多次构建

```ts
import { build } from 'tsdown'

// 构建主库
await build({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  outDir: 'dist',
  dts: true,
})

// 构建 CLI 工具
await build({
  entry: ['src/cli.ts'],
  format: ['esm'],
  outDir: 'dist/bin',
  platform: 'node',
  shims: true,
})
```

### 条件构建

```ts
import { build } from 'tsdown'

const isDev = process.env.NODE_ENV === 'development'

await build({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  minify: !isDev,
  sourcemap: isDev,
  clean: !isDev,
})
```

### 带错误处理

```ts
import { build } from 'tsdown'

try {
  await build({
    entry: ['src/index.ts'],
    format: ['esm', 'cjs'],
    dts: true,
  })
  console.log('✅ 构建成功')
} catch (error) {
  console.error('❌ 构建失败:', error)
  process.exit(1)
}
```

### 自动化工作流

```ts
import { build } from 'tsdown'
import { execSync } from 'child_process'

async function release() {
  // 清理
  console.log('清理...')
  execSync('rm -rf dist')

  // 构建
  console.log('构建...')
  await build({
    entry: ['src/index.ts'],
    format: ['esm', 'cjs'],
    dts: true,
    minify: true,
  })

  // 测试
  console.log('测试...')
  execSync('npm test')

  // 发布
  console.log('发布...')
  execSync('npm publish')
}

release().catch(console.error)
```

### 带后处理的构建

```ts
import { build } from 'tsdown'
import { copyFileSync } from 'fs'

await build({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
  hooks: {
    'build:done': async () => {
      // 复制其他文件
      copyFileSync('README.md', 'dist/README.md')
      copyFileSync('LICENSE', 'dist/LICENSE')
      console.log('复制了其他文件')
    },
  },
})
```

## 监视模式

遗憾的是，监视模式未在编程 API 中直接公开。使用 CLI 进行监视模式：

```ts
// 使用 CLI 进行监视模式
import { spawn } from 'child_process'

spawn('tsdown', ['--watch'], {
  stdio: 'inherit',
  shell: true,
})
```

## 集成示例

### 与任务运行器

```ts
// gulpfile.js
import { build } from 'tsdown'
import gulp from 'gulp'

gulp.task('build', async () => {
  await build({
    entry: ['src/index.ts'],
    format: ['esm', 'cjs'],
    dts: true,
  })
})

gulp.task('watch', () => {
  return gulp.watch('src/**/*.ts', gulp.series('build'))
})
```

### 与自定义 CLI

```ts
// scripts/cli.ts
import { build } from 'tsdown'
import { Command } from 'commander'

const program = new Command()

program
  .command('build')
  .option('--prod', '生产构建')
  .action(async (options) => {
    await build({
      entry: ['src/index.ts'],
      format: ['esm', 'cjs'],
      minify: options.prod,
      sourcemap: !options.prod,
    })
  })

program.parse()
```

### 与 CI/CD

```ts
// .github/scripts/build.ts
import { build } from 'tsdown'

const isCI = process.env.CI === 'true'

await build({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
  minify: isCI,
  clean: true,
})

// 上传到工件存储
if (isCI) {
  // 将 dist/ 上传到 S3 等
}
```

## TypeScript 支持

```ts
// scripts/build.ts
import { build, type UserConfig } from 'tsdown'

const config: UserConfig = {
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
}

await build(config)
```

## 提示

1. **使用 TypeScript**以获得类型安全
2. **正确处理错误**
3. **使用钩子**进行自定义逻辑
4. **记录进度**以获得可见性
5. **使用 CLI 进行监视**模式
6. **脚本中出错时退出**

## 故障排除

### 导入错误

确保已安装 tsdown：
```bash
pnpm add -D tsdown
```

### 类型错误

导入类型：
```ts
import type { UserConfig } from 'tsdown'
```

### 构建静默失败

添加错误处理：
```ts
try {
  await build(config)
} catch (error) {
  console.error(error)
  process.exit(1)
}
```

### 选项不工作

检查拼写和类型：
```ts
// ✅ 正确
{ format: ['esm', 'cjs'] }

// ❌ 错误
{ formats: ['esm', 'cjs'] }
```

## 相关

- [配置文件](option-config-file.md) - 配置选项
- [Hooks](advanced-hooks.md) - 生命周期钩子
- [CLI](reference-cli.md) - 命令行界面
- [插件](advanced-plugins.md) - 插件系统
