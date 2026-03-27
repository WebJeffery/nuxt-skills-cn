---
name: "el-transfer"
description: "用于双列列表选择穿梭组件。当用户需要在两个列表之间移动项目、从大型数据集中选择多个项目管理权限时调用---

# Transfer 组件

Transfer 提供双列列表选择,用于在列表之间移动项目
## 何时使用

- 权限管理
- 从大型数据集中多- 项目分类
- 团队成员选择

## 基本用法

```vue
<template>
  <el-transfer
    v-model="value"
    :data="data"
    :titles="['Source', 'Target']"
  />
</template>

<script setup>
import { ref } from 'vue'

const value = ref([1, 4])
const data = ref(
  Array.from({ length: 15 }).map((_, index) => ({
    key: index + 1,
    label: `Option ${index + 1}`,
    disabled: index % 4 === 0
  }))
)
</script>
```

## 可搜
```vue
<template>
  <el-transfer
    v-model="value"
    :data="data"
    filterable
    filter-placeholder="Search..."
  />
</template>
```

## 可自定义

```vue
<template>
  <el-transfer
    v-model="value"
    :data="data"
    :titles="['Available', 'Selected']"
    :button-texts="['Remove', 'Add']"
    :render-content="renderFunc"
  />
</template>

<script setup>
const renderFunc = (h, option) => {
  return h('span', null, `${option.key} - ${option.label}`)
}
</script>
```

## 属性别
```vue
<template>
  <el-transfer
    v-model="value"
    :data="data"
    :props="{
      key: 'id',
      label: 'name'
    }"
  />
</template>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定| `array` | `[]` |
| data | 数据| `array` | `[]` |
| filterable | 启用搜索 | `boolean` | `false` |
| filter-placeholder | 搜索占位| `string` | `'Search'` |
| filter-method | 自定义搜索方| `(query, item) => boolean` | |
| target-order | 目标列表顺| `'original' \| 'push' \| 'unshift'` | `'original'` |
| titles | 自定义标签| `string[]` | `['List 1', 'List 2']` |
| button-texts | 自定义按钮文| `string[]` | `[]` |
| render-content | 自定义渲染函| `(h, option) => VNode` | |
| format | 显示格式 | `object` | |
| props | 属性别| `object` | |
| left-default-checked | 左侧默认选中 | `array` | `[]` |
| right-default-checked | 右侧默认选中 | `array` | `[]` |
| left-checked | 左侧选中 | `array` | |
| right-checked | 右侧选中 | `array` | |

### 事件

| Name | Description | Type |
|------|-------------|------|
| change | 值改变时触发 | `(value, direction, movedKeys) => void` |
| left-check-change | 左侧列表选择变化 | `(value, movedKeys) => void` |
| right-check-change | 右侧列表选择变化 | `(value, movedKeys) => void` |

### 插槽

| Name | Description | Type |
|------|-------------|------|
| left-footer | 左侧列表页脚 | |
| right-footer | 右侧列表页脚 | |
| | 自定义项目内容| `{ option }` |

## 最佳实践
1. 使用 `filterable` 用于大型数据2. 使用 `render-content` 自定义丰富显3. 使用 `props` 映射自定义数据键
