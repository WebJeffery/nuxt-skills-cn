---
name: vitest-configuration
description: 使用 vite.config.ts 或 vitest.config.ts 配置 Vitest
---

# 配置

Vitest 从 `vitest.config.ts` 或 `vite.config.ts` 读取配置。它与 Vite 共享相同的配置格式。

## 基本设置

```ts
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    // 测试选项
  },
})
```

## 与现有 Vite 配置一起使用

添加 Vitest 类型引用并使用 `test` 属性:

```ts
// vite.config.ts
/// <reference types="vitest/config" />
import { defineConfig } from 'vite'

export default defineConfig({
  test: {
    globals: true,
    environment: 'jsdom',
  },
})
```

## 合并配置

如果您有单独的配置文件,使用 `mergeConfig`:

```ts
// vitest.config.ts
import { defineConfig, mergeConfig } from 'vitest/config'
import viteConfig from './vite.config'

export default mergeConfig(viteConfig, defineConfig({
  test: {
    environment: 'jsdom',
  },
}))
```

## 常用选项

```ts
defineConfig({
  test: {
    // 启用全局 API(describe, it, expect)而无需导入
    globals: true,
    
    // 测试环境: 'node', 'jsdom', 'happy-dom'
    environment: 'node',
    
    // 在每个测试文件之前运行的设置文件
    setupFiles: ['./tests/setup.ts'],
    
    // 测试文件的包含模式
    include: ['**/*.{test,spec}.{js,ts,jsx,tsx}'],
    
    // 排除模式
    exclude: ['**/node_modules/**', '**/dist/**'],
    
    // 测试超时(毫秒)
    testTimeout: 5000,
    
    // 钩子超时(毫秒)
    hookTimeout: 10000,
    
    // 默认启用监视模式
    watch: true,
    
    // 覆盖率配置
    coverage: {
      provider: 'v8', // 或 'istanbul'
      reporter: ['text', 'html'],
      include: ['src/**/*.ts'],
    },
    
    // 在隔离中运行测试(每个文件在单独的进程中)
    isolate: true,
    
    // 用于运行测试的池: 'threads', 'forks', 'vmThreads'
    pool: 'threads',
    
    // 线程/进程数
    poolOptions: {
      threads: {
        maxThreads: 4,
        minThreads: 1,
      },
    },
    
    // 在测试之间自动清除模拟
    clearMocks: true,
    
    // 在测试之间恢复模拟
    restoreMocks: true,
    
    // 重试失败的测试
    retry: 0,
    
    // 第一次失败后停止
    bail: 0,
  },
})
```

## 条件配置

使用 `mode` 或 `process.env.VITEST` 进行特定测试的配置:

```ts
export default defineConfig(({ mode }) => ({
  plugins: mode === 'test' ? [] : [myPlugin()],
  test: {
    // 测试选项
  },
}))
```

## 项目 (Monorepos)

在同一个 Vitest 进程中运行不同的配置:

```ts
defineConfig({
  test: {
    projects: [
      'packages/*',
      {
        test: {
          name: 'unit',
          include: ['tests/unit/**/*.test.ts'],
          environment: 'node',
        },
      },
      {
        test: {
          name: 'integration',
          include: ['tests/integration/**/*.test.ts'],
          environment: 'jsdom',
        },
      },
    ],
  },
})
```

## 关键点

- Vitest 使用 Vite 的转换管道 - 相同的 `resolve.alias`、插件有效
- `vitest.config.ts` 优先于 `vite.config.ts`
- 使用 `--config` 标志指定自定义配置路径
- 运行测试时 `process.env.VITEST` 被设置为 `true`
- 测试配置使用 `test` 属性,其余是 Vite 配置

<!-- 
Source references:
- https://vitest.dev/guide/#configuring-vitest
- https://vitest.dev/config/
-->
