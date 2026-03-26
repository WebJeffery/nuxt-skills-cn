---
name: vue-in-vitepress-markdown
description: 在 markdown 文件中使用 Vue 组件、script setup、指令和模板
---

# Markdown 中的 Vue

VitePress markdown 文件被编译为 Vue 单文件组件,启用完整的 Vue 功能。

## 插值

Vue 表达式在 markdown 中工作:

```md
{{ 1 + 1 }}

{{ new Date().toLocaleDateString() }}
```

## 指令

带有 Vue 指令的 HTML:

```md
<span v-for="i in 3">{{ i }}</span>

<div v-if="$frontmatter.showBanner">
  Banner content
</div>
```

## Script 和 Style

在 frontmatter 之后添加 `<script setup>` 和 `<style>`:

```md
---
title: My Page
---

<script setup>
import { ref } from 'vue'
import MyComponent from './MyComponent.vue'

const count = ref(0)
</script>

# {{ $frontmatter.title }}

Count: {{ count }}

<button @click="count++">Increment</button>

<MyComponent />

<style module>
.button {
  color: red;
}
</style>
```

**注意:** 使用 `<style module>` 而不是 `<style scoped>` 以避免页面大小膨胀。

## 导入组件

本地导入(每页代码分割):

```md
<script setup>
import CustomComponent from '../components/CustomComponent.vue'
</script>

<CustomComponent />
```

## 全局组件

在主题中注册以随处使用:

```ts
// .vitepress/theme/index.ts
import DefaultTheme from 'vitepress/theme'
import MyGlobalComponent from './MyGlobalComponent.vue'

export default {
  extends: DefaultTheme,
  enhanceApp({ app }) {
    app.component('MyGlobalComponent', MyGlobalComponent)
  }
}
```

然后在任何 markdown 中使用:

```md
<MyGlobalComponent />
```

**重要:** 组件名称必须包含连字符或为 PascalCase,以避免被视为内联 HTML 元素。

## 运行时 API

访问 VitePress 数据:

```md
<script setup>
import { useData, useRoute, useRouter } from 'vitepress'

const { page, frontmatter, theme, site } = useData()
const route = useRoute()
const router = useRouter()
</script>

Current page: {{ page.relativePath }}
```

## 全局变量

无需导入即可使用:

```md
# {{ $frontmatter.title }}

Params: {{ $params.id }}
```

## 标题中的组件

```md
# My Title <Badge type="tip" text="v2.0" />
```

## 转义 Vue 语法

防止 Vue 插值:

```md
<span v-pre>{{ will be displayed as-is }}</span>
```

或使用容器:

```md
::: v-pre
{{ this won't be processed }}
:::
```

## 代码块中的 Vue

使用 `-vue` 后缀在围栏代码中启用 Vue 处理:

````md
```js-vue
Hello {{ 1 + 1 }}
```
````

## CSS 预处理器

开箱即用支持(安装预处理器):

```bash
npm install -D sass  # for .scss/.sass
npm install -D less  # for .less
npm install -D stylus # for .styl/.stylus
```

```vue
<style lang="scss">
.title {
  font-size: 20px;
}
</style>
```

## 使用 Teleports

仅使用 SSG 传送到 body:

```md
<ClientOnly>
  <Teleport to="#modal">
    <div>Modal content</div>
  </Teleport>
</ClientOnly>
```

## VS Code IntelliSense

为 `.md` 文件启用 Vue 语言功能:

```json
// tsconfig.json
{
  "include": ["docs/**/*.ts", "docs/**/*.vue", "docs/**/*.md"],
  "vueCompilerOptions": {
    "vitePressExtensions": [".md"]
  }
}
```

```json
// .vscode/settings.json
{
  "vue.server.includeLanguages": ["vue", "markdown"]
}
```

## 关键点

- Markdown 文件是 Vue SFC - 使用 `<script setup>` 和 `<style>`
- 通过 `useData()` 或 `$frontmatter` 全局访问页面数据
- 本地导入组件或在主题中全局注册
- 使用 `<style module>` 而不是 `<style scoped>`
- 用 `<ClientOnly>` 包装非 SSR 组件
- 组件名称必须是 PascalCase 或包含连字符

<!--
Source references:
- https://vitepress.dev/guide/using-vue
- https://vitepress.dev/reference/runtime-api
-->
