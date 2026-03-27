---
name: "el-table"
description: "用于显示数据表格组件支持排序、筛选、选择和分页。当用户需要显示表格数据实现数据网格功能时调用---

# Element Plus Table 组件

显示多个具有相似格式数据。您可以对表格中数据进行排序、筛选和比较
## 何时调用

在以下情况下调用此技能
- 用户需要以表格格式显示数据
- 用户想要实现排序筛- 用户需要行选择(单选多
- 用户想要固定列表头
- 用户需要可展开行树形数- 用户询问表格分页

## 功能特性
- **灵活布局**: 固定表头、固定列、自适应高度
- **行选择**: 单选多- **排序**: 本地远程排- **筛*: 基于列筛- **自定义模*: 自定义单元格和表头内容- **可展开*: 行展开以显示详细内容- **树形数据**: 分层数据显示
- **汇总行**: 自动自定义汇- **单元格合*: 支持行跨度和列跨
## API 参考
### Table 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| data | 表格数据 | `any[]` | [] |
| height | 表格高度 | `string \| number` | |
| max-height | 表格最大高| `string \| number` | |
| stripe | 是否为斑马纹表格 | `boolean` | false |
| border | 是否带有纵向边框 | `boolean` | false |
| size | 表格尺寸 | `'' \| 'large' \| 'default' \| 'small'` | |
| fit | 列宽度是否自撑开 | `boolean` | true |
| show-header | 是否显示表头 | `boolean` | true |
| highlight-current-row | 是否高亮当前| `boolean` | false |
| current-row-key | 当前key | `string \| number` | |
| row-class-name | 行 className | `Function \| string` | |
| row-style | 行 style | `Function \| object` | |
| cell-class-name | 单元格 className | `Function \| string` | |
| cell-style | 单元格 style | `Function \| object` | |
| row-key | 行数据 Key | `Function \| string` | |
| empty-text | 空数据时显示文| `string` | No Data |
| default-expand-all | 是否默认展开所有行 | `boolean` | false |
| expand-row-keys | 展开行 key 数组 | `string[]` | |
| default-sort | 默认排序列和顺| `object` | |
| tooltip-effect | overflow tooltip effect | `'dark' \| 'light'` | dark |
| show-summary | 是否显示合计| `boolean` | false |
| sum-text | 合计行第一列文本 | `string` | Sum |
| summary-method | 自定义合计方| `Function` | |
| span-method | 合并行列方法 | `Function` | |
| select-on-indeterminate | 控制全选复选框行为 | `boolean` | true |
| indent | 树形数据缩| `number` | 16 |
| lazy | 是否懒加载子节点数据 | `boolean` | false |
| load | 加载子节点数据方法 | `Function` | |
| tree-props | 渲染嵌套数据配置选项 | `object` | |
| table-layout | 表格布局算法 | `'fixed' \| 'auto'` | fixed |
| scrollbar-always-on | 总是显示滚动| `boolean` | false |

### Table 事件

| Name | Description | Type |
|------|-------------|------|
| select | 当用户手动勾选数据行Checkbox 时触发事件 | `(selection, row) => void` |
| select-all | 当用户手动勾选全Checkbox 时触发事件 | `(selection) => void` |
| selection-change | 当选择项发生变化时会触发该事件 | `(selection) => void` |
| cell-mouse-enter | 当单元格 hover 进入时会触发该事| `(row, column, cell, event) => void` |
| cell-mouse-leave | 当单元格 hover 退出时会触发该事件 | `(row, column, cell, event) => void` |
| cell-click | 当某个单元格被点击时会触发该事件 | `(row, column, cell, event) => void` |
| cell-dblclick | 当某个单元格被双击击时会触发该事| `(row, column, cell, event) => void` |
| cell-contextmenu | 当某个单元格被右键点击时会触发该事件 | `(row, column, cell, event) => void` |
| row-click | 当某一行被点击时会触发该事| `(row, column, event) => void` |
| row-contextmenu | 当某一行被鼠标右键点击时会触发该事| `(row, column, event) => void` |
| row-dblclick | 当某一行被双击时会触发该事| `(row, column, event) => void` |
| header-click | 当某一列表头被点击时会触发该事件 | `(column, event) => void` |
| header-contextmenu | 当某一列表头被鼠标右键点击时触发该事| `(column, event) => void` |
| sort-change | 当表格排序条件发生变化时候会触发该事| `(data) => void` |
| filter-change | 当表格筛选条件发生变化时候会触发该事| `(filters) => void` |
| current-change | 当表格当前行发生变化时候会触发该事| `(currentRow, oldCurrentRow) => void` |
| header-dragend | 当拖动表头改变了列宽度时候会触发该事| `(newWidth, oldWidth, column, event) => void` |
| expand-change | 当用户对某一行展开者关闭时候会触发该事| `(row, expandedRows) => void` |

### Table 暴露

| Name | Description | Type |
|------|-------------|------|
| clearSelection | 用于多选表清空用户选择 | `() => void` |
| getSelectionRows | 用于多选表返回当前选中行 | `() => any[]` |
| toggleRowSelection | 用于多选表切换某一行选中状态| `(row, selected?, ignoreSelectable?) => void` |
| toggleAllSelection | 用于多选表切换所有行选中状态| `() => void` |
| toggleRowExpansion | 用于可展开表格,切换某一行展开状态| `(row, expanded?) => void` |
| setCurrentRow | 用于单选表设定某一行为选中| `(row) => void` |
| clearSort | 用于清空排序条件 | `() => void` |
| clearFilter | 用于清空过滤条件 | `(columnKeys?) => void` |
| doLayout | Table 进行重新布局 | `() => void` |
| sort | 手动Table 进行排序 | `(prop, order) => void` |
| scrollTo | 滚动到指定位| `(options) => void` |
| setScrollTop | 设置滚动条顶部距离 | `(top) => void` |
| setScrollLeft | 设置滚动条左侧距离 | `(left) => void` |

### TableColumn 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| type | 对应列类型 | `'selection' \| 'index' \| 'expand'` | |
| index | 自定义索| `number \| Function` | |
| column-key | 列 key | `string` | |
| label | 显示标签| `string` | |
| prop | 对应列内容字段| `string` | |
| width | 对应列宽度 | `string \| number` | |
| min-width | 对应列最小宽| `string \| number` | |
| fixed | 列是否固| `'left' \| 'right' \| boolean` | |
| render-header | 列标签Label 区域渲染使用Function | `Function` | |
| sortable | 对应列是否可以排| `boolean \| 'custom'` | false |
| sort-method | 对数据进行排序时候使用方法 | `Function` | |
| sort-by | 指定排序依据属性| `Function \| string \| string[]` | |
| sort-orders | 排序顺| `array` | ['ascending', 'descending', null] |
| resizable | 对应列是否可以通过拖动改变宽度 | `boolean` | true |
| formatter | 格式化内容| `Function` | |
| show-overflow-tooltip | 当内容过长被隐藏时显tooltip | `boolean \| object` | false |
| align | 对齐方式 | `'left' \| 'center' \| 'right'` | |
| header-align | 表头对齐方式 | `'left' \| 'center' \| 'right'` | |
| class-name | 列 className | `string` | |
| label-class-name | 当前列标题自定义类型| `string` | |
| selectable | 该行是否可被选择 | `Function` | |
| reserve-selection | 仅对 type=selection 列有效 | `boolean` | false |
| filters | 数据过滤选项 | `array` | |
| filter-placement | 过滤弹出框定位 | `string` | |
| filter-multiple | 数据过滤选项是否多| `boolean` | true |
| filter-method | 数据过滤使用方| `Function` | |
| filtered-value | 选中数据过滤项 | `array` | |

### TableColumn 插槽

| Name | Description | Type |
|------|-------------|------|
| default | 自定义内容| `{ row, column, $index }` |
| header | 自定义表| `{ column, $index }` |

## 使用示例

### 基本表格

```vue
<template>
  <el-table :data="tableData" style="width: 100%">
    <el-table-column prop="date" label="Date" width="180" />
    <el-table-column prop="name" label="Name" width="180" />
    <el-table-column prop="address" label="Address" />
  </el-table>
</template>

<script setup>
const tableData = [
  { date: '2016-05-03', name: 'Tom', address: 'No. 189, Grove St' },
  { date: '2016-05-02', name: 'Jack', address: 'No. 189, Grove St' }
]
</script>
```

### 斑马纹表
```vue
<template>
  <el-table :data="tableData" stripe style="width: 100%">
    <el-table-column prop="date" label="Date" />
    <el-table-column prop="name" label="Name" />
  </el-table>
</template>
```

### 带边框表
```vue
<template>
  <el-table :data="tableData" border style="width: 100%">
    <el-table-column prop="date" label="Date" />
    <el-table-column prop="name" label="Name" />
  </el-table>
</template>
```

### 固定表头

```vue
<template>
  <el-table :data="tableData" height="250" style="width: 100%">
    <el-table-column prop="date" label="Date" width="180" />
    <el-table-column prop="name" label="Name" width="180" />
    <el-table-column prop="address" label="Address" />
  </el-table>
</template>
```

### 固定
```vue
<template>
  <el-table :data="tableData" style="width: 100%">
    <el-table-column fixed prop="date" label="Date" width="150" />
    <el-table-column prop="name" label="Name" width="120" />
    <el-table-column prop="address" label="Address" width="300" />
    <el-table-column fixed="right" label="Operations" width="120">
      <template #default>
        <el-button type="text" size="small">Detail</el-button>
        <el-button type="text" size="small">Edit</el-button>
      </template>
    </el-table-column>
  </el-table>
</template>
```

### 多
```vue
<template>
  <el-table ref="multipleTableRef" :data="tableData" @selection-change="handleSelectionChange">
    <el-table-column type="selection" width="55" />
    <el-table-column prop="date" label="Date" width="120" />
    <el-table-column prop="name" label="Name" width="120" />
  </el-table>
  <div style="margin-top: 20px">
    <el-button @click="toggleSelection()">Clear selection</el-button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const multipleTableRef = ref()
const multipleSelection = ref([])

const handleSelectionChange = (val) => {
  multipleSelection.value = val
}

const toggleSelection = () => {
  multipleTableRef.value.clearSelection()
}
</script>
```

### 排序

```vue
<template>
  <el-table :data="tableData" :default-sort="{ prop: 'date', order: 'descending' }">
    <el-table-column prop="date" label="Date" sortable width="180" />
    <el-table-column prop="name" label="Name" sortable width="180" />
    <el-table-column prop="address" label="Address" />
  </el-table>
</template>
```

### 自定义列模板

```vue
<template>
  <el-table :data="tableData" style="width: 100%">
    <el-table-column label="Date" width="180">
      <template #default="scope">
        <div style="display: flex; align-items: center">
          <el-icon><timer /></el-icon>
          <span style="margin-left: 10px">{{ scope.row.date }}</span>
        </div>
      </template>
    </el-table-column>
    <el-table-column label="Name" width="180">
      <template #default="scope">
        <el-popover effect="light" trigger="hover" placement="top" width="auto">
          <template #default>
            <div>name: {{ scope.row.name }}</div>
          </template>
          <template #reference>
            <el-tag>{{ scope.row.name }}</el-tag>
          </template>
        </el-popover>
      </template>
    </el-table-column>
    <el-table-column label="Operations">
      <template #default="scope">
        <el-button size="small" @click="handleEdit(scope.$index, scope.row)">Edit</el-button>
        <el-button size="small" type="danger" @click="handleDelete(scope.$index, scope.row)">Delete</el-button>
      </template>
    </el-table-column>
  </el-table>
</template>
```

### 可展开
```vue
<template>
  <el-table :data="tableData" style="width: 100%">
    <el-table-column type="expand">
      <template #default="props">
        <div m="4">
          <p m="t-0 b-2">State: {{ props.row.state }}</p>
          <p m="t-0 b-2">City: {{ props.row.city }}</p>
        </div>
      </template>
    </el-table-column>
    <el-table-column prop="date" label="Date" width="180" />
    <el-table-column prop="name" label="Name" width="180" />
  </el-table>
</template>
```

### 汇总行

```vue
<template>
  <el-table :data="tableData" show-summary style="width: 100%">
    <el-table-column prop="id" label="ID" width="180" />
    <el-table-column prop="name" label="Name" />
    <el-table-column prop="amount1" label="Amount 1" />
    <el-table-column prop="amount2" label="Amount 2" />
  </el-table>
</template>
```

### 自定义汇
```vue
<template>
  <el-table :data="tableData" show-summary :summary-method="getSummaries">
    <el-table-column prop="id" label="ID" />
    <el-table-column prop="amount" label="Amount" />
  </el-table>
</template>

<script setup>
const getSummaries = (param) => {
  const { columns, data } = param
  const sums = []
  columns.forEach((column, index) => {
    if (index === 0) {
      sums[index] = 'Total Cost'
      return
    }
    const values = data.map((item) => Number(item[column.property]))
    if (!values.every((value) => Number.isNaN(value))) {
      sums[index] = `$ ${values.reduce((prev, curr) => {
        const value = Number(curr)
        if (!Number.isNaN(value)) {
          return prev + curr
        } else {
          return prev
        }
      }, 0)}`
    } else {
      sums[index] = 'N/A'
    }
  })
  return sums
}
</script>
```

## 常见问题

### 1. 表格不更
数据更改后使`doLayout()`:

```vue
<script setup>
const tableRef = ref()

const updateData = async () => {
  await fetchData()
  nextTick(() => {
    tableRef.value?.doLayout()
  })
}
</script>
```

### 2. 选择不清
使用 `clearSelection()`:

```vue
<script setup>
const tableRef = ref()

const clearAll = () => {
  tableRef.value?.clearSelection()
}
</script>
```

### 3. 排序不工
确保 `prop` 与数据字段匹

```vue
<el-table-column prop="name" sortable />  <!-- 必须匹配 row.name -->
```

## 最佳实践
1. **使用 row-key**: 设置 `row-key` 以获得更好性能和选择
2. **虚拟滚动**: 对大数据集使TableV2
3. **懒加*: 对树形数据使`lazy`
4. **筛选防*: 对筛选操作进行防5. **固定宽度**: 为固定列设置宽度
