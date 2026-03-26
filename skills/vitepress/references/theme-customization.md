---
name: extending-vitepress-default-theme
description: 自定义 CSS 变量、使用布局插槽、注册全局组件和覆盖主题字体
---

# 扩展默认主题

通过 CSS、插槽和 Vue 组件自定义默认主题。

## 主题入口文件

创建 `.vitepress/theme/index.ts` 以扩展默认主题:

```ts
// .vitepress/theme/index.ts
import DefaultTheme from 'vitepress/theme'
import './custom.css'

export default DefaultTheme
```

## CSS 变量

覆盖根 CSS 变量:

```css
/* .vitepress/theme/custom.css */
:root {
  /* 品牌颜色 */
  --vp-c-brand-1: #646cff;
  --vp-c-brand-2: #747bff;
  --vp-c-brand-3: #9499ff;
  
  /* 背景 */
  --vp-c-bg: #ffffff;
  --vp-c-bg-soft: #f6f6f7;
  
  /* 文本 */
  --vp-c-text-1: #213547;
  --vp-c-text-2: #476582;
}

.dark {
  --vp-c-brand-1: #747bff;
  --vp-c-bg: #1a1a1a;
}
```

参见[所有 CSS 变量](https://github.com/vuejs/vitepress/blob/main/src/client/theme-default/styles/vars.css)。

## 主页英雄自定义

```css
:root {
  /* 渐变名称颜色 */
  --vp-home-hero-name-color: transparent;
  --vp-home-hero-name-background: linear-gradient(120deg, #bd34fe, #41d1ff);
  
  /* 英雄图像发光 */
  --vp-home-hero-image-background-image: linear-gradient(-45deg, #bd34fe 50%, #47caff 50%);
  --vp-home-hero-image-filter: blur(44px);
}
```

## 自定义字体

删除 Inter 字体并使用您自己的:

```ts
// .vitepress/theme/index.ts
import DefaultTheme from 'vitepress/theme-without-fonts'
import './fonts.css'

export default DefaultTheme
```

```css
/* .vitepress/theme/fonts.css */
@font-face {
  font-family: 'MyFont';
  src: url('/fonts/myfont.woff2') format('woff2');
}

:root {
  --vp-font-family-base: 'MyFont', sans-serif;
  --vp-font-family-mono: 'Fira Code', monospace;
}
```

在配置中预加载字体:

```ts
// .vitepress/config.ts
export default {
  transformHead({ assets }) {
    const fontFile = assets.find(file => /myfont\.[\w-]+\.woff2/.test(file))
    if (fontFile) {
      return [
        ['link', { rel: 'preload', href: fontFile, as: 'font', type: 'font/woff2', crossorigin: '' }]
      ]
    }
  }
}
```

## 全局组件

注册在所有 markdown 中可用的组件:

```ts
// .vitepress/theme/index.ts
import DefaultTheme from 'vitepress/theme'
import MyComponent from './components/MyComponent.vue'

export default {
  extends: DefaultTheme,
  enhanceApp({ app }) {
    app.component('MyComponent', MyComponent)
  }
}
```

在 markdown 中使用:

```md
<MyComponent :prop="value" />
```

## 布局插槽

将内容注入到特定位置:

```ts
// .vitepress/theme/index.ts
import DefaultTheme from 'vitepress/theme'
import MyLayout from './MyLayout.vue'

export default {
  extends: DefaultTheme,
  Layout: MyLayout
}
```

```vue
<!-- .vitepress/theme/MyLayout.vue -->
<script setup>
import DefaultTheme from 'vitepress/theme'
const { Layout } = DefaultTheme
</script>

<template>
  <Layout>
    <template #aside-outline-before>
      <div>Above outline</div>
    </template>
    
    <template #doc-before>
      <div>Before doc content</div>
    </template>
    
    <template #doc-after>
      <div>After doc content</div>
    </template>
  </Layout>
</template>
```

### 可用插槽

**文档布局(`layout: doc`):**
- `doc-top`, `doc-bottom`
- `doc-before`, `doc-after`
- `doc-footer-before`
- `sidebar-nav-before`, `sidebar-nav-after`
- `aside-top`, `aside-bottom`
- `aside-outline-before`, `aside-outline-after`
- `aside-ads-before`, `aside-ads-after`

**主页布局(`layout: home`):**
- `home-hero-before`, `home-hero-after`
- `home-hero-info-before`, `home-hero-info`, `home-hero-info-after`
- `home-hero-actions-after`, `home-hero-image`
- `home-features-before`, `home-features-after`

**页面布局(`layout: page`):**
- `page-top`, `page-bottom`

**始终可用:**
- `layout-top`, `layout-bottom`
- `nav-bar-title-before`, `nav-bar-title-after`
- `nav-bar-content-before`, `nav-bar-content-after`
- `not-found` (404 页面)

## 使用渲染函数

模板插槽的替代方案:

```ts
// .vitepress/theme/index.ts
import { h } from 'vue'
import DefaultTheme from 'vitepress/theme'
import MyComponent from './MyComponent.vue'

export default {
  extends: DefaultTheme,
  Layout() {
    return h(DefaultTheme.Layout, null, {
      'aside-outline-before': () => h(MyComponent)
    })
  }
}
```

## 覆盖内部组件

使用 Vite 别名替换默认主题组件:

```ts
// .vitepress/config.ts
import { fileURLToPath, URL } from 'node:url'

export default {
  vite: {
    resolve: {
      alias: [
        {
          find: /^.*\/VPNavBar\.vue$/,
          replacement: fileURLToPath(
            new URL('./theme/components/CustomNavBar.vue', import.meta.url)
          )
        }
      ]
    }
  }
}
```

## 视图转换

自定义暗色模式切换动画:

```vue
<!-- .vitepress/theme/Layout.vue -->
<script setup>
import { useData } from 'vitepress'
import DefaultTheme from 'vitepress/theme'
import { nextTick, provide } from 'vue'

const { isDark } = useData()

provide('toggle-appearance', async ({ clientX: x, clientY: y }) => {
  if (!document.startViewTransition) {
    isDark.value = !isDark.value
    return
  }

  const clipPath = [
    `circle(0px at ${x}px ${y}px)`,
    `circle(${Math.hypot(Math.max(x, innerWidth - x), Math.max(y, innerHeight - y))}px at ${x}px ${y}px)`
  ]

  await document.startViewTransition(async () => {
    isDark.value = !isDark.value
    await nextTick()
  }).ready

  document.documentElement.animate(
    { clipPath: isDark.value ? clipPath.reverse() : clipPath },
    { duration: 300, easing: 'ease-in', pseudoElement: `::view-transition-${isDark.value ? 'old' : 'new'}(root)` }
  )
})
</script>

<template>
  <DefaultTheme.Layout />
</template>
```

## 关键点

- 导入 `vitepress/theme-without-fonts` 以使用自定义字体
- 使用布局插槽注入内容而无需覆盖组件
- 全局组件在 `enhanceApp` 中注册
- 覆盖 CSS 变量以进行主题化
- 使用 Vite 别名替换内部组件

<!--
Source references:
- https://vitepress.dev/guide/extending-default-theme
-->
