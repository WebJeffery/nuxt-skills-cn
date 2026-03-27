---
name: "el-tree"
description: "用于显示具有选择、筛选和拖放支持分层数据树形组件。当用户需要显示文件夹结构、组织架构图、分类树任何具有可展开节点分层数据时调用---

# 树形组件

树形组件显示一组具有层次结构数据,支持选择、筛选、懒加载和拖放功能
## 何时使用

- 显示文件夹目录结构
- 显示组织架构- 分类型分类选择
- 带有嵌套项菜单导航
- 任何分层数据表示

## 基本用法

```vue
<template>
  <el-tree :data="data" :props="defaultProps" />
</template>

<script setup>
const data = [
  {
    label: 'Level one 1',
    children: [
      {
        label: 'Level two 1-1',
        children: [
          { label: 'Level three 1-1-1' }
        ]
      }
    ]
  },
  {
    label: 'Level one 2',
    children: [
      { label: 'Level two 2-1' },
      { label: 'Level two 2-2' }
    ]
  }
]

const defaultProps = {
  children: 'children',
  label: 'label'
}
</script>
```

## 带复选框可选择
```vue
<template>
  <el-tree
    ref="treeRef"
    :data="data"
    :props="defaultProps"
    show-checkbox
    node-key="id"
    :default-expanded-keys="[2, 3]"
    :default-checked-keys="[5]"
  />
</template>

<script setup>
import { ref } from 'vue'

const treeRef = ref()

const data = [
  {
    id: 1,
    label: 'Level one 1',
    children: [
      { id: 4, label: 'Level two 1-1' },
      { id: 5, label: 'Level two 1-2' }
    ]
  },
  {
    id: 2,
    label: 'Level one 2',
    children: [
      { id: 6, label: 'Level two 2-1' }
    ]
  },
  {
    id: 3,
    label: 'Level one 3',
    children: [
      { id: 7, label: 'Level two 3-1' }
    ]
  }
]

const defaultProps = {
  children: 'children',
  label: 'label'
}
</script>
```

## 懒加
```vue
<template>
  <el-tree
    :props="props"
    :load="loadNode"
    lazy
    show-checkbox
  />
</template>

<script setup>
const props = {
  label: 'name',
  children: 'zones',
  isLeaf: 'leaf'
}

const loadNode = (node, resolve) => {
  if (node.level === 0) {
    return resolve([{ name: 'Root' }])
  }
  
  if (node.level > 1) {
    return resolve([])
  }
  
  setTimeout(() => {
    const data = [
      { name: 'leaf', leaf: true },
      { name: 'zone' }
    ]
    resolve(data)
  }, 500)
}
</script>
```

## 自定义节点内容
```vue
<template>
  <el-tree :data="data" :props="defaultProps">
    <template #default="{ node, data }">
      <span class="custom-tree-node">
        <span>{{ node.label }}</span>
        <span>
          <el-button type="primary" size="small" @click="append(data)">
            Append
          </el-button>
          <el-button type="danger" size="small" @click="remove(node, data)">
            Delete
          </el-button>
        </span>
      </span>
    </template>
  </el-tree>
</template>

<script setup>
let id = 1000

const append = (data) => {
  const newChild = { label: 'test', children: [] }
  if (!data.children) {
    data.children = []
  }
  data.children.push(newChild)
}

const remove = (node, data) => {
  const parent = node.parent
  const children = parent.data.children || parent.data
  const index = children.findIndex(d => d.id === data.id)
  children.splice(index, 1)
}
</script>
```

## 筛
```vue
<template>
  <el-input
    v-model="filterText"
    placeholder="Filter keyword"
  />
  <el-tree
    ref="treeRef"
    :data="data"
    :props="defaultProps"
    :filter-node-method="filterNode"
  />
</template>

<script setup>
import { ref, watch } from 'vue'

const filterText = ref('')
const treeRef = ref()

const filterNode = (value, data) => {
  if (!value) return true
  return data.label.includes(value)
}

watch(filterText, (val) => {
  treeRef.value.filter(val)
})
</script>
```

