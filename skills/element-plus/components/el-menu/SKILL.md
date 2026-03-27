---
name: "el-menu"
description: "用于网站导航菜单组件支持水平和垂直模式。当用户需要创建导航菜单、侧边栏下拉菜单时调用---

# Element Plus Menu 组件

为您网站提供导航菜单
## 何时调用

在以下情况下调用此技能
- 用户需要创建导航菜- 用户想要水平垂直菜- 用户需要可折叠侧边栏菜单
- 用户想要Vue Router 集成
- 用户询问子菜单菜单
## 功能特性
- **多种模式**: 水平(顶部和垂侧边
- **可折*: 垂直菜单可以折叠
- **Vue Router 集成**: 自动路由导航
- **子菜*: 多级嵌套菜单
- **菜单*: 分组相关菜单- **可定*: 颜色、图标和主题
- **无障碍访问*: 键盘导航支持

## API 参考
### Menu 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| mode | 菜单显示模式 | `'horizontal' \| 'vertical'` | vertical |
| collapse | 是否水平折叠收起菜单(仅在 vertical 模式下可 | `boolean` | false |
| ellipsis | 是否省略多余菜单项(仅在 horizontal 模式下可 | `boolean` | true |
| ellipsis-icon | 自定义省略图仅在 horizontal 模式下可 | `string \| Component` | |
| popper-offset | popper 偏移量 | `number` | 6 |
| default-active | 页面加载时激活菜单 index | `string` | '' |
| default-openeds | 默认展开子菜单index 数组 | `string[]` | [] |
| unique-opened | 是否只保持一个子菜单展开 | `boolean` | false |
| menu-trigger | 子菜单触发方式(horizontal 模式) | `'hover' \| 'click'` | hover |
| router | 是否启用 vue-router 模式 | `boolean` | false |
| collapse-transition | 是否开启折叠动| `boolean` | true |
| popper-effect | 折叠时 Tooltip 主题 | `'dark' \| 'light'` | dark |
| close-on-click-outside | 点击菜单外部时是否收起菜| `boolean` | false |
| popper-class | 弹出菜单自定义类名 | `string` | |
| popper-style | 弹出菜单自定义样式 | `string \| object` | |
| show-timeout | 显示菜单延| `number` | 300 |
| hide-timeout | 隐藏菜单延| `number` | 300 |

### Menu 事件

| Name | Description | Type |
|------|-------------|------|
| select | 菜单激活回| `(index, indexPath, item, routerResult) => void` |
| open | 子菜单展开回| `(index, indexPath) => void` |
| close | 子菜单收起回调 | `(index, indexPath) => void` |

### Menu 插槽

| Name | Description | Subtags |
|------|-------------|---------|
| default | 自定义默认内容| SubMenu / MenuItem / MenuItemGroup |

### Menu 暴露

| Name | Description | Type |
|------|-------------|------|
| open | 展开指定子菜单 | `(index: string) => void` |
| close | 收起指定子菜单 | `(index: string) => void` |
| handleResize | 手动触发菜单宽度重新计算 | `() => void` |
| updateActiveIndex | 设置当前激活菜单 index | `(index: string) => void` |

### SubMenu 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| index | 唯一标识(必填) | `string` | |
| popper-class | 弹出菜单自定义类名 | `string` | |
| popper-style | 弹出菜单自定义样式 | `string \| object` | |
| show-timeout | 显示前延迟 | `number` | |
| hide-timeout | 隐藏前延迟 | `number` | |
| disabled | 是否禁用 | `boolean` | false |
| teleported | 是否将弹出菜单传送到 body | `boolean` | undefined |
| popper-offset | popper 偏移量 | `number` | |
| expand-close-icon | 展开且子菜单关闭时图标 | `string \| Component` | |
| expand-open-icon | 展开且子菜单打开时图标 | `string \| Component` | |
| collapse-close-icon | 折叠且子菜单关闭时图标 | `string \| Component` | |
| collapse-open-icon | 折叠且子菜单打开时图标 | `string \| Component` | |

### SubMenu 插槽

| Name | Description | Subtags |
|------|-------------|---------|
| default | 自定义默认内容| SubMenu / MenuItem / MenuItemGroup |
| title | 自定义标题内容| |

### MenuItem 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| index | 唯一标识(必填) | `string` | |
| route | Vue Router 路由参数 | `string \| object` | |
| disabled | 是否禁用 | `boolean` | false |

### MenuItem 事件

| Name | Description | Type |
|------|-------------|------|
| click | 菜单项点击时回| `(item) => void` |

### MenuItem 插槽

| Name | Description |
|------|-------------|
| default | 自定义默认内容|
| title | 自定义标题内容|

### MenuItemGroup 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| title | 分组标题 | `string` | |

### MenuItemGroup 插槽

| Name | Description | Subtags |
|------|-------------|---------|
| default | 自定义默认内容| MenuItem |
| title | 自定义分组标签| |

## 使用示例

### 顶部水平)

```vue
<template>
  <el-menu
    :default-active="activeIndex"
    mode="horizontal"
    @select="handleSelect"
  >
    <el-menu-item index="1">Processing Center</el-menu-item>
    <el-sub-menu index="2">
      <template #title>Workspace</template>
      <el-menu-item index="2-1">Item One</el-menu-item>
      <el-menu-item index="2-2">Item Two</el-menu-item>
      <el-menu-item index="2-3">Item Three</el-menu-item>
    </el-sub-menu>
    <el-menu-item index="3" disabled>Info</el-menu-item>
    <el-menu-item index="4">Orders</el-menu-item>
  </el-menu>
</template>

<script setup>
import { ref } from 'vue'

const activeIndex = ref('1')

const handleSelect = (key, keyPath) => {
  console.log(key, keyPath)
}
</script>
```

### 左右对齐

```vue
<template>
  <el-menu mode="horizontal">
    <el-menu-item index="1">Left Item</el-menu-item>
    <el-menu-item index="2">Left Item</el-menu-item>
    <div style="flex-grow: 1" />
    <el-menu-item index="3">Right Item</el-menu-item>
    <el-menu-item index="4">Right Item</el-menu-item>
  </el-menu>
</template>
```

### 侧边垂直)

```vue
<template>
  <el-menu
    :default-active="activeIndex"
    class="el-menu-vertical"
  >
    <el-menu-item index="1">
      <el-icon><location /></el-icon>
      <span>Navigator One</span>
    </el-menu-item>
    <el-menu-item index="2">
      <el-icon><document /></el-icon>
      <span>Navigator Two</span>
    </el-menu-item>
    <el-menu-item index="3">
      <el-icon><setting /></el-icon>
      <span>Navigator Three</span>
    </el-menu-item>
  </el-menu>
</template>

<script setup>
import { ref } from 'vue'
import { Location, Document, Setting } from '@element-plus/icons-vue'

const activeIndex = ref('1')
</script>

<style>
.el-menu-vertical {
  width: 200px;
  min-height: 400px;
}
</style>
```

### 可折叠侧边栏

```vue
<template>
  <el-radio-group v-model="isCollapse" style="margin-bottom: 20px">
    <el-radio-button :value="false">Expand</el-radio-button>
    <el-radio-button :value="true">Collapse</el-radio-button>
  </el-radio-group>
  
  <el-menu
    :default-active="activeIndex"
    :collapse="isCollapse"
    class="el-menu-vertical"
  >
    <el-menu-item index="1">
      <el-icon><HomeFilled /></el-icon>
      <template #title>Home</template>
    </el-menu-item>
    <el-sub-menu index="2">
      <template #title>
        <el-icon><Document /></el-icon>
        <span>Documents</span>
      </template>
      <el-menu-item index="2-1">Recent</el-menu-item>
      <el-menu-item index="2-2">Starred</el-menu-item>
    </el-sub-menu>
    <el-menu-item index="3">
      <el-icon><Setting /></el-icon>
      <template #title>Settings</template>
    </el-menu-item>
  </el-menu>
</template>

<script setup>
import { ref } from 'vue'
import { HomeFilled, Document, Setting } from '@element-plus/icons-vue'

const isCollapse = ref(false)
const activeIndex = ref('1')
</script>

<style>
.el-menu-vertical:not(.el-menu--collapse) {
  width: 200px;
}
</style>
```

### Vue Router 配合

```vue
<template>
  <el-menu
    :default-active="$route.path"
    router
  >
    <el-menu-item index="/dashboard">
      <el-icon><HomeFilled /></el-icon>
      <span>Dashboard</span>
    </el-menu-item>
    <el-menu-item index="/users">
      <el-icon><User /></el-icon>
      <span>Users</span>
    </el-menu-item>
    <el-menu-item index="/settings">
      <el-icon><Setting /></el-icon>
      <span>Settings</span>
    </el-menu-item>
  </el-menu>
</template>
```

### 菜单
```vue
<template>
  <el-menu :default-active="activeIndex">
    <el-menu-item-group title="Group One">
      <el-menu-item index="1-1">Option 1</el-menu-item>
      <el-menu-item index="1-2">Option 2</el-menu-item>
    </el-menu-item-group>
    <el-menu-item-group title="Group Two">
      <el-menu-item index="2-1">Option 3</el-menu-item>
      <el-menu-item index="2-2">Option 4</el-menu-item>
    </el-menu-item-group>
  </el-menu>
</template>
```

## 常见问题

### 1. 菜单不更新激活状态
使用 `updateActiveIndex` 方法:

```vue
<script setup>
const menuRef = ref()

const updateActive = (index) => {
  menuRef.value?.updateActiveIndex(index)
}
</script>
```

### 2. 路由导航不工
确保设置`router` 属性`index` 匹配路由路径:

```vue
<el-menu router>
  <el-menu-item index="/dashboard">Dashboard</el-menu-item>
</el-menu>
```

### 3. 水平模式下自定义高
覆盖 CSS 变量:

```css
.el-menu--horizontal {
  --el-menu-horizontal-height: 80px;
}
```

## 最佳实践
1. **使用 router 模式**: Vue Router 集成以进SPA 导航
2. **一致图标**: 在菜单项中一致地使用图标
3. **分组相关*: 使用 MenuItemGroup 进行组织
4. **移动端折*: 使用折叠模式进行响应式设5. **唯一索*: 确保每个菜单项都有唯一索