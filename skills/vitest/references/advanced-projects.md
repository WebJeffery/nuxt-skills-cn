---
name: projects-workspaces
description: 用于 monorepos 和不同测试类型的多项目配置
---

# 项目

在同一个 Vitest 进程中运行不同的测试配置。

## 基本项目设置

```ts
// vitest.config.ts
defineConfig({
  test: {
    projects: [
      // 配置文件的 glob 模式
      'packages/*',
      
      // 内联配置
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

## Monorepo 模式

```ts
defineConfig({
  test: {
    projects: [
      // 每个包都有自己的 vitest.config.ts
      'packages/core',
      'packages/cli',
      'packages/utils',
    ],
  },
})
```

包配置:

```ts
// packages/core/vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    name: 'core',
    include: ['src/**/*.test.ts'],
    environment: 'node',
  },
})
```

## 不同环境

在不同环境中运行相同的测试:

```ts
defineConfig({
  test: {
    projects: [
      {
        test: {
          name: 'happy-dom',
          root: './shared-tests',
          environment: 'happy-dom',
          setupFiles: ['./setup.happy-dom.ts'],
        },
      },
      {
        test: {
          name: 'node',
          root: './shared-tests',
          environment: 'node',
          setupFiles: ['./setup.node.ts'],
        },
      },
    ],
  },
})
```

## 浏览器 + Node 项目

```ts
defineConfig({
  test: {
    projects: [
      {
        test: {
          name: 'unit',
          include: ['tests/unit/**/*.test.ts'],
          environment: 'node',
        },
      },
      {
        test: {
          name: 'browser',
          include: ['tests/browser/**/*.test.ts'],
          browser: {
            enabled: true,
            name: 'chromium',
            provider: 'playwright',
          },
        },
      },
    ],
  },
})
```

## 共享配置

```ts
// vitest.shared.ts
export const sharedConfig = {
  testTimeout: 10000,
  setupFiles: ['./tests/setup.ts'],
}

// vitest.config.ts
import { sharedConfig } from './vitest.shared'

defineConfig({
  test: {
    projects: [
      {
        test: {
          ...sharedConfig,
          name: 'unit',
          include: ['tests/unit/**/*.test.ts'],
        },
      },
      {
        test: {
          ...sharedConfig,
          name: 'e2e',
          include: ['tests/e2e/**/*.test.ts'],
        },
      },
    ],
  },
})
```

## 项目特定依赖

每个项目可以有不同的内联依赖:

```ts
defineConfig({
  test: {
    projects: [
      {
        test: {
          name: 'project-a',
          server: {
            deps: {
              inline: ['package-a'],
            },
          },
        },
      },
    ],
  },
})
```

## 运行特定项目

```bash
# 运行特定项目
vitest --project unit
vitest --project integration

# 多个项目
vitest --project unit --project e2e

# 排除项目
vitest --project.ignore browser
```

## 向项目提供值

从配置共享值到测试:

```ts
// vitest.config.ts
defineConfig({
  test: {
    projects: [
      {
        test: {
          name: 'staging',
          provide: {
            apiUrl: 'https://staging.api.com',
            debug: true,
          },
        },
      },
      {
        test: {
          name: 'production',
          provide: {
            apiUrl: 'https://api.com',
            debug: false,
          },
        },
      },
    ],
  },
})

// 在测试中,使用 inject
import { inject } from 'vitest'

test('uses correct api', () => {
  const url = inject('apiUrl')
  expect(url).toContain('api.com')
})
```

## 使用 Fixtures

```ts
const test = base.extend({
  apiUrl: ['/default', { injected: true }],
})

test('uses injected url', ({ apiUrl }) => {
  // apiUrl 来自项目的 provide 配置
})
```

## 项目隔离

默认情况下,每个项目在自己的线程池中运行:

```ts
defineConfig({
  test: {
    projects: [
      {
        test: {
          name: 'isolated',
          isolate: true, // 完全隔离
          pool: 'forks',
        },
      },
    ],
  },
})
```

## 每项目全局设置

```ts
defineConfig({
  test: {
    projects: [
      {
        test: {
          name: 'with-db',
          globalSetup: ['./tests/db-setup.ts'],
        },
      },
    ],
  },
})
```

## 关键点

- 项目在同一个 Vitest 进程中运行
- 每个项目可以有不同的环境、配置
- 使用 glob 模式进行 monorepo 包
- 使用 `--project` 标志运行特定项目
- 使用 `provide` 将配置值注入到测试中
- 项目继承根配置,除非被覆盖

<!-- 
Source references:
- https://vitest.dev/guide/projects.html
-->
