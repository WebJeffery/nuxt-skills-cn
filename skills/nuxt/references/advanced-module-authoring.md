---
name: module-authoring
description: 创建可发布 Nuxt 模块的完整指南，包含最佳实践
---

# 模块开发

本指南介绍如何创建具有正确结构、类型安全和最佳实践的可发布 Nuxt 模块。

## 模块结构

可发布模块的推荐结构：

```
my-nuxt-module/
├── src/
│   ├── module.ts          # 模块入口
│   └── runtime/
│       ├── components/    # Vue 组件
│       ├── composables/   # 组合式函数
│       ├── plugins/       # Nuxt 插件
│       └── server/        # 服务器处理程序
├── playground/            # 开发应用
├── package.json
└── tsconfig.json
```

## 模块定义

### 基本模块与类型安全选项

```ts
// src/module.ts
import { defineNuxtModule, createResolver, addPlugin, addComponent, addImports } from '@nuxt/kit'

export interface ModuleOptions {
  prefix?: string
  apiKey: string
  enabled?: boolean
}

export default defineNuxtModule<ModuleOptions>({
  meta: {
    name: 'my-module',
    configKey: 'myModule',
    compatibility: {
      nuxt: '>=3.0.0',
    },
  },
  defaults: {
    prefix: 'My',
    enabled: true,
  },
  setup(options, nuxt) {
    if (!options.enabled) return

    const { resolve } = createResolver(import.meta.url)

    // 模块设置逻辑在这里
  },
})
```

### 使用 `.with()` 进行严格类型推断

当需要 TypeScript 推断默认值始终存在时：

```ts
import { defineNuxtModule } from '@nuxt/kit'

interface ModuleOptions {
  apiKey: string
  baseURL: string
  timeout?: number
}

export default defineNuxtModule<ModuleOptions>().with({
  meta: {
    name: '@nuxtjs/my-api',
    configKey: 'myApi',
  },
  defaults: {
    baseURL: 'https://api.example.com',
    timeout: 5000,
  },
  setup(resolvedOptions, nuxt) {
    // resolvedOptions.baseURL 保证是 string（不是 undefined）
    // resolvedOptions.timeout 保证是 number（不是 undefined）
  },
})
```

## 添加运行时资源

### 组件

```ts
import { addComponent, addComponentsDir, createResolver } from '@nuxt/kit'

export default defineNuxtModule({
  setup() {
    const { resolve } = createResolver(import.meta.url)

    // 单个组件
    addComponent({
      name: 'MyButton',
      filePath: resolve('./runtime/components/MyButton.vue'),
    })

    // 带前缀的组件目录
    addComponentsDir({
      path: resolve('./runtime/components'),
      prefix: 'My',
      pathPrefix: false,
    })
  },
})
```

### 组合式函数和自动导入

```ts
import { addImports, addImportsDir, createResolver } from '@nuxt/kit'

export default defineNuxtModule({
  setup() {
    const { resolve } = createResolver(import.meta.url)

    // 单个导入
    addImports({
      name: 'useMyUtil',
      from: resolve('./runtime/composables/useMyUtil'),
    })

    // 组合式函数目录
    addImportsDir(resolve('./runtime/composables'))
  },
})
```

### 插件

```ts
import { addPlugin, addPluginTemplate, createResolver } from '@nuxt/kit'

export default defineNuxtModule({
  setup(options) {
    const { resolve } = createResolver(import.meta.url)

    // 静态插件文件
    addPlugin({
      src: resolve('./runtime/plugins/myPlugin'),
      mode: 'client', // 'client'、'server' 或 'all'
    })

    // 带生成代码的动态插件
    addPluginTemplate({
      filename: 'my-module-plugin.mjs',
      getContents: () => `
import { defineNuxtPlugin } from '#app/nuxt'

export default defineNuxtPlugin({
  name: 'my-module',
  setup() {
    const config = ${JSON.stringify(options)}
    // 插件逻辑
  }
})`,
    })
  },
})
```

## 服务器扩展

### 服务器处理程序

```ts
import { addServerHandler, addServerScanDir, createResolver } from '@nuxt/kit'

export default defineNuxtModule({
  setup() {
    const { resolve } = createResolver(import.meta.url)

    // 单个处理程序
    addServerHandler({
      route: '/api/my-endpoint',
      handler: resolve('./runtime/server/api/my-endpoint'),
    })

    // 扫描整个服务器目录（api/、routes/、middleware/、utils/）
    addServerScanDir(resolve('./runtime/server'))
  },
})
```

### 服务器组合式函数

