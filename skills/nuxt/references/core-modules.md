---
name: nuxt-modules
description: 创建和使用 Nuxt 模块扩展框架功能
---

# Nuxt 模块

模块扩展 Nuxt 的核心功能。它们在构建时运行，可以添加组件、composables、插件和配置。

## 使用模块

安装并添加到 `nuxt.config.ts`：

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  modules: [
    // npm 包
    '@nuxt/ui',
    // 本地模块
    './modules/my-module',
    // 内联模块
    (options, nuxt) => {
      console.log('内联模块')
    },
    // 带选项
    ['@nuxt/image', { provider: 'cloudinary' }],
  ],
})
```

## 创建模块

### 基础模块

```ts
// modules/my-module.ts
export default defineNuxtModule({
  meta: {
    name: 'my-module',
    configKey: 'myModule',
  },
  defaults: {
    enabled: true,
  },
  setup(options, nuxt) {
    if (!options.enabled) return

    console.log('我的模块正在运行！')
  },
})
```

### 添加组件

```ts
// modules/ui/index.ts
import { addComponent, createResolver } from '@nuxt/kit'

export default defineNuxtModule({
  setup(options, nuxt) {
    const { resolve } = createResolver(import.meta.url)

    // 添加单个组件
    addComponent({
      name: 'MyButton',
      filePath: resolve('./runtime/components/MyButton.vue'),
    })

    // 添加组件目录
    addComponentsDir({
      path: resolve('./runtime/components'),
      prefix: 'My',
    })
  },
})
```

### 添加 Composables

```ts
// modules/my-module.ts
import { addImports } from '@nuxt/kit'
import { myComposable } from './runtime/composables'

export default defineNuxtModule({
  setup(options, nuxt) {
    addImports({
      name: 'useMyFeature',
      as: 'useMyFeature',
      from: '@nuxt/kit',
    })
  },
})
```

### 添加插件

```ts
// modules/auth-module/index.ts
import { addPlugin, createResolver } from '@nuxt/kit'

export default defineNuxtModule({
  setup(options, nuxt) {
    const { resolve } = createResolver(import.meta.url)

    addPlugin({
      src: resolve('./runtime/plugin.ts'),
    })
  },
})
```

### 添加配置

```ts
// modules/i18n/index.ts
import { addImports, createResolver } from '@nuxt/kit'

export default defineNuxtModule({
  setup(options, nuxt) {
    // 添加类型到 nuxt.config.ts
    nuxt.hook('ready', () => {
      console.log('Nuxt 已准备就绪！')
    })
  },
})
```

## Nuxt Kit 工具

### 常用函数

```ts
import { 
  addPlugin, 
  addComponent, 
  addComponentsDir,
  addImports,
  createResolver,
  useNuxt,
} from '@nuxt/kit'

export default defineNuxtModule({
  setup(options, nuxt) {
    // 当前 Nuxt 实例
    const nuxt = useNuxt()
    
    // 添加钩子
    nuxt.hook('render:setup', () => {
      console.log('渲染设置')
    })
  },
})
```

### 解析路径

```ts
import { createResolver } from '@nuxt/kit'

export default defineNuxtModule({
  setup(options, nuxt) {
    const { resolve } = createResolver(import.meta.url)
    
    // 解析相对于模块的路径
    const componentPath = resolve('./components/MyButton.vue')
    const imagePath = resolve('./assets/logo.png')
  },
})
```

## 模块选项

```ts
export default defineNuxtModule({
  setup(options, nuxt) {
    // 访问模块选项
    const apiKey = options.apiKey
    const enabled = options.enabled ?? true
    
    // 从 nuxt.config.ts 访问配置
    const nuxtConfig = nuxt.options
  },
})
```

## 发布模块

创建可发布的 Nuxt 模块：

```ts
// package.json
{
  "name": "@nuxtjs/my-module",
  "type": "module",
  "exports": {
    ".": {
      "import": "./dist/module.mjs",
      "require": "./dist/module.cjs"
    }
  },
  "keywords": ["nuxt", "module", "nuxt-module"],
  "peerDependencies": {
    "nuxt": "^3.0.0"
  }
}
```

## 最佳实践

1. **使用 Nuxt Kit** - 提供类型安全和统一的 API
2. **创建解析器** - 处理相对于模块的路径
3. **添加类型** - 支持模块的 TypeScript 类型定义
4. **使用钩子** - 在适当的生命周期中集成
5. **提供默认选项** - 使模块开箱即用

## 高级模块开发

### 模块钩子

```ts
export default defineNuxtModule({
  setup(options, nuxt) {
    // 模块初始化时
    nuxt.hook('modules:before', () => {
      console.log('模块初始化前')
    })
    
    // 配置完成后
    nuxt.hook('modules:done', () => {
      console.log('所有模块加载完成')
    })
    
    // 应用构建前
    nuxt.hook('build:before', () => {
      console.log('开始构建')
    })
  },
})
```

### 条件注册

```ts
export default defineNuxtModule({
  setup(options, nuxt) {
    // 仅在 SSR 模式下
    if (nuxt.options.ssr) {
      addPlugin({
        src: resolve('./runtime/plugin.server.ts'),
        mode: 'server',
      })
    }
  },
})
```

### 添加类型定义

```ts
// modules/my-module.ts
export default defineNuxtModule({
  setup(options, nuxt) {
    // 添加到 nuxt.config.ts 的类型
    nuxt.hook('prepare:types', ({ tsConfig, references }) => {
      references.push({
        path: resolve('./runtime/types'),
      })
    })
  },
})
```

### 模块版本控制

```ts
// modules/my-module.ts
import { packageDir } from '@nuxt/kit'

