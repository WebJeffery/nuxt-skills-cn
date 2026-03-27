---
name: "el-table-v2"
description: "用于高效渲染大量数据虚拟化表格组件。当用户需要显示大型数据集并使用虚拟滚动以提高性能时调用。"
---

# 虚拟化表格(TableV2)组件

TableV2 提供虚拟化表格，用于高效渲染大量数据。

## 何时使用

- 大型数据集(1000+ 行)
- 性能关键表格
- 需要虚拟滚动
- 实现时数据更新

## 基本用法

```vue
<template>
  <el-table-v2
    :columns="columns"
    :data="data"
    :width="700"
    :height="400"
  />
</template>

<script setup>
const columns = [
  { key: 'id', title: 'ID', width: 100 },
  { key: 'name', title: '名称', width: 200 },
  { key: 'email', title: '邮箱', width: 300 }
]

const data = Array.from({ length: 1000 }).map((_, idx) => ({
  id: idx,
  name: `用户 ${idx}`,
  email: `user${idx}@example.com`
}))
</script>
```

## 自动调整大小

```vue
<template>
  <el-auto-resizer>
    <template #default="{ height, width }">
      <el-table-v2
        :columns="columns"
        :data="data"
        :width="width"
        :height="height"
      />
    </template>
  </el-auto-resizer>
</template>
```

## 自定义单元格渲染器

```vue
<template>
  <el-table-v2
    :columns="columns"
    :data="data"
    :width="700"
    :height="400"
  />
</template>

<script setup>
import { h } from 'vue'
import { ElButton } from 'element-plus'

const columns = [
  { key: 'id', title: 'ID', width: 100 },
  {
    key: 'action',
    title: '操作',
    width: 150,
    cellRenderer: ({ rowData }) => h(ElButton, { size: 'small' }, () => '编辑')
  }
]

const data = Array.from({ length: 100 }).map((_, idx) => ({
  id: idx
}))
</script>
```

## 固定列

```vue
<template>
  <el-table-v2
    :columns="columns"
    :data="data"
    :width="700"
    :height="400"
  />
</template>

<script setup>
const columns = [
  { key: 'id', title: 'ID', width: 100, fixed: 'left' },
  { key: 'name', title: '名称', width: 200 },
  { key: 'email', title: '邮箱', width: 300 },
  { key: 'action', title: '操作', width: 100, fixed: 'right' }
]

const data = Array.from({ length: 100 }).map((_, idx) => ({
  id: idx,
  name: `用户 ${idx}`,
  email: `user${idx}@example.com`
}))
</script>
```

## 选择

```vue
<template>
  <el-table-v2
    :columns="columns"
    :data="data"
    :width="700"
    :height="400"
  />
</template>

<script setup>
import { h, ref } from 'vue'
import { ElCheckbox } from 'element-plus'

const selectedIds = ref(new Set())

const columns = [
  {
    key: 'selection',
    width: 50,
    cellRenderer: ({ rowData }) => h(ElCheckbox, {
      modelValue: selectedIds.value.has(rowData.id),
      onChange: (val) => {
        if (val) selectedIds.value.add(rowData.id)
        else selectedIds.value.delete(rowData.id)
      }
    })
  },
  { key: 'name', title: '名称', width: 200 }
]

const data = Array.from({ length: 100 }).map((_, idx) => ({
  id: idx,
  name: `用户 ${idx}`
}))
</script>
```

## API 参考

### TableV2 属性

| Name | Description | Type | Default |
|------|-------------|------|---------|
| columns | 列定义 | `Column[]` | - |
| data | 表格数据 | `Data[]` | `[]` |
| width | 表格宽度 | `number` | - |
| height | 表格高度 | `number` | - |
| row-height | 行高 | `number` | `50` |
| header-height | 表头高度 | `number \| number[]` | `50` |
| row-key | 行键 | `string \| Symbol \| number` | `'id'` |
| fixed | 固定列宽 | `boolean` | `false` |
| cache | 预渲染行数 | `number` | `2` |
| estimated-row-height | 动态行高估计行高 | `number` | - |

### 列属性

| Name | Description | Type | Default |
|------|-------------|------|---------|
| key | 唯一键 | `KeyType` | - |
| title | 表头标题 | `string` | - |
| width | 列宽 | `number` | - |
| align | 内容对齐 | `'left' \| 'center' \| 'right'` | `'left'` |
| fixed | 固定方向 | `boolean \| 'left' \| 'right'` | `false` |
| sortable | 可排序 | `boolean` | `false` |
| cellRenderer | 自定义单元格渲染器 | `Component \| (props) => VNode` | - |
| headerCellRenderer | 自定义表头渲染器 | `Component \| (props) => VNode` | - |

### 事件

| Name | Description | Type |
|------|-------------|------|
| column-sort | 列排序 | `(params) => void` |
| scroll | 滚动事件 | `(params) => void` |
| end-reached | 到达末尾 | `(remainDistance) => void` |
| rows-rendered | 行渲染 | `(params) => void` |

### 暴露

| Name | Description | Type |
|------|-------------|------|
| scrollTo | 滚动到位置 | `(params) => void` |
| scrollToRow | 滚动到行 | `(row, strategy?) => void` |

## 最佳实践

1. 用于具有 1000+ 行数据集
2. 使用 `estimated-row-height` 用于动态高度
3. 使用 `cache` 获得更平滑滚动
