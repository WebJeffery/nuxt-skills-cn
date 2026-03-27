---
name: "el-popover"
description: "用于在弹出框中显示丰富内容弹出框组件。当用户需要在弹出框中显示复杂内容、嵌套操作丰富信息时调用---

# Element Plus Popover 组件

在弹出框中显示丰富内容通过悬停、点击、聚焦上下文菜单触发
## 何时调用

在以下情况下调用此技能
- 用户需要在弹出框中显示丰富内容
- 用户想要创建嵌套操作
- 用户需要在弹出框中显示表格表- 用户想要通过点击悬停触发弹出框
- 用户询问虚拟触发

## 功能特性
- **9 种位*: Tooltip 相同
- **多种触发方式**: hover、click、focus、contextmenu
- **丰富内容**: 支持表格、表单等复杂内容
- **虚拟触发**: 分离触发器和内容元素
- **嵌套组件**: 可以嵌套其他组件
- **可定制宽*: 可配置宽
## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| trigger | Popover 如何触发 | `'click' \| 'focus' \| 'hover' \| 'contextmenu' \| Array` | hover |
| trigger-keys | 控制显示键盘代| `Array` | ['Enter', 'Space'] |
| title | Popover 标题 | `string` | |
| effect | Tooltip 主题 | `'dark' \| 'light'` | light |
| content | Popover 内容 | `string` | '' |
| width | Popover 宽度 | `string \| number` | 150 |
| placement | Popover 位置 | `'top' \| 'top-start' \| 'top-end' \| 'bottom' \| 'bottom-start' \| 'bottom-end' \| 'left' \| 'left-start' \| 'left-end' \| 'right' \| 'right-start' \| 'right-end'` | bottom |
| disabled | Popover 是否禁用 | `boolean` | false |
| visible / v-model:visible | Popover 是否可见 | `boolean \| null` | null |
| offset | Popover 偏移| `number` | undefined |
| transition | 过渡动画 | `string` | |
| show-arrow | 是否显示箭头 | `boolean` | true |
| popper-options | popper.js 参数 | `object` | {} |
| popper-class | Popover 自定义类名 | `string` | |
| popper-style | Popover 自定义样式 | `string \| object` | |
| show-after | 出现延迟(ms) | `number` | 0 |
| hide-after | 消失延迟(ms) | `number` | 200 |
| auto-close | 隐藏超时时间(ms) | `number` | 0 |
| tabindex | Popover tabindex | `number \| string` | 0 |
| teleported | Popover 是否传| `boolean` | true |
| append-to | Popover 附加到哪个元| `CSSSelector \| HTMLElement` | body |
| persistent | Popover 在不活动时是否持| `boolean` | true |
| virtual-triggering | 是否启用虚拟触发 | `boolean` | |
| virtual-ref | 虚拟触发引用元| `HTMLElement` | |

### 插槽

| Name | Description | Type |
|------|-------------|------|
| default | Popover 内容| `{hide: () => void}` |
| reference | 触发 Popover HTML 元素 | |

### 事件

| Name | Description | Type |
|------|-------------|------|
| show | Popover 显示时触| `() => void` |
| before-enter | 进入过渡前触| `() => void` |
| after-enter | 进入过渡后触| `() => void` |
| hide | Popover 隐藏时触| `() => void` |
| before-leave | 离开过渡前触| `() => void` |
| after-leave | 离开过渡后触| `() => void` |

### 暴露

| Name | Description | Type |
|------|-------------|------|
| hide | 隐藏 Popover | `() => void` |

## 使用示例

### 基本用法

```vue
<template>
  <el-popover
    title="Title"
    content="This is popover content"
    trigger="click"
  >
    <template #reference>
      <el-button>Click to activate</el-button>
    </template>
  </el-popover>
</template>
```

### 悬停触发

```vue
<template>
  <el-popover
    placement="top"
    title="Title"
    :width="200"
    trigger="hover"
    content="This is popover content"
  >
    <template #reference>
      <el-button>Hover to activate</el-button>
    </template>
  </el-popover>
</template>
```

### 丰富内容

