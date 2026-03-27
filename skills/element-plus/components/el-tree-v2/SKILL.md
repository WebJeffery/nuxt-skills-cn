---
name: "el-tree-v2"
description: "用于大型分层数据集虚拟化树组件,具有极快滚动速度。当用户需要显示大型树结构(1000+ 节点)并使用虚拟滚动以提高性能时调用---

# 虚拟化树(TreeV2)组件

TreeV2 提供虚拟化树,用于高效渲染大型分层数据集
## 何时使用

- 大型树结1000+ 节点)
- 性能关键树视图
- 需要虚拟滚- 文件系统浏览
## 基本用法

```vue
<template>
  <el-tree-v2 :data="data" :props="defaultProps" :height="200" />
</template>

<script setup>
const defaultProps = {
  value: 'id',
  label: 'label',
  children: 'children'
}

const data = [
  {
    id: '1',
    label: 'Level 1',
    children: [
      { id: '1-1', label: 'Level 1-1' }
    ]
  }
]
</script>
```

## 可选择

```vue
<template>
  <el-tree-v2
    :data="data"
    :props="defaultProps"
    :height="200"
    show-checkbox
  />
</template>
```

## 默认状态
```vue
<template>
  <el-tree-v2
    :data="data"
    :props="defaultProps"
    :height="200"
    :default-expanded-keys="['1']"
    :default-checked-keys="['1-1']"
    show-checkbox
  />
</template>
```

## 自定义节点内容
```vue
<template>
  <el-tree-v2 :data="data" :props="defaultProps" :height="200">
    <template #default="{ node, data }">
      <span>{{ node.label }} - Custom</span>
    </template>
  </el-tree-v2>
</template>
```

## 过滤

```vue
<template>
  <el-input v-model="filterText" placeholder="Filter" />
  <el-tree-v2
    ref="treeRef"
    :data="data"
    :props="defaultProps"
    :height="200"
    :filter-method="filterMethod"
  />
</template>

<script setup>
import { ref, watch } from 'vue'

const filterText = ref('')
const treeRef = ref()

const filterMethod = (query, data, node) => {
  if (!query) return true
  return data.label.includes(query)
}

watch(filterText, (val) => {
  treeRef.value.filter(val)
})
</script>
```

## API 参考
### TreeV2 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| data | 树数| `Array<{[key: string]: any}>` | |
| empty-text | 数据为空时文本 | `string` | |
| props | 配置选项 | `object` | |
| highlight-current | 高亮当前节点 | `boolean` | `false` |
| expand-on-click-node | 点击节点时展开 | `boolean` | `true` |
| check-on-click-node | 点击节点时选中 | `boolean` | `false` |
| check-on-click-leaf | 点击叶节点时选中 | `boolean` | `true` |
| default-expanded-keys | 初始展开键 | `string[] \| number[]` | |
| show-checkbox | 显示复选框 | `boolean` | `false` |
| check-strictly | 父子独立 | `boolean` | `false` |
| default-checked-keys | 初始选中键 | `string[] \| number[]` | |
| current-node-key | 当前节点| `string \| number` | |
| filter-method | 过滤方法 | `(query, data, node) => boolean` | |
| indent | 缩进(像素) | `number` | `16` |
| icon | 自定义图| `string \| Component` | |
| item-size | 节点高度 | `number` | `26` |
| scrollbar-always-on | 始终显示滚动| `boolean` | `false` |
| height | 树高| `number` | `200` |

### Props 配置

| Attribute | Description | Type | Default |
|-----------|-------------|------|---------|
| value | 唯一| `string` | `'id'` |
| label | 标签| `string` | `'label'` |
| children | 子节点键 | `string` | `'children'` |
| disabled | 禁用| `string` | `'disabled'` |
| class | 自定义节点类 | `string \| (data, node) => string` | |

### 暴露

| Method | Description | Parameters |
|--------|-------------|------------|
| filter | 过滤节点 | `(query)` |
| getCheckedNodes | 获取选中节| `(leafOnly)` |
| getCheckedKeys | 获取选中键 | `(leafOnly)` |
| setCheckedKeys | 设置选中键 | `(keys)` |
| setChecked | 设置节点选中状态| `(key, checked)` |
| setExpandedKeys | 设置展开键 | `(keys)` |
| getHalfCheckedNodes | 获取半选中节| |
| getHalfCheckedKeys | 获取半选中键 | |
| getCurrentKey | 获取当前| |
| getCurrentNode | 获取当前节点 | |
| setCurrentKey | 设置当前| `(key)` |
| getNode | 通过键获取节| `(data)` |
| expandNode | 展开节点 | `(node)` |
| collapseNode | 折叠节点 | `(node)` |
| setData | 设置数据 | `(data)` |
| scrollTo | 滚动到位| `(offset)` |
| scrollToNode | 滚动到节| `(key, strategy)` |

### 事件

| Name | Description | Parameters |
|------|-------------|------------|
| node-click | 节点点击 | `(data, node, e)` |
| node-drop | 节点拖放 | `(data, node, e)` |
| node-contextmenu | 右键点击 | `(e, data, node)` |
| check-change | 选中状态改| `(data, checked)` |
| check | 复选框点击 | `(data, info)` |
| current-change | 当前节点改变 | `(data, node)` |
| node-expand | 节点展开 | `(data, node)` |
| node-collapse | 节点折叠 | `(data, node)` |

### 插槽

| Name | Description | Type |
|------|-------------|------|
| default | 自定义节点内容| `{ node, data }` |
| empty | 空内容| |

## 最佳实践
1. 用于具有 1000+ 节点树
2. 设置适当`item-size` 用于节点高度
3. 使用 `setData` 用于大型响应式数据更