---
name: "el-segmented"
description: "用于从多个选项中选择单个选项分段组件。当用户需要创建单选样式按钮组、类似选项卡控件选项开关时调用---

# Segmented 组件

Segmented 显示多个选项并允许用户选择单个选项
## 何时使用

- 单选样式选择
- 类似选项卡控件
- 选项开- 视图模式切换

## 基本用法

```vue
<template>
  <el-segmented v-model="value" :options="options" />
</template>

<script setup>
import { ref } from 'vue'

const value = ref('Mon')
const options = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
</script>
```

## 方向

```vue
<template>
  <el-segmented v-model="value" :options="options" direction="vertical" />
</template>
```

## 禁用

```vue
<template>
  <el-segmented v-model="value" :options="options" disabled />
</template>
```

## 块级

```vue
<template>
  <el-segmented v-model="value" :options="options" block />
</template>
```

## 自定义内容
```vue
<template>
  <el-segmented v-model="value" :options="options">
    <template #default="{ item }">
      <div class="flex items-center gap-2">
        <el-icon><component :is="item.icon" /></el-icon>
        <span>{{ item.label }}</span>
      </div>
    </template>
  </el-segmented>
</template>

<script setup>
import { ref } from 'vue'

const value = ref('list')
const options = [
  { label: 'List', value: 'list', icon: 'List' },
  { label: 'Grid', value: 'grid', icon: 'Grid' }
]
</script>
```

## 自定义属性
```vue
<template>
  <el-segmented
    v-model="value"
    :options="options"
    :props="{ label: 'name', value: 'id' }"
  />
</template>

<script setup>
import { ref } from 'vue'

const value = ref(1)
const options = [
  { id: 1, name: 'Option 1' },
  { id: 2, name: 'Option 2' }
]
</script>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定| `string \| number \| boolean` | |
| options | 选项数据 | `Option[]` | `[]` |
| props | 配置选项 | `object` | |
| size | 尺寸 | `'' \| 'large' \| 'default' \| 'small'` | `''` |
| block | 适应父级宽度 | `boolean` | `false` |
| disabled | 禁用 | `boolean` | `false` |
| direction | 显示方向 | `'horizontal' \| 'vertical'` | `'horizontal'` |

### Props 配置

| Name | Description | Type | Default |
|------|-------------|------|---------|
| value | 值键 | `string` | `'value'` |
| label | 标签| `string` | `'label'` |
| disabled | 禁用| `string` | `'disabled'` |

### 事件

| Name | Description | Type |
|------|-------------|------|
| change | 值改| `(val: any) => void` |

### 插槽

| Name | Description | Type |
|------|-------------|------|
| default | 选项渲染| `{ item: Option }` |

## 最佳实践
1. 使用 `block` 用于全宽布局
2. 使用 `direction="vertical"` 用于有限水平空3. 使用自定义内容用于丰富选项显示