export default defineNuxtModule({
  meta: {
    name: 'my-module',
    version: require(packageDir('./runtime') + '/package.json').version,
  },
})
```

### 模块间通信

```ts
// 模块 A 提供数据
export default defineNuxtModule({
  setup(options, nuxt) {
    nuxt.options.runtimeConfig.public.myModule = {
      enabled: true,
    }
  },
})

// 模块 B 使用数据
export default defineNuxtModule({
  setup(options, nuxt) {
    const enabled = nuxt.options.runtimeConfig.public.myModule?.enabled
  },
})
```

### 添加 Vue 指令

```ts
export default defineNuxtModule({
  setup(options, nuxt) {
    addPlugin({
      src: resolve('./runtime/directive.ts'),
    })
  },
})
```

```ts
// runtime/directive.ts
export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.vueApp.directive('focus', {
    mounted(el) {
      el.focus()
    },
  })
})
```

### 添加中间件

```ts
export default defineNuxtModule({
  setup(options, nuxt) {
    const { resolve } = createResolver(import.meta.url)
    
    nuxt.hook('pages:extend', (pages) => {
      pages.push({
        name: 'middleware-page',
        path: '/protected',
        file: resolve('./runtime/middleware/auth.ts'),
      })
    })
  },
})
```

## 模块测试

### 单元测试

```ts
// test/module.test.ts
import { setup, $fetch, createPage } from '@nuxt/test-utils/e2e'
import { describe, it, expect } from 'vitest'

await setup({
  rootDir: './fixtures/basic',
  server: true,
})

describe('my module', () => {
  it('renders correctly', async () => {
    const html = await $fetch('/')
    expect(html).toContain('Hello')
  })
})
```

### 集成测试

```ts
// test/integration.test.ts
import { describe, it, expect } from 'vitest'
import { defineTestModule } from '@nuxt/test-utils/kit'

describe('module integration', () => {
  it('registers components', async () => {
    const { mount } = await defineTestModule({
      modules: ['my-module'],
    })
    
    const { html } = await mount({
      template: '<MyButton />',
    })
    
    expect(html()).toContain('button')
  })
})
```

## 模块配置选项

### 选项验证

```ts
import { resolvePath } from '@nuxt/kit'

export default defineNuxtModule({
  defaults: {
    apiKey: '',
    timeout: 5000,
    enabled: true,
  },
  
  setup(options, nuxt) {
    // 验证选项
    if (options.enabled && !options.apiKey) {
      throw new Error('API key is required when module is enabled')
    }
    
    // 解析路径
    const configPath = await resolvePath(options.configFile)
  },
})
```

### 环境特定选项

```ts
export default defineNuxtModule({
  setup(options, nuxt) {
    const isDev = nuxt.options.dev
    
    if (isDev) {
      console.log('开发模式配置')
    }
  },
})
```

## 模块打包

### 发布模块

```bash
# 构建模块
npm run build

# 发布到 npm
npm publish
```

### package.json 配置

```json
{
  "name": "@nuxtjs/my-module",
  "version": "1.0.0",
  "type": "module",
  "exports": {
    ".": {
      "types": "./dist/types.d.ts",
      "import": "./dist/module.mjs",
      "require": "./dist/module.cjs"
    },
    "./dist/runtime/*": "./dist/runtime/*"
  },
  "files": [
    "dist"
  ],
  "keywords": [
    "nuxt",
    "module",
    "nuxt-module"
  ]
}
```

## 常见问题

### 模块不生效

**原因：** 模块未正确安装或配置

**解决：**

```ts
// 检查模块是否加载
nuxt.hook('ready', () => {
  console.log('Nuxt ready, module loaded')
})
```

### 类型定义缺失

**解决：**

```ts
// 创建 runtime/types/index.d.ts
declare module '#app' {
  interface NuxtApp {
    $myModule: MyModuleApi
  }
}
```

### 构建错误

**原因：** 路径解析问题

**解决：**

```ts
const { resolve } = createResolver(import.meta.url)
const componentPath = resolve('./runtime/components/Button.vue')
```

## 模块最佳实践

1. **使用 @nuxt/kit** - 提供稳定的 API
2. **清晰的命名** - 避免冲突
3. **提供默认配置** - 易于上手
4. **详细的文档** - 包含示例
5. **完整的类型** - TypeScript 支持
6. **测试覆盖** - 确保质量
7. **版本管理** - 遵循语义化版本

<!-- 
源码参考：
- https://nuxt.com/docs/guide/going-further/modules
- https://nuxt.com/docs/api/kit
- https://nuxt.com/docs/api/kit/modules
-->