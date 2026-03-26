---
name: nuxt-layers
description: 使用层扩展 Nuxt 应用，实现代码共享和复用
---

# Nuxt 层

层允许跨项目共享和复用部分 Nuxt 应用。它们可以包含组件、组合式函数、页面、布局和配置。

## 使用场景

- 通过 `nuxt.config` 和 `app.config` 跨项目共享可重用的配置预设
- 使用 `app/components/` 目录创建组件库
- 使用 `app/composables/` 和 `app/utils/` 目录创建工具和组合式函数库
- 创建 Nuxt 模块预设
- 跨项目共享标准设置
- 创建 Nuxt 主题
- 通过实现模块化架构支持大型项目的领域驱动设计（DDD）模式

## 使用层

### 从 npm 包

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  extends: [
    '@my-org/base-layer',
    '@nuxtjs/ui-layer',
  ],
})
```

### 从 Git 仓库

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  extends: [
    'github:username/repo',
    'github:username/repo/base', // 子目录
    'github:username/repo#v1.0', // 特定标签
    'github:username/repo#dev', // 分支
    'gitlab:username/repo',
    'bitbucket:username/repo',
  ],
})
```

### 从本地目录

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  extends: [
    '../base-layer',
    './layers/shared',
  ],
})
```

### 自动扫描的层

放在 `layers/` 目录中自动发现：

```
my-app/
├── layers/
│   ├── base/
│   │   └── nuxt.config.ts
│   └── ui/
│       └── nuxt.config.ts
└── nuxt.config.ts
```

## 创建层

最小层结构：

```
my-layer/
├── nuxt.config.ts       # 必需
├── app/
│   ├── components/      # 自动合并
│   ├── composables/     # 自动合并
│   ├── layouts/         # 自动合并
│   ├── middleware/      # 自动合并
│   ├── pages/           # 自动合并
│   ├── plugins/         # 自动合并
│   └── app.config.ts    # 合并
├── server/              # 自动合并
└── package.json
```

### 层的 nuxt.config.ts

```ts
// my-layer/nuxt.config.ts
export default defineNuxtConfig({
  // 层配置
  app: {
    head: {
      title: 'My Layer App',
    },
  },
  // 共享模块
  modules: ['@nuxt/ui'],
})
```

### 层组件

```vue
<!-- my-layer/app/components/BaseButton.vue -->
<template>
  <button class="base-btn">
    <slot />
  </button>
</template>
```

在消费项目中使用：

```vue
<template>
  <BaseButton>点击我</BaseButton>
</template>
```

### 层组合式函数

```ts
// my-layer/app/composables/useTheme.ts
export function useTheme() {
  const isDark = useState('theme-dark', () => false)
  const toggle = () => isDark.value = !isDark.value
  return { isDark, toggle }
}
```

## 层优先级

使用多个层时，了解覆盖顺序非常重要。具有**更高优先级**的层在定义相同文件或组件时覆盖具有较低优先级的层。

### 优先级顺序

从高到低优先级：
1. **你的项目文件** - 始终具有最高优先级
2. **`~~/layers` 目录中的自动扫描层** - 按字母顺序排序（Z 比 A 优先级高）
3. **`extends` 配置中的层** - 第一个条目比第二个优先级高

### 实际示例

考虑多个层定义相同组件：

```
Directory structure
layers/
  1.base/
    app/components/Button.vue    # 基础按钮样式
  2.theme/
    app/components/Button.vue    # 主题按钮（覆盖基础）
app/
  components/Button.vue          # 项目按钮（覆盖所有层）
```

在这种情况下：
- 如果只存在层，则使用 `2.theme/Button.vue`（字母顺序更高）
- 如果你的项目中存在 `app/components/Button.vue`，它将覆盖所有层

### 控制优先级

可以使用数字前缀来控制层顺序：

```
Directory structure
layers/
  1.base/        # 最低优先级
  2.features/    # 中等优先级
  3.admin/       # 最高优先级（在层中）
```

### 何时使用每种方式

- **`~~/layers` 目录** - 用于项目本地的层
- **`extends`** - 用于外部依赖（npm 包、远程仓库）或项目目录外的层

### 使用 extends 的完整示例

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  extends: [
    '../base', // 项目外的本地层
    '@my-themes/awesome', // NPM 包
    'github:my-themes/awesome#v1', // 远程仓库
  ],
})
```

如果你还有 `~~/layers/custom`，优先级顺序为：
- 你的项目文件（最高）
- `~~/layers/custom`
- `../base`
- `@my-themes/awesome`
- `github:my-themes/awesome#v1`（最低）

## 层别名

访问层文件：

```ts
// 自动扫描的层获得别名
import Component from '#layers/base/components/Component.vue'
```

### 命名别名

```ts
// my-layer/nuxt.config.ts
export default defineNuxtConfig({
  $meta: {
    name: 'my-layer',
  },
})
```

