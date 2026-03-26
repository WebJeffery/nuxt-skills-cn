---
name: global-context
description: 以编程方式访问导航、幻灯片信息和配置
---

# 全局上下文与 API

以编程方式访问导航、幻灯片信息和配置。

## 模板变量

在幻灯片和组件中可用：

```md
第 {{ $page }} 页，共 {{ $nav.total }} 页
标题：{{ $slidev.configs.title }}
```

### $nav

导航状态和控制：

| 属性 | 类型 | 描述 |
|----------|------|-------------|
| `$nav.currentPage` | number | 当前页（从 1 开始） |
| `$nav.currentLayout` | string | 当前布局名称 |
| `$nav.total` | number | 总幻灯片数 |
| `$nav.isPresenter` | boolean | 是否在演讲者模式 |
| `$nav.next()` | function | 下一次点击/幻灯片 |
| `$nav.prev()` | function | 上一次点击/幻灯片 |
| `$nav.nextSlide()` | function | 下一张幻灯片 |
| `$nav.prevSlide()` | function | 上一张幻灯片 |
| `$nav.go(n)` | function | 跳转到第 n 张幻灯片 |

### $slidev

全局上下文：

| 属性 | 描述 |
|----------|-------------|
| `$slidev.configs` | 项目配置（标题等） |
| `$slidev.themeConfigs` | 主题配置 |

### $frontmatter

当前幻灯片的 frontmatter：

```md
布局：{{ $frontmatter.layout }}
```

### $clicks

当前幻灯片上的点击次数。

### $page

当前页码（从 1 开始）。

### $renderContext

当前渲染上下文：
- `'slide'` - 正常幻灯片视图
- `'overview'` - 概览模式
- `'presenter'` - 演讲者模式
- `'previewNext'` - 下一张幻灯片预览

## Composables

从 `@slidev/client` 导入：

```ts
import {
  useNav,
  useDarkMode,
  useIsSlideActive,
  useSlideContext,
  onSlideEnter,
  onSlideLeave,
} from '@slidev/client'
```

### useNav

```ts
const nav = useNav()
nav.next()
nav.go(5)
console.log(nav.currentPage)
```

### useDarkMode

```ts
const { isDark, toggle } = useDarkMode()
```

### useIsSlideActive

```ts
const isActive = useIsSlideActive()
// 返回 ref<boolean>
```

### useSlideContext

```ts
const { $page, $clicks, $frontmatter } = useSlideContext()
```

## 生命周期钩子

```ts
import { onSlideEnter, onSlideLeave } from '@slidev/client'

onSlideEnter(() => {
  // 幻灯片变为活动状态
  startAnimation()
})

onSlideLeave(() => {
  // 幻灯片变为非活动状态
  cleanup()
})
```

**重要**：不要在幻灯片中使用 `onMounted`/`onUnmounted` - 组件实例会持久存在。请改用 `onSlideEnter`/`onSlideLeave`。

## 条件渲染示例

```html
<!-- 仅在演讲者模式显示 -->
<div v-if="$nav.isPresenter">
  演讲者备注
</div>

<!-- 在封面幻灯片上隐藏 -->
<footer v-if="$nav.currentLayout !== 'cover'">
  第 {{ $nav.currentPage }} 页
</footer>

<!-- 根据上下文显示不同内容 -->
<template v-if="$renderContext === 'slide'">
  正常视图
</template>
<template v-else-if="$renderContext === 'presenter'">
  演讲者视图
</template>
```

## 类型导入

```ts
import type { TocItem } from '@slidev/types'
```
