---
name: "el-slider"
description: "用于在固定范围内拖动滑块组件。当用户需要在范围内选择数值、设置音亮度按价格范围过滤时调用---

# Slider 组件

Slider 组件允许用户在固定范围内拖动滑块来选择值
## 何时使用

- 数值范围选择
- 音量/亮度控制
- 价格范围过滤- 进度指示
## 基本用法

```vue
<template>
  <el-slider v-model="value" />
</template>

<script setup>
import { ref } from 'vue'

const value = ref(50)
</script>
```

## 离散
```vue
<template>
  <el-slider v-model="value" :step="10" show-stops />
</template>
```

## 带输入框

```vue
<template>
  <el-slider v-model="value" show-input />
</template>
```

## 范围选择

```vue
<template>
  <el-slider v-model="range" range :max="1000" />
</template>

<script setup>
import { ref } from 'vue'

const range = ref([100, 500])
</script>
```

## 垂直模式

```vue
<template>
  <el-slider v-model="value" vertical height="200px" />
</template>
```

## 显示标记

```vue
<template>
  <el-slider v-model="value" :marks="marks" />
</template>

<script setup>
import { ref } from 'vue'

const value = ref(30)
const marks = {
  0: '0°C',
  8: '8°C',
  37: '37°C',
  100: '100°C'
}
</script>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定| `number \| number[]` | `0` |
| min | 最小| `number` | `0` |
| max | 最大| `number` | `100` |
| disabled | 是否禁用 | `boolean` | `false` |
| step | 步长 | `number \| 'mark'` | `1` |
| show-input | 显示输入| `boolean` | `false` |
| show-input-controls | 显示输入控件 | `boolean` | `true` |
| size | 尺寸 | `'' \| 'large' \| 'default' \| 'small'` | `'default'` |
| show-stops | 显示断点 | `boolean` | `false` |
| show-tooltip | 显示工具提示 | `boolean` | `true` |
| range | 启用范围模式 | `boolean` | `false` |
| vertical | 垂直模式 | `boolean` | `false` |
| height | 高度(垂直模式) | `string` | |
| marks | 标记 | `SliderMarks` | |
| placement | 工具提示位置 | Placement enum | `'top'` |

### 事件

| Name | Description | Type |
|------|-------------|------|
| change | 值改变时触发 | `(value: number \| number[]) => void` |
| input | 拖动时触| `(value: number \| number[]) => void` |

## 最佳实践
1. 使用 `marks` 用于标记间隔
2. 使用 `range` 用于最最大选择
3. 使用 `step="mark"` 限制为标记