```ts
// 在消费项目中
import { something } from '#layers/my-layer/utils'
```

### 自定义层别名

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  extends: [
    [
      'github:my-themes/awesome',
      {
        meta: {
          name: 'my-awesome-theme',
        },
      },
    ],
  ],
})
```

### 在模块中访问层信息

```ts
// modules/my-module.ts
import { defineNuxtModule, getLayerDirectories } from '@nuxt/kit'

export default defineNuxtModule({
  setup() {
    const layers = getLayerDirectories()

    for (const [index, layer] of layers.entries()) {
      console.log(`Layer ${index}:`)
      console.log(`  Root: ${layer.root}`)
      console.log(`  App: ${layer.app}`)
      console.log(`  Server: ${layer.server}`)
      console.log(`  Pages: ${layer.appPages}`)
    }
  },
})
```

## 层配置合并

### 配置合并规则

层的配置会按优先级顺序合并：

```ts
// base-layer/nuxt.config.ts
export default defineNuxtConfig({
  app: {
    head: {
      title: 'Base Layer',
    },
  },
  modules: ['@nuxt/ui'],
})

// theme-layer/nuxt.config.ts
export default defineNuxtConfig({
  app: {
    head: {
      title: 'Theme Layer', // 覆盖基础层
    },
  },
  css: ['~/assets/theme.css'],
})

// app/nuxt.config.ts
export default defineNuxtConfig({
  extends: ['base-layer', 'theme-layer'],
  app: {
    head: {
      title: 'My App', // 覆盖所有层
    },
  },
  modules: ['@pinia/nuxt'], // 与层的模块合并
})
```

### 组件和资源覆盖

相同名称的文件会被更高优先级的文件覆盖：

```
base-layer/app/components/Button.vue     # 基础按钮
theme-layer/app/components/Button.vue    # 主题按钮（覆盖基础）
app/app/components/Button.vue            # 自定义按钮（覆盖所有层）
```

## 发布层

### 作为 npm 包

```json
{
  "name": "my-nuxt-layer",
  "version": "1.0.0",
  "type": "module",
  "main": "./nuxt.config.ts",
  "dependencies": {
    "@nuxt/ui": "^2.0.0"
  },
  "devDependencies": {
    "nuxt": "^3.0.0"
  }
}
```

### 私有层

对于私有 git 仓库：

```bash
export GIGET_AUTH=<github-token>
```

## 层配置合并

### 配置合并规则

层的配置会按优先级顺序合并：

```ts
// base-layer/nuxt.config.ts
export default defineNuxtConfig({
  app: {
    head: {
      title: 'Base Layer',
    },
  },
  modules: ['@nuxt/ui'],
})

// theme-layer/nuxt.config.ts
export default defineNuxtConfig({
  app: {
    head: {
      title: 'Theme Layer', // 覆盖基础层
    },
  },
  css: ['~/assets/theme.css'],
})

// app/nuxt.config.ts
export default defineNuxtConfig({
  extends: ['base-layer', 'theme-layer'],
  app: {
    head: {
      title: 'My App', // 覆盖所有层
    },
  },
  modules: ['@pinia/nuxt'], // 与层的模块合并
})
```

### 组件和资源覆盖

相同名称的文件会被更高优先级的文件覆盖：

```
base-layer/app/components/Button.vue     # 基础按钮
theme-layer/app/components/Button.vue    # 主题按钮（覆盖基础）
app/app/components/Button.vue            # 自定义按钮（覆盖所有层）
```

## 层最佳实践

```ts
// my-layer/nuxt.config.ts
import { fileURLToPath } from 'node:url'
import { dirname, join } from 'node:path'

const currentDir = dirname(fileURLToPath(import.meta.url))

export default defineNuxtConfig({
  css: [
    join(currentDir, './assets/main.css'),
  ],
})
```

### 安装依赖

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  extends: [
    ['github:user/layer', { install: true }],
  ],
})
```

### 禁用层模块

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  extends: ['./base-layer'],
  // 禁用来自层的模块
  image: false, // 禁用 @nuxt/image
  pinia: false, // 禁用 @pinia/nuxt
})
```

## 启动模板

创建新层：

```bash
npx nuxi init --template layer my-layer
```

## 示例：主题层

```
theme-layer/
├── nuxt.config.ts
├── app/
│   ├── app.config.ts
│   ├── components/
│   │   ├── ThemeButton.vue
│   │   └── ThemeCard.vue
│   ├── composables/
│   │   └── useTheme.ts
│   └── assets/
│       └── theme.css
└── package.json
```

```ts
// theme-layer/nuxt.config.ts
export default defineNuxtConfig({
  css: ['~/assets/theme.css'],
})
```

```ts
// theme-layer/app/app.config.ts
export default defineAppConfig({
  theme: {
    primaryColor: '#00dc82',
    darkMode: false,
  },
})
```

```ts
// consuming-app/nuxt.config.ts
export default defineNuxtConfig({
  extends: ['theme-layer'],
})

