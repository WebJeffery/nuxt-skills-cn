---
name: "el-dropdown"
description: "用于显示链接和操作列表可切换菜单下拉组件。当用户需要创建下拉菜单、操作菜单上下文菜单时调用---

# Dropdown 组件

Dropdown 组件提供一个可切换菜用于显示链接和操作列表
## 何时使用

- 导航菜单
- 操作菜单
- 上下文菜- 分割按钮操作

## 基本用法

```vue
<template>
  <el-dropdown>
    <span class="el-dropdown-link">
      Dropdown List
      <el-icon class="el-icon--right"><ArrowDown /></el-icon>
    </span>
    <template #dropdown>
      <el-dropdown-menu>
        <el-dropdown-item>Action 1</el-dropdown-item>
        <el-dropdown-item>Action 2</el-dropdown-item>
        <el-dropdown-item>Action 3</el-dropdown-item>
      </el-dropdown-menu>
    </template>
  </el-dropdown>
</template>

<script setup>
import { ArrowDown } from '@element-plus/icons-vue'
</script>
```

## 触发元素

```vue
<template>
  <el-dropdown split-button type="primary" @click="handleClick">
    Dropdown List
    <template #dropdown>
      <el-dropdown-menu>
        <el-dropdown-item>Action 1</el-dropdown-item>
        <el-dropdown-item>Action 2</el-dropdown-item>
      </el-dropdown-menu>
    </template>
  </el-dropdown>
</template>
```

## 触发方式

```vue
<template>
  <el-dropdown trigger="click">
    <span>Click to trigger</span>
    <template #dropdown>
      <el-dropdown-menu>
        <el-dropdown-item>Action</el-dropdown-item>
      </el-dropdown-menu>
    </template>
  </el-dropdown>
  
  <el-dropdown trigger="hover">
    <span>Hover to trigger</span>
    <template #dropdown>
      <el-dropdown-menu>
        <el-dropdown-item>Action</el-dropdown-item>
      </el-dropdown-menu>
    </template>
  </el-dropdown>
</template>
```

## 命令事件

```vue
<template>
  <el-dropdown @command="handleCommand">
    <span>Dropdown</span>
    <template #dropdown>
      <el-dropdown-menu>
        <el-dropdown-item command="a">Action 1</el-dropdown-item>
        <el-dropdown-item command="b">Action 2</el-dropdown-item>
      </el-dropdown-menu>
    </template>
  </el-dropdown>
</template>

<script setup>
const handleCommand = (command) => {
  console.log('Command:', command)
}
</script>
```

## 尺寸

```vue
<template>
  <el-dropdown size="large">
    <el-button>Large</el-button>
    <template #dropdown>
      <el-dropdown-menu>
        <el-dropdown-item>Action</el-dropdown-item>
      </el-dropdown-menu>
    </template>
  </el-dropdown>
</template>
```

## API 参考
### Dropdown 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| type | 菜单按钮类型 | Button type enum | `''` |
| size | 菜单尺寸 | `'large' \| 'default' \| 'small'` | `''` |
| max-height | 菜单最大高| `string \| number` | `''` |
| split-button | 显示为按钮组 | `boolean` | `false` |
| disabled | 是否禁用 | `boolean` | `false` |
| placement | 弹出菜单位| Placement enum | `'bottom'` |
| effect | Tooltip 主题 | `'dark' \| 'light'` | `'light'` |
| trigger | 如何触发 | `'click' \| 'hover' \| 'contextmenu'` | `'hover'` |
| hide-on-click | 点击后隐藏菜| `boolean` | `true` |
| show-timeout | 显示前延迟 | `number` | `150` |
| hide-timeout | 隐藏前延迟 | `number` | `150` |
| teleported | 传送到 body | `boolean` | `true` |

### Dropdown 事件

| Name | Description | Type |
|------|-------------|------|
| click | 左按钮点split-button) | `(e: MouseEvent) => void` |
| command | 下拉项点| `(...args: any[]) => void` |
| visible-change | 下拉可见性变| `(val: boolean) => void` |

### Dropdown 暴露

| Name | Description | Type |
|------|-------------|------|
| handleOpen | 打开下拉菜单 | `() => void` |
| handleClose | 关闭下拉菜单 | `() => void` |

### DropdownItem 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| command | 要分发命令 | `string \| number \| object` | |
| disabled | 是否禁用 | `boolean` | `false` |
| divided | 显示分隔| `boolean` | `false` |
| icon | 自定义图| `string \| Component` | |

## 最佳实践
1. 使用 `split-button` 用于带有下拉主要操2. 使用 `command` 事件进行操作处理
3. 设置 `hide-on-click="false"` 用于多操作菜