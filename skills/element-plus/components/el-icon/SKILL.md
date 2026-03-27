---
name: "el-icon"
description: "用于显示 SVG 图标图标组件。当用户需要显示图标、自定义图标大小和颜色使用 Element Plus 图标集合时调用---

# Icon 组件

Icon 组件提供具有可自定义大小和颜色 SVG 图标
## 何时使用

- UI 图标
- 按钮图标
- 状态指示器
- 视觉元素

## 安装

```bash
npm install @element-plus/icons-vue
```

## 基本用法

```vue
<template>
  <el-icon :size="30" :color="color">
    <Edit />
  </el-icon>
</template>

<script setup>
import { ref } from 'vue'
import { Edit } from '@element-plus/icons-vue'

const color = ref('#409EFC')
</script>
```

## 与组件结
```vue
<template>
  <el-button type="primary">
    <el-icon style="vertical-align: middle;">
      <Search />
    </el-icon>
    <span style="vertical-align: middle;">Search</span>
  </el-button>
</template>

<script setup>
import { Search } from '@element-plus/icons-vue'
</script>
```

## 加载图标

```vue
<template>
  <el-icon class="is-loading">
    <Loading />
  </el-icon>
</template>

<script setup>
import { Loading } from '@element-plus/icons-vue'
</script>
```

## 直接使用 SVG

```vue
<template>
  <Edit style="width: 1em; height: 1em; margin-right: 8px;" />
  <Share style="width: 1em; height: 1em; margin-right: 8px;" />
  <Delete style="width: 1em; height: 1em; margin-right: 8px;" />
</template>

<script setup>
import { Edit, Share, Delete } from '@element-plus/icons-vue'
</script>
```

## 全局注册

```ts
// main.ts
import * as ElementPlusIconsVue from '@element-plus/icons-vue'

const app = createApp(App)
for (const [key, component] of Object.entries(ElementPlusIconsVue)) {
  app.component(key, component)
}
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| color | SVG 填充颜色 | `string` | 从父级继|
| size | 图标大小 | `number \| string` | 从字体大小继|

### 插槽

| Name | Description |
|------|-------------|
| default | SVG 图标组件 |

## 图标集合

Element Plus 提供 300+ 图标,包括:
- 操作图标: Edit, Delete, Share, Download, Upload
- 箭头图标: ArrowLeft, ArrowRight, ArrowUp, ArrowDown
- 状态图 SuccessFilled, WarningFilled, CircleCloseFilled, InfoFilled
- 媒体图标: Picture, VideoCamera, Microphone
- 以及更多...

## 最佳实践
1. 使用 `el-icon` 包装器以获得一致尺寸
2. 添加 `is-loading` 类用于加载动3. 单独导入图标用于 tree-shaking
