---
name: components-auto-imports
description: 自动导入的组件、懒加载和水合策略
---

# 组件自动导入

Nuxt 自动从 `app/components/` 目录导入 Vue 组件。

## 基本自动导入

```
components/
├── Button.vue         → <Button />
├── Card.vue           → <Card />
└── AppHeader.vue      → <AppHeader />
```

```vue
<template>
  <!-- 无需导入 -->
  <AppHeader />
  <Card>
    <Button>点击我</Button>
  </Card>
</template>
```

## 命名约定

### 嵌套目录名称

组件名称包含目录路径：

```
components/
├── base/
│   └── Button.vue       → <BaseButton />
├── form/
│   ├── Input.vue        → <FormInput />
│   └── Select.vue       → <FormSelect />
└── ui/
    └── modal/
        └── Dialog.vue   → <UiModalDialog />
```

### 禁用路径前缀

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  components: [
    {
      path: '~/components',
      pathPrefix: false, // 仅使用文件名
    },
  ],
})
```

使用 `pathPrefix: false` 时：
```
components/base/Button.vue → <Button />
```

## 懒加载

使用 `Lazy` 前缀进行动态导入：

```vue
<script setup lang="ts">
const showChart = ref(false)
</script>

<template>
  <!-- 组件代码仅在渲染时加载 -->
  <LazyHeavyChart v-if="showChart" />
  <button @click="showChart = true">显示图表</button>
</template>
```

优点：
- 减少初始包大小
- 将组件代码分割到单独的块中
- 按需加载

## 懒水合策略

控制懒组件何时变为可交互：

### `hydrate-on-visible`

当组件进入视口时水合：

```vue
<template>
  <LazyComments hydrate-on-visible />
</template>
```

### `hydrate-on-idle`

当浏览器空闲时水合：

```vue
<template>
  <LazyAnalytics hydrate-on-idle />
</template>
```

### `hydrate-on-interaction`

在用户交互时水合：

```vue
<template>
  <!-- 在点击、聚焦或 pointerenter 时水合 -->
  <LazyDropdown hydrate-on-interaction />

  <!-- 特定事件 -->
  <LazyTooltip hydrate-on-interaction="mouseover" />
</template>
```

### `hydrate-on-media-query`

当媒体查询匹配时水合：

```vue
<template>
  <LazyMobileMenu hydrate-on-media-query="(max-width: 768px)" />
</template>
```

### `hydrate-after`

延迟后水合（毫秒）：

```vue
<template>
  <LazyAds :hydrate-after="3000" />
</template>
```

### `hydrate-when`

条件水合：

```vue
<script setup lang="ts">
const isReady = ref(false)
</script>

<template>
  <LazyEditor :hydrate-when="isReady" />
</template>
```

### `hydrate-never`

从不水合（仅静态）：

```vue
<template>
  <LazyStaticFooter hydrate-never />
</template>
```

### 水合事件

```vue
<template>
  <LazyChart hydrate-on-visible @hydrated="onChartReady" />
</template>

<script setup>
function onChartReady() {
  console.log('图表现在可交互')
}
</script>
```

## 客户端/服务器组件

### 仅客户端 (`.client.vue`)

```
components/
└── BrowserChart.client.vue
```

```vue
<template>
  <!-- 仅在浏览器中渲染 -->
  <BrowserChart />
</template>
```

### 仅服务器 (`.server.vue`)

```
components/
└── ServerMarkdown.server.vue
```

```vue
<template>
  <!-- 在服务器上渲染，不水合 -->
  <ServerMarkdown :content="markdown" />
</template>
```

需要实验性标志：

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  experimental: {
    componentIslands: true,
  },
})
```

### 配对组件

```
components/
├── Comments.client.vue  # 浏览器版本
└── Comments.server.vue  # SSR 版本
```

服务器版本在 SSR 期间渲染，客户端版本在水合后接管。

## 动态组件

```vue
<script setup lang="ts">
import { SomeComponent } from '#components'

const dynamicComponent = resolveComponent('MyButton')
</script>

<template>
  <component :is="dynamicComponent" />
  <component :is="SomeComponent" />
</template>
```

## 直接导入

在需要时绕过自动导入：

```vue
<script setup lang="ts">
import { LazyMountainsList, NuxtLink } from '#components'
</script>
```

## 自定义目录

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  components: [
    { path: '~/components/ui', prefix: 'Ui' },
    { path: '~/components/forms', prefix: 'Form' },
    '~/components', // 默认，应放在最后
  ],
})
```

## 全局组件

全局注册（创建异步块）：

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  components: {
    global: true,
    dirs: ['~/components'],
  },
})
```

或使用 `.global.vue` 后缀：

```
components/
└── Icon.global.vue  → 全局可用
```

## 禁用组件自动导入

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  components: {
    dirs: [], // 禁用自动导入
  },
})
```

## 库作者

从 npm 包注册组件：

```ts
// my-ui-lib/nuxt.ts
import { addComponentsDir, createResolver, defineNuxtModule } from '@nuxt/kit'

export default defineNuxtModule({
  setup() {
    const resolver = createResolver(import.meta.url)

    addComponentsDir({
      path: resolver.resolve('./components'),
      prefix: 'MyUi',
    })
  },
})
```

## 性能优化

### 减少包大小

```vue
<template>
  <!-- 按需加载重型组件 -->
  <LazyHeavyComponent v-if="showHeavy" />
  <button @click="showHeavy = true">加载</button>
</template>
```

### 预加载策略

```vue
<template>
  <!-- 预加载关键组件 -->
  <LazyLazyChart hydrate-on-idle />
  
  <!-- 永不水合的静态内容 -->
  <LazyStaticFooter hydrate-never />
</template>
```

## 常见问题

### 组件未自动导入

**原因：** 组件不在 `components/` 目录下

**解决：**

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  components: [
    {
      path: '~/custom-components',
      pathPrefix: false,
    },
  ],
})
```

### 水合不匹配

**原因：** 服务器和客户端渲染不一致

**解决：**

```vue
<script setup>
const isClient = import.meta.client
</script>

<template>
  <ClientOnly>
    <BrowserComponent v-if="isClient" />
  </ClientOnly>
</template>
```

### 命名冲突

```ts
// components/base/Button.vue
// components/ui/Button.vue

// 使用完整名称避免冲突
<BaseButton />
<UiButton />
```

## 最佳实践

1. **使用描述性名称** - 组件名称应清晰表达其用途
2. **合理使用 Lazy** - 对重型组件使用懒加载
3. **选择合适的水合策略** - 根据组件特性选择
4. **组织组件结构** - 使用目录分组相关组件
5. **避免深层嵌套** - 最多 2-3 层目录

<!--
Source references:
- https://nuxt.com/docs/directory-structure/app/components
- https://nuxt.com/docs/guide/concepts/auto-imports#auto-imported-components
-->
