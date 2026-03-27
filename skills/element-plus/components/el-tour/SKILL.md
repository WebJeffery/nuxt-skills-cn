---
name: "el-tour"
description: "用于引导用户了解产品导览组件。当用户需要创建产品导览、入门指南功能演练时调用---

# Tour 组件

Tour 是一个弹出组件用于通过分步说明引导用户了解产品
## 何时使用

- 产品入门
- 功能导览
- 用户指导
- 教程演练

## 基本用法

```vue
<template>
  <el-button ref="buttonRef">Start Tour</el-button>
  
  <el-tour v-model="open">
    <el-tour-step
      :target="buttonRef?.$el"
      title="Start Tour"
      description="Click this button to start the tour"
    />
    <el-tour-step
      :target="inputRef?.$el"
      title="Input Field"
      description="This is an input field"
    />
  </el-tour>
</template>

<script setup>
import { ref } from 'vue'

const open = ref(false)
const buttonRef = ref()
const inputRef = ref()
</script>
```

## 非模
```vue
<template>
  <el-tour v-model="open" :mask="false" type="primary">
    <el-tour-step
      :target="targetRef"
      title="Non-modal Tour"
      description="This tour doesn't block interactions"
    />
  </el-tour>
</template>
```

## 自定义位
```vue
<template>
  <el-tour v-model="open">
    <el-tour-step
      :target="targetRef"
      placement="right"
      title="Right Placement"
      description="The guide appears on the right"
    />
  </el-tour>
</template>
```

## 自定义遮罩样式
```vue
<template>
  <el-tour
    v-model="open"
    :mask="{
      style: { boxShadow: '0 0 10px rgba(0,0,0,0.3)' },
      color: 'rgba(0, 0, 0, 0.5)'
    }"
  >
    <el-tour-step :target="targetRef" title="Custom Mask" />
  </el-tour>
</template>
```

## API 参考
### Tour 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 打开导览 | `boolean` | `false` |
| current / v-model:current | 当前步骤 | `number` | `0` |
| placement | 默认位置 | Placement enum | `'bottom'` |
| mask | 遮罩配置 | `boolean \| { style?, color? }` | `true` |
| type | 导览类型 | `'default' \| 'primary'` | `'default'` |
| show-arrow | 显示箭头 | `boolean` | `true` |
| show-close | 显示关闭按钮 | `boolean` | `true` |
| close-on-press-escape | ESC 关闭 | `boolean` | `true` |
| scroll-into-view-options | 滚动选项 | `boolean \| ScrollIntoViewOptions` | `{ block: 'center' }` |
| z-index | Z-index | `number` | `2001` |

### Tour 事件

| Name | Description | Type |
|------|-------------|------|
| close | 导览关闭 | `(current: number) => void` |
| finish | 导览完成 | `() => void` |
| change | 步骤改变 | `(current: number) => void` |

### Tour 插槽

| Name | Description | Type |
|------|-------------|------|
| default | TourStep 组件 | |
| indicators | 自定义指示器 | `{ current, total }` |

### TourStep 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| target | 目标元素 | `HTMLElement \| string \| () => HTMLElement` | |
| title | 步骤标题 | `string` | |
| description | 步骤描述 | `string` | |
| placement | 步骤位置 | Placement enum | `'bottom'` |
| show-arrow | 显示箭头 | `boolean` | |
| show-close | 显示关闭按钮 | `boolean` | |
| next-button-props | 下一步按钮属性| `{ children, onClick }` | |
| prev-button-props | 上一步按钮属性| `{ children, onClick }` | |

### TourStep 插槽

| Name | Description |
|------|-------------|
| default | 自定义描|
| header | 自定义页|

## 最佳实践
1. 使用 `mask="false"` 用于非阻塞导2. 使用 `type="primary"` 用于强调
3. 提供清晰步骤描