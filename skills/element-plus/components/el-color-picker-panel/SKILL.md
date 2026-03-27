---
name: "el-color-picker-panel"
description: "ColorPickerPanel 是 ColorPicker 核心面板组件，用于自定义颜色选择 UI。当用户需要构建自定义颜色选择器界面而不带下拉包装器时调用。"
---

# ColorPickerPanel 组件

ColorPickerPanel 是 ColorPicker 核心组件，用于构建自定义颜色选择界面。

## 何时使用

- 自定义颜色选择器 UI
- 嵌入式颜色选择
- 高级颜色工具
- 自定义下拉实现

## 基本用法

```vue
<template>
  <el-color-picker-panel v-model="color" />
</template>

<script setup>
import { ref } from 'vue'

const color = ref('#409EFF')
</script>
```

## Alpha 通道

```vue
<template>
  <el-color-picker-panel v-model="color" show-alpha />
</template>

<script setup>
import { ref } from 'vue'

const color = ref('rgba(64, 158, 255, 0.8)')
</script>
```

## 预定义颜色

```vue
<template>
  <el-color-picker-panel
    v-model="color"
    :predefine="predefineColors"
  />
</template>

<script setup>
import { ref } from 'vue'

const color = ref('#409EFF')
const predefineColors = [
  '#ff4500',
  '#ff8c00',
  '#ffd700',
  '#90ee90',
  '#00ced1',
  '#1e90ff',
  '#c71585'
]
</script>
```

## 无边框

```vue
<template>
  <el-color-picker-panel v-model="color" :border="false" />
</template>
```

## 禁用

```vue
<template>
  <el-color-picker-panel v-model="color" disabled />
</template>
```

## API 参考

### 属性

| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定值 | `string` | - |
| border | 显示边框 | `boolean` | `true` |
| disabled | 禁用 | `boolean` | `false` |
| show-alpha | 显示 alpha 滑块 | `boolean` | `false` |
| color-format | 颜色格式 | `'rgb' \| 'prgb' \| 'hex' \| 'hex3' \| 'hex4' \| 'hex6' \| 'hex8' \| 'name' \| 'hsl' \| 'hsv'` | `'hex'` 或 `'rgb'` |
| predefine | 预定义颜色 | `string[]` | - |
| validate-event | 触发表单验证 | `boolean` | `true` |

### 插槽

| Name | Description |
|------|-------------|
| footer | 面板后内容 |

### 暴露

| Name | Description | Type |
|------|-------------|------|
| color | 当前颜色对象 | `Color` |
| inputRef | 输入引用 | `InputInstance` |
| update | 更新子组件 | `() => void` |

## 最佳实践

1. 用于自定义颜色选择器实现
2. 与 Popover 结合实现下拉行为
3. 使用 `predefine` 用于品牌颜色