## 手风琴模
```vue
<template>
  <el-tree
    :data="data"
    :props="defaultProps"
    accordion
  />
</template>
```

## 可拖拽树

```vue
<template>
  <el-tree
    :data="data"
    :props="defaultProps"
    draggable
    :allow-drop="allowDrop"
    :allow-drag="allowDrag"
    @node-drag-start="handleDragStart"
    @node-drag-enter="handleDragEnter"
    @node-drag-leave="handleDragLeave"
    @node-drag-over="handleDragOver"
    @node-drag-end="handleDragEnd"
    @node-drop="handleDrop"
  />
</template>

<script setup>
const allowDrop = (draggingNode, dropNode, type) => {
  if (dropNode.data.label.includes('Level three')) {
    return type !== 'inner'
  }
  return true
}

const allowDrag = (draggingNode) => {
  return !draggingNode.data.label.includes('Level three')
}

const handleDragStart = (node, ev) => {
  console.log('drag start', node)
}

const handleDrop = (draggingNode, dropNode, dropType, ev) => {
  console.log('drop', draggingNode, dropNode, dropType)
}
</script>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| data | 树形数据 | `Array<{[key: string]: any}>` | |
| empty-text | 数据为空时文本 | `string` | |
| node-key | 节点唯一标识| `string` | |
| props | 配置选项 | `object` | |
| render-after-expand | 仅在父节点展开后渲染子节点 | `boolean` | `true` |
| load | 加载子树数据方懒模 | `(node, resolve, reject) => void` | |
| render-content | 树节点渲染函| `(h, { node, data, store }) => void` | |
| highlight-current | 高亮当前节点 | `boolean` | `false` |
| default-expand-all | 默认展开所有节| `boolean` | `false` |
| expand-on-click-node | 点击节点时展开 | `boolean` | `true` |
| check-on-click-node | 点击节点时选中 | `boolean` | `false` |
| check-on-click-leaf | 点击叶子节点时选中 | `boolean` | `true` |
| auto-expand-parent | 子节点展开时自动展开父节| `boolean` | `true` |
| default-expanded-keys | 初始展开节点键 | `Array<string \| number>` | |
| show-checkbox | 显示复选框 | `boolean` | `false` |
| check-strictly | 父子选择独立 | `boolean` | `false` |
| default-checked-keys | 初始选中节点键 | `Array<string \| number>` | |
| current-node-key | 初始选中节点键 | `string \| number` | |
| filter-node-method | 筛选方| `(value, data, node) => boolean` | |
| accordion | 一次只展开一个同级节| `boolean` | `false` |
| indent | 缩进像素 | `number` | `18` |
| icon | 自定义树节点图标 | `string \| Component` | |
| lazy | 启用懒加| `boolean` | `false` |
| draggable | 启用拖放 | `boolean` | `false` |
| allow-drag | 判断是否可拖拽函数 | `(node) => boolean` | |
| allow-drop | 判断是否可放置函数 | `(draggingNode, dropNode, type) => boolean` | |

### Props 配置

| Attribute | Description | Type | Default |
|-----------|-------------|------|---------|
| label | 节点标签键 | `string \| (data, node) => string` | |
| children | 节点子树键 | `string` | |
| disabled | 禁用状态| `string \| (data, node) => boolean` | |
| isLeaf | 叶子节点检测试| `string \| (data, node) => boolean` | |
| class | 自定义节点类 | `string \| (data, node) => string` | |

### 暴露(方法)

| Method | Description | Parameters |
|--------|-------------|------------|
| filter | 筛选所有树节点 | `(value)` |
| updateKeyChildren | 为节点设置新数据 | `(key, data)` |
| getCheckedNodes | 获取选中节点 | `(leafOnly, includeHalfChecked)` |
| setCheckedNodes | 设置选中节点 | `(nodes, leafOnly)` |
| getCheckedKeys | 获取选中| `(leafOnly)` |
| setCheckedKeys | 设置选中| `(keys, leafOnly)` |
| setChecked | 设置节点选中状态| `(key/data, checked, deep)` |
| getHalfCheckedNodes | 获取半选中节点 | |
| getHalfCheckedKeys | 获取半选中| |
| getCurrentKey | 获取当前节点| |
| getCurrentNode | 获取当前节点数据 | |
| setCurrentKey | 通过键设置当前节| `(key, shouldAutoExpandParent)` |
| setCurrentNode | 设置当前节点 | `(node, shouldAutoExpandParent)` |
| getNode | 通过数据键获取节点 | `(data)` |
| remove | 移除节点 | `(data)` |
| append | 追加子节| `(data, parentNode)` |
| insertBefore | 在另一个节点前插入 | `(data, refNode)` |
| insertAfter | 在另一个节点后插入 | `(data, refNode)` |

### 事件

| Name | Description | Parameters |
|------|-------------|------------|
| node-click | 节点被点| `(data, node, TreeNode, event)` |
| node-contextmenu | 右键点击节点 | `(event, data, node, TreeNode)` |
| check-change | 选中状态改| `(data, checked, indeterminate)` |
| check | 复选框被点| `(data, { checkedNodes, checkedKeys, halfCheckedNodes, halfCheckedKeys })` |
| current-change | 当前节点改变 | `(data, node)` |
| node-expand | 节点展开 | `(data, node, TreeNode)` |
| node-collapse | 节点折叠 | `(data, node, TreeNode)` |
| node-drag-start | 拖拽开| `(node, event)` |
| node-drag-enter | 拖拽进入节点 | `(draggingNode, enterNode, event)` |
| node-drag-leave | 拖拽离开节点 | `(draggingNode, leaveNode, event)` |
| node-drag-over | 拖拽悬停在节点上 | `(draggingNode, overNode, event)` |
| node-drag-end | 拖拽结束 | `(draggingNode, endNode, type, event)` |
| node-drop | 节点被放| `(draggingNode, dropNode, type, event)` |

### 插槽

| Name | Description |
|------|-------------|
| default | 树节点自定义内容|
| empty | 数据为空时自定义内容|

## 常见模式

### 获取选中节点

```vue
<template>
  <el-tree
    ref="treeRef"
    :data="data"
    show-checkbox
    node-key="id"
  />
  <el-button @click="getChecked">Get Checked</el-button>