```ts
import { addServerImports, addServerImportsDir, createResolver } from '@nuxt/kit'

export default defineNuxtModule({
  setup() {
    const { resolve } = createResolver(import.meta.url)

    // 单个服务器导入
    addServerImports({
      name: 'useServerUtil',
      from: resolve('./runtime/server/utils/useServerUtil'),
    })

    // 服务器组合式函数目录
    addServerImportsDir(resolve('./runtime/server/composables'))
  },
})
```

### Nitro 插件

```ts
import { addServerPlugin, createResolver } from '@nuxt/kit'

export default defineNuxtModule({
  setup() {
    const { resolve } = createResolver(import.meta.url)
    addServerPlugin(resolve('./runtime/server/plugin'))
  },
})
```

```ts
// runtime/server/plugin.ts
import { defineNitroPlugin } from 'nitropack/runtime'

export default defineNitroPlugin((nitroApp) => {
  nitroApp.hooks.hook('request', (event) => {
    console.log('请求:', event.path)
  })
})
```

## 模板和虚拟文件

### 生成虚拟文件

```ts
import { addTemplate, addTypeTemplate, addServerTemplate, createResolver } from '@nuxt/kit'

export default defineNuxtModule({
  setup(options, nuxt) {
    const { resolve } = createResolver(import.meta.url)

    // 客户端/构建虚拟文件（通过 #build/my-config.mjs 访问）
    addTemplate({
      filename: 'my-config.mjs',
      getContents: () => `export default ${JSON.stringify(options)}`,
    })

    // 类型声明
    addTypeTemplate({
      filename: 'types/my-module.d.ts',
      getContents: () => `
declare module '#my-module' {
  export interface Config {
    apiKey: string
  }
}`,
    })

    // Nitro 虚拟文件（在服务器路由中可访问）
    addServerTemplate({
      filename: '#my-module/config.mjs',
      getContents: () => `export const config = ${JSON.stringify(options)}`,
    })
  },
})
```

### 访问虚拟文件

```ts
// 在运行时插件中
// @ts-expect-error - 虚拟文件
import config from '#build/my-config.mjs'

// 在服务器路由中
import { config } from '#my-module/config.js'
```

## 扩展页面和路由

```ts
import { extendPages, extendRouteRules, addRouteMiddleware, createResolver } from '@nuxt/kit'

export default defineNuxtModule({
  setup() {
    const { resolve } = createResolver(import.meta.url)

    // 添加页面
    extendPages((pages) => {
      pages.push({
        name: 'my-page',
        path: '/my-route',
        file: resolve('./runtime/pages/MyPage.vue'),
      })
    })

    // 添加路由规则（缓存、重定向等）
    extendRouteRules('/api/**', {
      cache: { maxAge: 60 },
    })

    // 添加中间件
    addRouteMiddleware({
      name: 'my-middleware',
      path: resolve('./runtime/middleware/myMiddleware'),
      global: true,
    })
  },
})
```

## 模块依赖

声明对其他模块的依赖及版本约束：

```ts
export default defineNuxtModule({
  meta: {
    name: 'my-module',
  },
  moduleDependencies: {
    '@nuxtjs/tailwindcss': {
      version: '>=6.0.0',
      // 设置默认值（用户可以覆盖）
      defaults: {
        exposeConfig: true,
      },
      // 强制特定选项
      overrides: {
        viewer: false,
      },
    },
    '@nuxtjs/i18n': {
      optional: true, // 如果未安装不会失败
      defaults: {
        defaultLocale: 'en',
      },
    },
  },
  setup() {
    // 依赖项保证在此运行之前设置
  },
})
```

### 动态依赖

```ts
moduleDependencies(nuxt) {
  const deps: Record<string, any> = {
    '@nuxtjs/tailwindcss': { version: '>=6.0.0' },
  }

  if (nuxt.options.ssr) {
    deps['@nuxtjs/html-validator'] = { optional: true }
  }

  return deps
}
```

## 生命周期钩子

需要 `meta.name` 和 `meta.version`：

```ts
export default defineNuxtModule({
  meta: {
    name: 'my-module',
    version: '1.2.0',
  },
  onInstall(nuxt) {
    // 首次设置
    console.log('模块首次安装')
  },
  onUpgrade(nuxt, options, previousVersion) {
    // 版本升级迁移
    console.log(`从 ${previousVersion} 升级`)
  },
  setup(options, nuxt) {
    // 常规设置每次构建都运行
  },
})
```

## 扩展配置

```ts
export default defineNuxtModule({
  setup(options, nuxt) {
    // 添加 CSS
    nuxt.options.css.push('my-module/styles.css')

    // 添加运行时配置
    nuxt.options.runtimeConfig.public.myModule = {
      apiUrl: options.apiUrl,
    }

    // 扩展 Vite 配置
    nuxt.options.vite.optimizeDeps ||= {}
    nuxt.options.vite.optimizeDeps.include ||= []
    nuxt.options.vite.optimizeDeps.include.push('some-package')

    // 添加构建转译
    nuxt.options.build.transpile.push('my-package')
  },
})
```

