---
name: "el-tabs"
description: "用于将数据集合划分为不同类型标签页组件。当用户需要创建标签页界面、动态标签页卡片式标签页时调用---

# Element Plus Tabs 组件

将相关但属于不同类型数据集合分开
## 何时调用

在以下情况下调用此技能
- 用户需要创建标签页内容
- 用户想要动态添关闭标签- 用户需要卡片边框卡片样式标签页
- 用户想要定位标签顶部、左侧、右侧、底
- 用户询问带确认标签页切
## 功能特性
- **多种样式**: 默认、卡片、边框卡- **标签页位*: 顶部、底部、左侧、右- **动态标签页**: 添加和移除标签页
- **自定义标签*: 标签标签中自定义内容- **懒加载渲*: 懒加载标签页内容
- **离开前钩*: 切换标签页前确认

## API 参考
### Tabs 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定选中标签页名称 | `string \| number` | |
| default-value | 初始渲染时应激活标签页| `string \| number` | |
| type | Tab 类型| `'' \| 'card' \| 'border-card'` | '' |
| closable | Tab 是否可关| `boolean` | false |
| addable | Tab 是否可添| `boolean` | false |
| editable | Tab 是否可添加和可关| `boolean` | false |
| tab-position | 标签页位置 | `'top' \| 'right' \| 'bottom' \| 'left'` | top |
| stretch | 宽度是否适应容器 | `boolean` | false |
| before-leave | 切换标签页前钩| `(activeName, oldActiveName) => Awaitable<void \| boolean>` | () => true |
| tabindex | 标签tabindex | `string \| number` | 0 |

### Tabs 事件

| Name | Description | Parameters |
|------|-------------|------------|
| tab-click | 当标签页被点击时触发 | `(pane, ev)` |
| tab-change | activeName 改变时触| `(name)` |
| tab-remove | 当移除按钮被点击时触| `(name)` |
| tab-add | 当添加按钮被点击时触| `()` |
| edit | 当添加移除被点击时触发 | `(paneName, action)` |

### Tabs 插槽

| Name | Description | Subtags |
|------|-------------|---------|
| default | 自定义默认内容| Tab-pane |
| add-icon | 自定义添加按钮图| |

### Tabs 暴露

| Name | Description | Type |
|------|-------------|------|
| currentName | 当前激活标签页名| `Ref<TabPaneName>` |
| tabNavRef | tab-nav 组件实现例 | `Ref<TabNavInstance \| undefined>` |

### Tab-pane 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| label | 标签页标题 | `string` | '' |
| disabled | Tab 是否禁用 | `boolean` | false |
| name | 对应 Tabs name 标识符 | `string \| number` | |
| closable | Tab 是否可关| `boolean` | false |
| lazy | Tab 是否懒渲| `boolean` | false |

### Tab-pane 插槽

| Name | Description |
|------|-------------|
| default | Tab-pane 内容|
| label | Tab-pane 标签|

## 使用示例

### 基本用法

```vue
<template>
  <el-tabs v-model="activeName" @tab-click="handleClick">
    <el-tab-pane label="User" name="first">User</el-tab-pane>
    <el-tab-pane label="Config" name="second">Config</el-tab-pane>
    <el-tab-pane label="Role" name="third">Role</el-tab-pane>
    <el-tab-pane label="Task" name="fourth">Task</el-tab-pane>
  </el-tabs>
</template>

<script setup>
import { ref } from 'vue'

const activeName = ref('first')

const handleClick = (tab, event) => {
  console.log(tab, event)
}
</script>
```

### 卡片样式

```vue
<template>
  <el-tabs v-model="activeName" type="card">
    <el-tab-pane label="User" name="first">User</el-tab-pane>
    <el-tab-pane label="Config" name="second">Config</el-tab-pane>
    <el-tab-pane label="Role" name="third">Role</el-tab-pane>
  </el-tabs>
</template>

<script setup>
import { ref } from 'vue'

const activeName = ref('first')
</script>
```

### 边框卡片

```vue
<template>
  <el-tabs type="border-card">
    <el-tab-pane label="User">User</el-tab-pane>
    <el-tab-pane label="Config">Config</el-tab-pane>
    <el-tab-pane label="Role">Role</el-tab-pane>
  </el-tabs>
</template>
```

### 标签页位
```vue
<template>
  <el-radio-group v-model="tabPosition" style="margin-bottom: 32px">
    <el-radio-button value="top">Top</el-radio-button>
    <el-radio-button value="right">Right</el-radio-button>
    <el-radio-button value="bottom">Bottom</el-radio-button>
    <el-radio-button value="left">Left</el-radio-button>
  </el-radio-group>

  <el-tabs :tab-position="tabPosition" style="height: 200px">
    <el-tab-pane label="User">User</el-tab-pane>
    <el-tab-pane label="Config">Config</el-tab-pane>
    <el-tab-pane label="Role">Role</el-tab-pane>
    <el-tab-pane label="Task">Task</el-tab-pane>
  </el-tabs>
</template>

<script setup>
import { ref } from 'vue'

const tabPosition = ref('left')
</script>
```

