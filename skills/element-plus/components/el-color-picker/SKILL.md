---
name: "el-color-picker"
description: "用于颜色选择颜色选择器组件。当用户需要选择颜色、设置主题颜色自定义颜色值时调用---

# ColorPicker 组件

ColorPicker 是一个颜色选择支持多种颜色格式
## 何时使用

- 主题自定- 颜色选择
- 设计工具
- 样式配置

## 基本用法

```vue
<template>
  <el-color-picker v-model="color" />
</template>

<script setup>
import { ref } from 'vue'

const color = ref('#409EFF')
</script>
```

## Alpha 通道

```vue
<template>
  <el-color-picker v-model="color" show-alpha />
</template>

<script setup>
import { ref } from 'vue'

const color = ref('rgba(19, 206, 102, 0.8)')
</script>
```

## 预定义颜色
```vue
<template>
  <el-color-picker
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

## 尺寸

```vue
<template>
  <el-color-picker v-model="color" size="large" />
  <el-color-picker v-model="color" />
  <el-color-picker v-model="color" size="small" />
</template>
```

## 颜色格式

```vue
<template>
  <el-color-picker v-model="color" color-format="rgb" />
</template>

<script setup>
import { ref } from 'vue'

const color = ref('rgb(64, 158, 255)')
</script>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定| `string` | |
| disabled | 禁用 | `boolean` | `false` |
| clearable | 显示清除按钮 | `boolean` | `true` |
| size | 尺寸 | `'large' \| 'default' \| 'small'` | |
| show-alpha | 显示 alpha 滑块 | `boolean` | `false` |
| color-format | 颜色格式 | `'rgb' \| 'prgb' \| 'hex' \| 'hex3' \| 'hex4' \| 'hex6' \| 'hex8' \| 'name' \| 'hsl' \| 'hsv'` | `'hex'` `'rgb'` |
| predefine | 预定义颜色| `string[]` | |
| validate-event | 触发表单验证 | `boolean` | `true` |

### 事件

| Name | Description | Type |
|------|-------------|------|
| change | 值改| `(value: string) => void` |
| active-change | 激活颜色改| `(value: string) => void` |
| focus | 组件聚焦 | `(event: FocusEvent) => void` |
| blur | 组件失焦 | `(event: FocusEvent) => void` |
| clear | 清除按钮点击 | `() => void` |

### 暴露

| Name | Description | Type |
|------|-------------|------|
| color | 当前颜色对象 | `Color` |
| show | 显示选择| `() => void` |
| hide | 隐藏选择| `() => void` |
| focus | 聚焦选择| `() => void` |
| blur | 失焦选择| `() => void` |

## 最佳实践
1. 使用 `show-alpha` 用于透明颜色
2. 使用 `predefine` 用于品牌颜色
3. 使用 `color-format` 用于特定格式需要