```vue
<template>
  <el-popover placement="right" :width="400">
    <template #reference>
      <el-button>Click to see table</el-button>
    </template>
    <el-table :data="tableData">
      <el-table-column prop="name" label="Name" />
      <el-table-column prop="address" label="Address" />
    </el-table>
  </el-popover>
</template>

<script setup>
const tableData = [
  { name: 'Tom', address: 'No. 189, Grove St' },
  { name: 'Jack', address: 'No. 189, Grove St' }
]
</script>
```

### 嵌套操作

```vue
<template>
  <el-popover placement="top" :width="160">
    <template #reference>
      <el-button>Delete</el-button>
    </template>
    <p>Are you sure to delete this?</p>
    <div style="text-align: right; margin: 0">
      <el-button size="small" text @click="visible = false">Cancel</el-button>
      <el-button size="small" type="primary" @click="visible = false">
        Confirm
      </el-button>
    </div>
  </el-popover>
</template>

<script setup>
import { ref } from 'vue'

const visible = ref(false)
</script>
```

### 与表单配合
```vue
<template>
  <el-popover placement="bottom" :width="300" trigger="click">
    <template #reference>
      <el-button>Open Form</el-button>
    </template>
    <el-form :model="form" label-width="80px">
      <el-form-item label="Name">
        <el-input v-model="form.name" />
      </el-form-item>
      <el-form-item label="Email">
        <el-input v-model="form.email" />
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submitForm">Submit</el-button>
      </el-form-item>
    </el-form>
  </el-popover>
</template>

<script setup>
import { reactive } from 'vue'

const form = reactive({
  name: '',
  email: ''
})

const submitForm = () => {
  console.log('Form submitted:', form)
}
</script>
```

### 虚拟触发

```vue
<template>
  <el-button ref="buttonRef">Trigger</el-button>
  
  <el-popover
    virtual-triggering
    :virtual-ref="buttonRef"
    content="Virtual popover content"
  />
</template>

<script setup>
import { ref } from 'vue'

const buttonRef = ref()
</script>
```

### 受控可见
```vue
<template>
  <el-popover v-model:visible="visible" content="Controlled popover">
    <template #reference>
      <el-button @click="visible = !visible">Toggle</el-button>
    </template>
  </el-popover>
</template>

<script setup>
import { ref } from 'vue'

const visible = ref(false)
</script>
```

### 多种触发方式

```vue
<template>
  <el-popover
    :trigger="['click', 'hover']"
    content="Multiple triggers"
  >
    <template #reference>
      <el-button>Click or Hover</el-button>
    </template>
  </el-popover>
</template>
```

### 自定义宽
```vue
<template>
  <el-popover :width="300" content="Wide popover content">
    <template #reference>
      <el-button>Wide Popover</el-button>
    </template>
  </el-popover>
</template>
```

### 无箭
```vue
<template>
  <el-popover :show-arrow="false" content="No arrow popover">
    <template #reference>
      <el-button>No Arrow</el-button>
    </template>
  </el-popover>
</template>
```

### 从内容中关闭

```vue
<template>
  <el-popover placement="top" :width="200">
    <template #reference>
      <el-button>Click</el-button>
    </template>
    <template #default="{ hide }">
      <p>Click button to close</p>
      <el-button @click="hide">Close</el-button>
    </template>
  </el-popover>
</template>
```

## 常见问题

### 1. Popover 不关
使用插槽中 `hide` 函数:

```vue
<el-popover>
  <template #default="{ hide }">
    <el-button @click="hide">Close</el-button>
  </template>
</el-popover>
```

### 2. Popover 位置问题

使用 `placement` `fallback-placements`:

```vue
<el-popover
  placement="top"
  :fallback-placements="['bottom', 'left', 'right']"
>
</el-popover>
```

### 3. 点击外部不关
设置 `trigger="click"`:

```vue
<el-popover trigger="click">
</el-popover>
```

## 最佳实践
1. **使用适当触发器**: 根据用例选择触发2. **设置宽度**: 为内容设置适当宽3. **从内容中关闭**: 使用 `hide` 函数进行自定义关4. **虚拟触发**: 用于复杂触发场5. **受控可见*: 使用 v-model:visible 进行编程控制