### 自定义标签
```vue
<template>
  <el-tabs type="border-card">
    <el-tab-pane>
      <template #label>
        <span>
          <el-icon><Calendar /></el-icon>
          Route
        </span>
      </template>
      Route
    </el-tab-pane>
    <el-tab-pane label="Message">Message</el-tab-pane>
    <el-tab-pane label="Config">Config</el-tab-pane>
  </el-tabs>
</template>

<script setup>
import { Calendar } from '@element-plus/icons-vue'
</script>
```

### 动态标签页

```vue
<template>
  <el-tabs
    v-model="editableTabsValue"
    type="card"
    editable
    @edit="handleTabsEdit"
  >
    <el-tab-pane
      v-for="item in editableTabs"
      :key="item.name"
      :label="item.title"
      :name="item.name"
    >
      {{ item.content }}
    </el-tab-pane>
  </el-tabs>
</template>

<script setup>
import { ref } from 'vue'

let tabIndex = 2
const editableTabsValue = ref('2')
const editableTabs = ref([
  { title: 'Tab 1', name: '1', content: 'Tab 1 content' },
  { title: 'Tab 2', name: '2', content: 'Tab 2 content' }
])

const handleTabsEdit = (targetName, action) => {
  if (action === 'add') {
    const newTabName = `${++tabIndex}`
    editableTabs.value.push({
      title: 'New Tab',
      name: newTabName,
      content: 'New Tab content'
    })
    editableTabsValue.value = newTabName
  } else if (action === 'remove') {
    const tabs = editableTabs.value
    let activeName = editableTabsValue.value
    if (activeName === targetName) {
      tabs.forEach((tab, index) => {
        if (tab.name === targetName) {
          const nextTab = tabs[index + 1] || tabs[index - 1]
          if (nextTab) {
            activeName = nextTab.name
          }
        }
      })
    }
    editableTabsValue.value = activeName
    editableTabs.value = tabs.filter((tab) => tab.name !== targetName)
  }
}
</script>
```

### 离开前钩
```vue
<template>
  <el-tabs v-model="activeName" :before-leave="beforeLeave">
    <el-tab-pane label="User" name="first">User</el-tab-pane>
    <el-tab-pane label="Config" name="second">Config</el-tab-pane>
  </el-tabs>
</template>

<script setup>
import { ref } from 'vue'
import { ElMessage } from 'element-plus'

const activeName = ref('first')

const beforeLeave = async (activeName, oldActiveName) => {
  if (oldActiveName === 'first') {
    const confirmed = await ElMessageBox.confirm(
      'Are you sure you want to leave? Unsaved changes will be lost.',
      'Warning',
      { type: 'warning' }
    ).catch(() => false)
    return confirmed
  }
  return true
}
</script>
```

### 懒加
```vue
<template>
  <el-tabs v-model="activeName">
    <el-tab-pane label="Tab 1" name="1">Content 1</el-tab-pane>
    <el-tab-pane label="Tab 2" name="2" lazy>
      <!-- This content will only be rendered when tab is active -->
      <ExpensiveComponent />
    </el-tab-pane>
  </el-tabs>
</template>
```

### 自定义添加按钮图
```vue
<template>
  <el-tabs type="card" addable>
    <template #add-icon>
      <el-icon><Plus /></el-icon>
    </template>
    <el-tab-pane label="Tab 1">Content 1</el-tab-pane>
    <el-tab-pane label="Tab 2">Content 2</el-tab-pane>
  </el-tabs>
</template>

<script setup>
import { Plus } from '@element-plus/icons-vue'
</script>
```

## 常见问题

### 1. 标签页内容不更新

tab-pane 上使`:key` 以处理动态内容

```vue
<el-tab-pane :key="tabKey" :label="label">
  {{ content }}
</el-tab-pane>
```

### 2. 右侧位置高度问
设置固定高度:

```vue
<el-tabs tab-position="left" style="height: 400px">
  <!-- tabs -->
</el-tabs>
```

### 3. 标签页不切换

确保 `v-model` 值与标签`name` 匹配:

```vue
<el-tabs v-model="activeName">
  <el-tab-pane name="first">  <!-- activeName should be 'first' -->
</el-tabs>
```

## 最佳实践
1. **使用懒加*: 对昂贵标签页内容使`lazy`
2. **唯一名*: 确保每个 tab-pane 都有唯一名3. **离开前钩*: 使用 before-leave 进行未保存更改警4. **动态使用卡片类型*: 添加/移除标签页时使用卡片类型
5. **一致样式**: 在整个应用程序中使用一致标签页类型