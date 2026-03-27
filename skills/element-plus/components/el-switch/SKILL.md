---
name: "el-switch"
description: "用于在两个对立状态之间切换开关组件。当用户需要启禁用功能、切换设置在开/关状态之间切换时调用---

# Switch 组件

开关组件用于在两个对立状态之间切换
## 何时使用

- 启用/禁用功能
- 切换设置
- 开/关开- 布尔状态切
## 基本用法

```vue
<template>
  <el-switch v-model="value" />
</template>

<script setup>
import { ref } from 'vue'

const value = ref(true)
</script>
```

## 尺寸

```vue
<template>
  <el-switch v-model="value1" size="large" />
  <el-switch v-model="value2" />
  <el-switch v-model="value3" size="small" />
</template>
```

## 文字描述

```vue
<template>
  <el-switch
    v-model="value"
    active-text="Open"
    inactive-text="Close"
  />
  
  <el-switch
    v-model="value"
    active-text="O"
    inactive-text="X"
    inline-prompt
  />
</template>
```

## 自定义图
```vue
<template>
  <el-switch
    v-model="value"
    :active-icon="Check"
    :inactive-icon="Close"
  />
</template>

<script setup>
import { Check, Close } from '@element-plus/icons-vue'
</script>
```

## 扩展值类型
```vue
<template>
  <el-switch
    v-model="value"
    active-value="on"
    inactive-value="off"
  />
</template>

<script setup>
import { ref } from 'vue'

const value = ref('on')
</script>
```

## 禁用

```vue
<template>
  <el-switch v-model="value" disabled />
</template>
```

## 加载
```vue
<template>
  <el-switch v-model="value" loading />
</template>
```

## 阻止切换

```vue
<template>
  <el-switch v-model="value" :before-change="beforeChange" />
</template>

<script setup>
const beforeChange = () => {
  return confirm('Are you sure?')
}
</script>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定| `boolean \| string \| number` | `false` |
| disabled | 是否禁用 | `boolean` | `false` |
| loading | 加载状态| `boolean` | `false` |
| size | 尺寸 | `'' \| 'large' \| 'default' \| 'small'` | `''` |
| width | 宽度 | `number \| string` | `''` |
| inline-prompt | 在圆点内显示文字 | `boolean` | `false` |
| active-icon | 开启时图| `string \| Component` | |
| inactive-icon | 关闭时图标 | `string \| Component` | |
| active-text | 开启时文| `string` | `''` |
| inactive-text | 关闭时文字 | `string` | `''` |
| active-value | 开启时| `boolean \| string \| number` | `true` |
| inactive-value | 关闭时| `boolean \| string \| number` | `false` |
| name | Input name | `string` | `''` |
| validate-event | 触发表单验证 | `boolean` | `true` |
| before-change | 切换前钩子 | `() => Promise<boolean> \| boolean` | |

### 事件

| Name | Description | Type |
|------|-------------|------|
| change | 当值改变时触发 | `(val: boolean \| string \| number) => void` |

### 插槽

| Name | Description |
|------|-------------|
| active-action | 自定义激活操|
| inactive-action | 自定义非激活操|
| active | 自定义激活内容|
| inactive | 自定义非激活内容|

## 最佳实践
1. 使用 `before-change` 实现确认对话2. 使用 `loading` 处理异步操作
3. 使用 `active-value`/`inactive-value` 处理非布尔