</template>

<script setup>
import { ref } from 'vue'

const treeRef = ref()

const getChecked = () => {
  const checkedNodes = treeRef.value.getCheckedNodes()
  const checkedKeys = treeRef.value.getCheckedKeys()
  console.log('Nodes:', checkedNodes)
  console.log('Keys:', checkedKeys)
}
</script>
```

### 动态树操作

```vue
<template>
  <el-tree
    ref="treeRef"
    :data="data"
    node-key="id"
    default-expand-all
  />
  <el-button @click="addNode">Add Node</el-button>
  <el-button @click="removeNode">Remove Node</el-button>
</template>

<script setup>
import { ref } from 'vue'

const treeRef = ref()
const data = ref([
  { id: 1, label: 'Root', children: [] }
])

const addNode = () => {
  const newNode = { id: Date.now(), label: 'New Node', children: [] }
  treeRef.value.append(newNode, data.value[0])
}

const removeNode = () => {
  const node = treeRef.value.getNode(data.value[0].children[0])
  if (node) {
    treeRef.value.remove(node)
  }
}
</script>
```

## 组件交互

- **Form** 配合用于表单中分层数据选择
- **Input** 组合用于树筛- **Dialog** 配合用于模态框中树选择
- **Menu** 集成用于嵌套导航结构

## 最佳实践
1. 使用选择功能时始终设`node-key`
2. 对大型数据集使用 `lazy` 加载以提高性能
3. 实现可搜索树`filter-node-method`
4. 当父子选择应该独立时使`check-strictly`
5. 对于空间受限布局考虑 `accordion` 模式