## 使用钩子

```ts
export default defineNuxtModule({
  // 声明式钩子
  hooks: {
    'components:dirs': (dirs) => {
      dirs.push({ path: '~/extra' })
    },
  },

  setup(options, nuxt) {
    // 编程式钩子
    nuxt.hook('pages:extend', (pages) => {
      // 修改页面
    })

    nuxt.hook('imports:extend', (imports) => {
      imports.push({ name: 'myHelper', from: 'my-package' })
    })

    nuxt.hook('nitro:config', (config) => {
      // 修改 Nitro 配置
    })

    nuxt.hook('vite:extendConfig', (config) => {
      // 修改 Vite 配置
    })
  },
})
```

## 路径解析

```ts
import { createResolver, resolvePath, findPath } from '@nuxt/kit'

export default defineNuxtModule({
  async setup(options, nuxt) {
    // 相对于模块的解析器
    const { resolve } = createResolver(import.meta.url)

    const pluginPath = resolve('./runtime/plugin')

    // 使用扩展名和别名解析
    const entrypoint = await resolvePath('@some/package')

    // 查找第一个存在的文件
    const configPath = await findPath([
      resolve('./config.ts'),
      resolve('./config.js'),
    ])
  },
})
```

## 模块 Package.json

```json
{
  "name": "my-nuxt-module",
  "version": "1.0.0",
  "type": "module",
  "exports": {
    ".": {
      "import": "./dist/module.mjs",
      "require": "./dist/module.cjs"
    }
  },
  "main": "./dist/module.cjs",
  "module": "./dist/module.mjs",
  "types": "./dist/types.d.ts",
  "files": ["dist"],
  "scripts": {
    "dev": "nuxi dev playground",
    "build": "nuxt-module-build build",
    "prepare": "nuxt-module-build build --stub"
  },
  "dependencies": {
    "@nuxt/kit": "^3.0.0"
  },
  "devDependencies": {
    "@nuxt/module-builder": "latest",
    "nuxt": "^3.0.0"
  }
}
```

## 禁用模块

用户可以通过配置键禁用模块：

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  // 完全禁用
  myModule: false,

  // 或使用选项
  myModule: {
    enabled: false,
  },
})
```

## 开发工作流

1. **创建模块**：`npx nuxi init -t module my-module`
2. **开发**：`npm run dev`（运行 playground）
3. **构建**：`npm run build`
4. **测试**：`npm run test`

## 最佳实践

- 对所有路径解析使用 `createResolver(import.meta.url)`
- 为组件添加前缀以避免命名冲突
- 使用 `ModuleOptions` 接口使选项类型安全
- 使用 `moduleDependencies` 而不是 `installModule`
- 为所有选项提供合理的默认值
- 在 `meta.compatibility` 中添加兼容性要求
- 使用虚拟文件进行动态配置
- 适当地分离客户端/服务器插件

## 模块开发最佳实践

### 处理异步设置

Nuxt 模块可以是异步的，但要小心异步行为，因为 Nuxt 会等待模块设置完成才继续到下一个模块和启动开发服务器。如果耗时超过 1 秒，Nuxt 会发出警告。

```ts
import { defineNuxtModule } from '@nuxt/kit'

export default defineNuxtModule({
  setup(options, nuxt) {
    // ❌ 不好的做法：在 setup 中执行耗时操作
    // await longRunningOperation()

    // ✅ 好的做法：延迟到 hooks
    nuxt.hook('app:generated', async () => {
      await longRunningOperation()
    })
  },
})
```

### 为导出添加前缀

Nuxt 模块应该为任何暴露的配置、插件、API、composable、组件或服务器路由提供明确的前缀，以避免与其他模块、Nuxt 内部或用户定义的代码冲突。

```ts
// ❌ 避免：通用名称
| 类型 | 避免 | 推荐 |
| --- | --- | --- |
| 组件 | `<Button>`, `<Modal>` | `<FooButton>`, `<FooModal>` |
| Composables | `useData()`, `useModal()` | `useFooData()`, `useFooModal()` |
| 服务器路由 | `/api/track`, `/api/data` | `/api/_foo/track`, `/api/_foo/data` |

// ✅ 推荐：带模块名称前缀
// 组件
<FooButton />
<FooModal />

// Composables
const { data } = useFooData()
const modal = useFooModal()

// 服务器路由
/api/_foo/track
/api/_foo/data
```

### 使用 TypeScript

Nuxt 具有一流的 TypeScript 集成以提供最佳开发体验。即使不直接使用 TypeScript 的用户也能从类型中受益。

```ts
// 为模块选项提供完整类型
export interface ModuleOptions {
  apiKey?: string
  baseURL?: string
  timeout?: number
  features?: {
    analytics?: boolean
    tracking?: boolean
  }
}

