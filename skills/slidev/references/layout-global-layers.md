---
name: global-layers
description: 创建在幻灯片之间持久化的组件，如页脚和背景
---

# 全局图层

创建在幻灯片之间持久化的组件。

## 图层文件

在项目根目录中创建：
- `global-top.vue` - 在所有幻灯片之上（单个实例）
- `global-bottom.vue` - 在所有幻灯片之下（单个实例）
- `slide-top.vue` - 在每张幻灯片之上（每张幻灯片实例）
- `slide-bottom.vue` - 在每张幻灯片之下（每张幻灯片实例）
- `custom-nav-controls.vue` - 自定义导航控件

## Z 顺序（从上到下）

1. NavControls / custom-nav-controls.vue
2. global-top.vue
3. slide-top.vue
4. 幻灯片内容
5. slide-bottom.vue
6. global-bottom.vue

## 示例：页脚

```html
<!-- global-bottom.vue -->
<template>
  <footer class="absolute bottom-0 left-0 right-0 p-2">您的姓名</footer>
</template>
```

## 条件渲染

```html
<!-- 在封面布局上隐藏 -->
<template>
  <footer v-if="$nav.currentLayout !== 'cover'" class="absolute bottom-0 p-2">
    {{ $nav.currentPage }} / {{ $nav.total }}
  </footer>
</template>
```

## 导出说明

当全局图层依赖于导航状态时，使用 `--per-slide` 导出选项。