// consuming-app/app/app.config.ts
export default defineAppConfig({
  theme: {
    primaryColor: '#ff0000', // 覆盖
  },
})
```

## 层的高级用法

### 为导出添加前缀

避免与用户代码、Nuxt 内部或其他模块冲突：

```ts
// my-layer/app/components/
// 错误做法：避免通用名称
// <Button.vue>
// <Modal.vue>

// 正确做法：推荐带前缀
// <MyLayerButton.vue>
// <MyLayerModal.vue>

// my-layer/app/composables/
// 错误做法：避免
// export function useData() {}
// export function useModal() {}

// 正确做法：推荐
// export function useMyLayerData() {}
// export function useMyLayerModal() {}
```

### 服务器路由前缀

为层中的服务器路由使用唯一前缀：

```ts
// my-layer/server/api/
// 好的做法：使用模块名称作为前缀
// /api/_my-layer/endpoint
// /_my-layer/feature

// 错误做法：避免常见路径
// /api/auth
// /api/user
```

### 跨层共享配置

使用 `app.config.ts` 在层之间共享配置：

```ts
// base-layer/app/app.config.ts
export default defineAppConfig({
  theme: {
    primaryColor: '#00dc82',
    borderRadius: '8px',
  },
})

// theme-layer/app/app.config.ts
export default defineAppConfig({
  theme: {
    primaryColor: '#ff0000', // 覆盖基础层
    darkMode: true, // 新增配置
  },
})

// app/app/app.config.ts
export default defineAppConfig({
  theme: {
    primaryColor: '#00ff00', // 项目层覆盖所有层
    customProperty: 'value', // 项目特定配置
  },
})
```

### 使用 TypeScript

确保层提供完整的类型支持：

```ts
// my-layer/app/app.config.ts
export default defineAppConfig({
  theme: {
    type: 'light' as 'light' | 'dark',
    colors: {
      primary: '#00dc82',
      secondary: '#642c9e',
    },
  },
})

// types/app-config.d.ts
declare module '#app' {
  interface AppConfigInput {
    theme?: {
      type?: 'light' | 'dark'
      colors?: {
        primary?: string
        secondary?: string
      }
    }
  }
}
```

### 文档化层

为层创建清晰的文档：

```markdown
# My Layer

## 特性
- 提供可重用的 UI 组件
- 包含响应式组合式函数
- 内置 Tailwind CSS 配置

## 安装

在 `nuxt.config.ts` 中扩展：

```ts
export default defineNuxtConfig({
  extends: ['my-layer'],
})
```

## 使用

```vue
<template>
  <MyLayerButton @click="handleClick">点击我</MyLayerButton>
</template>

<script setup>
const { myTheme } = useMyLayerTheme()
</script>
```

## 配置选项

在 `app/app.config.ts` 中自定义：

```ts
export default defineAppConfig({
  myLayer: {
    primaryColor: '#00dc82',
  },
})
```
```

### 使用 Playground 测试层

创建 `playground/` 目录来测试你的层：

```
my-layer/
├── nuxt.config.ts
├── app/
│   └── components/
└── playground/
    ├── nuxt.config.ts
    └── app.vue
```

```ts
// playground/nuxt.config.ts
export default defineNuxtConfig({
  extends: ['..'], // 扩展父级层
})
```

运行 playground 进行测试：

```bash
npm run dev
```

### 处理条件逻辑

在层中使用环境检查来控制功能：

```ts
// my-layer/nuxt.config.ts
export default defineNuxtConfig({
  modules: [],
})

export default defineNuxtConfig({
  modules: import.meta.dev ? [
    '@nuxt/devtools',
  ] : [],
})
```

## 常见问题

### Q: 如何避免层之间的冲突？

**A:**
1. 为所有导出（组件、composables、路由）添加前缀
2. 使用 `app.config.ts` 提供可配置的选项
3. 在文档中清楚说明覆盖规则

### Q: 层会影响性能吗？

**A:**
- 层主要在构建时工作
- 运行时性能影响最小
- 避免在层中添加不必要的全局插件
- 使用 `Lazy` 前缀延迟加载大型组件

### Q: 如何调试层问题？

**A:**
```ts
// app/plugins/debug-layers.ts
export default defineNuxtPlugin(() => {
  if (import.meta.dev) {
    const { getLayerDirectories } = await import('@nuxt/kit')
    const layers = getLayerDirectories()

    console.table(
      layers.map((layer, index) => ({
        priority: index === 0 ? 'Highest' : `Layer ${index}`,
        root: layer.root,
      }))
    )
  }
})
```

<!--
Source references:
- https://nuxt.com/docs/getting-started/layers
- https://nuxt.com/docs/guide/going-further/layers
-->