export default defineNuxtModule<ModuleOptions>({
  defaults: {
    timeout: 5000,
    features: {
      analytics: true,
    },
  },
})
```

### 使用 ESM 语法

Nuxt 依赖于原生 ESM。请使用 `.ts` 或 `.mjs` 文件扩展名。

```json
{
  "name": "my-nuxt-module",
  "type": "module",
  "main": "./dist/module.mjs",
  "module": "./dist/module.mjs",
  "exports": {
    ".": {
      "import": "./dist/module.mjs"
    }
  }
}
```

### 文档化模块

在 README 文件中记录模块使用：

```markdown
# @nuxtjs/my-module

## 为什么使用这个模块？

- 提供 X 功能
- 集成 Y 服务
- 优化性能

## 如何使用？

### 安装

```bash
npm install @nuxtjs/my-module
```

### 配置

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  myModule: {
    apiKey: 'your-api-key',
    timeout: 5000,
  },
})
```

## 模块做什么？

- 添加 X 组件
- 自动导入 Y composables
- 配置 Z 服务
```

### 提供 Demo

创建一个最小化的 playground 并提供 StackBlitz 链接。这不仅为潜在用户提供了快速实验模块的方法，也是他们遇到问题时创建最小化重现的简单方式。

### 保持版本无关性

使用 "适用于 Nuxt" 而不是 "适用于 Nuxt 3"，以避免生态系统碎片化。使用 `meta.compatibility` 设置 Nuxt 版本约束。

### 遵循启动器约定

模块启动器提供了一组默认工具和配置（如 ESLint 配置）。如果你计划开源模块，坚持使用这些默认设置可以确保你的模块与其他社区模块共享一致的编码风格，使其他人更容易贡献。

### 测试模块

```ts
// tests/module.test.ts
import { describe, it, expect } from 'vitest'
import { setup, $fetch, createPage } from '@nuxt/test-utils/e2e'

describe('my-module', () => {
  it('注册组件', async () => {
    const { page } = await setup({
      rootDir: './fixtures/basic',
    })

    const html = await page.render('/')
    expect(html).toContain('<FooButton')
  })

  it('自动导入 composables', async () => {
    const { page } = await setup({
      rootDir: './fixtures/basic',
    })

    const { useMyModule } = await page.render('/').context
    expect(typeof useMyModule).toBe('function')
  })
})
```

### 调试模块

```ts
// 在开发模式下启用详细日志
export default defineNuxtModule({
  setup(options, nuxt) {
    if (import.meta.dev) {
      console.log('🔧 Module options:', options)
      console.log('📁 Module root:', nuxt.options.rootDir)
      console.log('📦 Module srcDir:', nuxt.options.srcDir)
    }
  },
})
```

### 错误处理

```ts
export default defineNuxtModule({
  setup(options, nuxt) {
    try {
      validateOptions(options)
    } catch (error) {
      // 提供清晰的错误信息
      console.error('❌ Module configuration error:', error.message)
      console.error('Please check your nuxt.config.ts')
      throw error
    }
  },
})

function validateOptions(options: ModuleOptions) {
  if (options.enabled && !options.apiKey) {
    throw new Error('apiKey is required when module is enabled')
  }
}
```

### 性能优化

```ts
export default defineNuxtModule({
  setup(options, nuxt) {
    // ❌ 不好的做法：同步阻塞操作
    // const data = readFileSync('large-file.json')

    // ✅ 好的做法：异步操作
    // const data = await readFile('large-file.json')

    // 延迟非关键设置
    nuxt.hook('build:done', () => {
      console.log('Build completed')
    })
  },
})
```

### 条件功能

```ts
export default defineNuxtModule({
  setup(options, nuxt) {
    // 基于 SSR 模式启用/禁用功能
    if (nuxt.options.ssr) {
      addServerPlugin('./plugins/ssr')
    }

    if (import.meta.dev) {
      addPlugin('./plugins/devtools')
    }

    if (options.features?.analytics) {
      addServerHandler({
        route: '/api/_my-module/analytics',
        handler: './api/analytics',
      })
    }
  },
})
```

<!--
Source references:
- https://nuxt.com/docs/api/kit/modules
- https://nuxt.com/docs/api/kit/components
- https://nuxt.com/docs/api/kit/autoimports
- https://nuxt.com/docs/api/kit/plugins
- https://nuxt.com/docs/api/kit/templates
- https://nuxt.com/docs/api/kit/nitro
- https://nuxt.com/docs/api/kit/pages
- https://nuxt.com/docs/api/kit/resolving
-->
