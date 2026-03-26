# 生命周期钩子

使用生命周期钩子扩展构建过程。

## 概述

钩子提供了一种在构建生命周期的特定阶段注入自定义逻辑的方法。灵感来自 [unbuild](https://github.com/unjs/unbuild)。

**推荐：** 使用[插件](advanced-plugins.md)进行大多数扩展。使用钩子进行简单的自定义任务或 Rolldown 插件注入。

## 使用模式

### 对象语法

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  hooks: {
    'build:prepare': async (context) => {
      console.log('构建开始...')
    },
    'build:done': async (context) => {
      console.log('构建完成!')
    },
  },
})
```

### 函数语法

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  hooks(hooks) {
    hooks.hook('build:prepare', () => {
      console.log('准备构建...')
    })

    hooks.hook('build:before', (context) => {
      console.log(`构建格式: ${context.format}`)
    })
  },
})
```

## 可用的钩子

### `build:prepare`

在构建过程开始之前调用。

**何时：** 每个构建会话一次

**上下文：**
```ts
{
  options: ResolvedConfig,
  hooks: Hookable
}
```

**用例：**
- 设置任务
- 验证
- 环境准备

**示例：**
```ts
hooks: {
  'build:prepare': async (context) => {
    console.log('开始构建:', context.options.entry)
    await cleanOldFiles()
  },
}
```

### `build:before`

在每次 Rolldown 构建之前调用。

**何时：** 每种格式一次（ESM、CJS 等）

**上下文：**
```ts
{
  options: ResolvedConfig,
  buildOptions: BuildOptions,
  hooks: Hookable
}
```

**用例：**
- 每种格式修改构建选项
- 动态注入插件
- 特定格式设置

**示例：**
```ts
hooks: {
  'build:before': async (context) => {
    console.log(`构建 ${context.buildOptions.format} 格式...`)

    // 添加特定格式的插件
    if (context.buildOptions.format === 'iife') {
      context.buildOptions.plugins.push(browserPlugin())
    }
  },
}
```

### `build:done`

构建完成后调用。

**何时：** 每个构建会话一次

**上下文：**
```ts
{
  options: ResolvedConfig,
  chunks: RolldownChunk[],
  hooks: Hookable
}
```

**用例：**
- 后处理
- 资源复制
- 通知
- 部署

**示例：**
```ts
hooks: {
  'build:done': async (context) => {
    console.log(`构建了 ${context.chunks.length} 个块`)

    // 复制其他文件
    await copyAssets()

    // 发送通知
    notifyBuildComplete()
  },
}
```

## 常见模式

### 构建通知

```ts
export default defineConfig({
  hooks: {
    'build:prepare': () => {
      console.log('🚀 开始构建...')
    },
    'build:done': (context) => {
      const size = context.chunks.reduce((sum, c) => sum + c.code.length, 0)
      console.log(`✅ 构建完成! 总大小: ${size} 字节`)
    },
  },
})
```

### 条件插件注入

```ts
export default defineConfig({
  hooks(hooks) {
    hooks.hook('build:before', (context) => {
      // 仅在生产中添加压缩
      if (process.env.NODE_ENV === 'production') {
        context.buildOptions.plugins.push(minifyPlugin())
      }
    })
  },
})
```

### 自定义文件复制

```ts
import { copyFile } from 'fs/promises'

export default defineConfig({
  hooks: {
    'build:done': async (context) => {
      // 将 README 复制到 dist
      await copyFile('README.md', `${context.options.outDir}/README.md`)
    },
  },
})
```

### 构建指标

```ts
export default defineConfig({
  hooks: {
    'build:prepare': (context) => {
      context.startTime = Date.now()
    },
    'build:done': (context) => {
      const duration = Date.now() - context.startTime
      console.log(`构建耗时 ${duration}ms`)

      // 记录块大小
      context.chunks.forEach((chunk) => {
        console.log(`${chunk.fileName}: ${chunk.code.length} 字节`)
      })
    },
  },
})
```

### 特定格式逻辑

```ts
export default defineConfig({
  format: ['esm', 'cjs', 'iife'],
  hooks: {
    'build:before': (context) => {
      const format = context.buildOptions.format

      if (format === 'iife') {
        // 浏览器特定设置
        context.buildOptions.globalName = 'MyLib'
      } else if (format === 'cjs') {
        // Node 特定设置
        context.buildOptions.platform = 'node'
      }
    },
  },
})
```

### 部署钩子

```ts
export default defineConfig({
  hooks: {
    'build:done': async (context) => {
      if (process.env.DEPLOY === 'true') {
        console.log('部署到 CDN...')
        await deployToCDN(context.options.outDir)
      }
    },
  },
})
```

## 高级用法

### 多个钩子

```ts
export default defineConfig({
  hooks(hooks) {
    // 注册多个钩子
    hooks.hook('build:prepare', setupEnvironment)
    hooks.hook('build:prepare', validateConfig)

    hooks.hook('build:before', injectPlugins)
    hooks.hook('build:before', logFormat)

    hooks.hook('build:done', generateManifest)
    hooks.hook('build:done', notifyComplete)
  },
})
```

### 异步钩子

```ts
export default defineConfig({
  hooks: {
    'build:prepare': async (context) => {
      await fetchRemoteConfig()
      await initializeDatabase()
    },
    'build:done': async (context) => {
      await uploadToS3(context.chunks)
      await invalidateCDN()
    },
  },
})
```

### 错误处理

```ts
export default defineConfig({
  hooks: {
    'build:done': async (context) => {
      try {
        await riskyOperation()
      } catch (error) {
        console.error('钩子失败:', error)
        // 不要抛出 - 允许构建完成
      }
    },
  },
})
```

## Hookable API

tsdown 使用 [hookable](https://github.com/unjs/hookable) 进行钩子。其他方法：

```ts
export default defineConfig({
  hooks(hooks) {
    // 注册钩子
    hooks.hook('build:done', handler)

    // 注册钩子一次
    hooks.hookOnce('build:prepare', handler)

    // 移除钩子
    hooks.removeHook('build:done', handler)

    // 清除事件的所有钩子
    hooks.removeHooks('build:done')

    // 手动调用钩子
    await hooks.callHook('build:done', context)
  },
})
```

## 提示

1. **使用插件**进行大多数扩展
2. **钩子用于简单任务**，如通知或文件复制
3. **支持异步钩子**用于 I/O 操作
4. **不要抛出错误**，除非您想要失败构建
5. **上下文是可变的**在 `build:before` 中用于高级用例
6. **允许多个钩子**用于同一事件

## 故障排除

### 钩子未被调用

- 验证钩子名称正确
- 检查钩子是否在配置中注册
- 确保异步钩子被等待

### 钩子中构建失败

- 添加 try/catch 进行错误处理
- 除非有意为之，否则不要抛出
- 记录错误以进行调试

### 上下文未定义

- 检查您正在使用哪个钩子
- 验证该钩子可用的上下文属性

## 相关

- [插件](advanced-plugins.md) - 插件系统
- [Rolldown 选项](advanced-rolldown-options.md) - 构建选项
- [监视模式](option-watch-mode.md) - 开发工作流
