---
name: "el-dialog"
description: "对话框模态组件用于在页面之上层中显示内容。当用户需要模态对话框、确认对话框表单弹出窗口时调用---

# Element Plus Dialog 组件

在保留当前页面状态同时通知用户
## 何时调用

在以下情况下调用此技能
- 用户需要显示模态内容- 用户想要创建确认对话- 用户需要表单对话框
- 用户想要可拖动对话- 用户需要嵌套对话框
- 用户询问对话框自定义

## 功能特性
- **模非模*: 带不带遮罩- **可自定义尺寸**: 宽度、高度、全- **可拖*: 通过标题栏拖动对话框
- **嵌套支持**: 多层对话- **自定义内容*: 标题、主体、页脚插槽- **动画**: 自定义过渡效- **居中对齐**: 水平和垂直居- **关闭时销*: 关闭时清DOM

## API 参考
### Dialog 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 对话框可见| `boolean` | false |
| title | 对话框标签| `string` | '' |
| width | 对话框宽| `string \| number` | '' |
| fullscreen | 是否全屏 | `boolean` | false |
| top | margin-top | `string` | '' |
| modal | 是否显示遮罩| `boolean` | true |
| modal-penetrable | 遮罩层是否可穿| `boolean` | false |
| modal-class | 遮罩层自定义类型| `string` | |
| header-class | 标题自定义类名 | `string` | |
| body-class | 主体内容自定义类名 | `string` | |
| footer-class | 页脚自定义类名 | `string` | |
| append-to-body | 是否将对话框追加body | `boolean` | false |
| append-to | 对话框追加到元| `CSSSelector \| HTMLElement` | body |
| lock-scroll | 是否禁用 body 滚动 | `boolean` | true |
| open-delay | 打开前延迟时间(ms) | `number` | 0 |
| close-delay | 关闭前延迟时间(ms) | `number` | 0 |
| close-on-click-modal | 是否可以通过点击遮罩层关| `boolean` | true |
| close-on-press-escape | 是否可以通过ESC 关闭 | `boolean` | true |
| show-close | 是否显示关闭按钮 | `boolean` | true |
| before-close | 关闭前回调 | `(done: DoneFn) => void` | |
| draggable | 是否启用拖动 | `boolean` | false |
| overflow | 拖动时是否可以超出视| `boolean` | false |
| center | 标题和页脚是否居中对| `boolean` | false |
| align-center | 对话框是否水平和垂直居中 | `boolean` | false |
| destroy-on-close | 关闭时是否销毁元| `boolean` | false |
| close-icon | 自定义关闭图| `string \| Component` | |
| z-index | 对话框 z-order | `number` | |
| header-aria-level | 标题aria-level 属性| `string` | 2 |
| transition | 自定义过渡配合| `string \| object` | dialog-fade |

### Dialog 插槽

| Name | Description |
|------|-------------|
| default | 对话框默认内容 |
| header | 对话框标题内容|
| footer | 对话框页脚内容|

### Dialog 事件

| Name | Description | Type |
|------|-------------|------|
| open | 当对话框打开时触| `() => void` |
| opened | 当打开动画结束时触| `() => void` |
| close | 当对话框关闭时触| `() => void` |
| closed | 当关闭动画结束时触发 | `() => void` |
| open-auto-focus | 对话框打开且内容获得焦点后触发 | `() => void` |
| close-auto-focus | 对话框关闭且内容获得焦点后触| `() => void` |

### Dialog 暴露

| Name | Description | Type |
|------|-------------|------|
| resetPosition | 重置位置 | `() => void` |
| handleClose | 关闭对话| `() => void` |

## 使用示例

### 基本对话
```vue
<template>
  <el-button @click="dialogVisible = true">Open Dialog</el-button>

  <el-dialog v-model="dialogVisible" title="Tips" width="30%">
    <span>This is a message</span>
    <template #footer>
      <span class="dialog-footer">
        <el-button @click="dialogVisible = false">Cancel</el-button>
        <el-button type="primary" @click="dialogVisible = false">Confirm</el-button>
      </span>
    </template>
  </el-dialog>
</template>

<script setup>
import { ref } from 'vue'

const dialogVisible = ref(false)
</script>
```

### 自定义内容
```vue
<template>
  <el-button @click="dialogVisible = true">Open Form Dialog</el-button>

  <el-dialog v-model="dialogVisible" title="Shipping Address" width="500">
    <el-form :model="form">
      <el-form-item label="Name" :label-width="formLabelWidth">
        <el-input v-model="form.name" autocomplete="off" />
      </el-form-item>
      <el-form-item label="Zone" :label-width="formLabelWidth">
        <el-select v-model="form.region" placeholder="Select">
          <el-option label="Zone No.1" value="shanghai" />
          <el-option label="Zone No.2" value="beijing" />
        </el-select>
      </el-form-item>
    </el-form>
    <template #footer>
      <el-button @click="dialogVisible = false">Cancel</el-button>
      <el-button type="primary" @click="dialogVisible = false">Confirm</el-button>
    </template>
  </el-dialog>
</template>

<script setup>
import { ref, reactive } from 'vue'

const dialogVisible = ref(false)
const formLabelWidth = '140px'

const form = reactive({
  name: '',
  region: ''
})
</script>
```

### 自定义标签
```vue
<template>
  <el-dialog v-model="dialogVisible">
    <template #header="{ titleId, titleClass }">
      <div class="my-header">
        <h4 :id="titleId" :class="titleClass">Custom Header</h4>
        <el-button type="danger" @click="dialogVisible = false">
          <el-icon class="el-icon--left"><CircleCloseFilled /></el-icon>
          Close
        </el-button>
      </div>
    </template>
    This is content
  </el-dialog>
</template>
```

### 嵌套对话
```vue
<template>
  <el-button @click="outerVisible = true">Open Outer Dialog</el-button>

  <el-dialog v-model="outerVisible" title="Outer Dialog">
    <el-button @click="innerVisible = true">Open Inner Dialog</el-button>
    
    <el-dialog
      v-model="innerVisible"
      title="Inner Dialog"
      append-to-body
    />
  </el-dialog>
</template>

<script setup>
import { ref } from 'vue'

const outerVisible = ref(false)
const innerVisible = ref(false)
</script>
```

### 居中内容

```vue
<template>
  <el-dialog v-model="dialogVisible" title="Tips" width="30%" center>
    <span>Content should be centered</span>
    <template #footer>
      <el-button @click="dialogVisible = false">Cancel</el-button>
      <el-button type="primary" @click="dialogVisible = false">Confirm</el-button>
    </template>
  </el-dialog>
</template>
```

### 居中对齐

```vue
<template>
  <el-dialog v-model="dialogVisible" title="Tips" width="30%" align-center>
    <span>Dialog is centered both horizontally and vertically</span>
  </el-dialog>
</template>
```

### 可拖动对话框

```vue
<template>
  <el-dialog v-model="dialogVisible" title="Tips" draggable>
    <span>Drag header to move the dialog</span>
  </el-dialog>
</template>
```

### 全屏

```vue
<template>
  <el-dialog v-model="dialogVisible" title="Tips" fullscreen>
    <span>Fullscreen dialog</span>
  </el-dialog>
</template>
```

### 关闭前回
```vue
<template>
  <el-dialog
    v-model="dialogVisible"
    title="Tips"
    :before-close="handleClose"
  >
    <span>This dialog will ask for confirmation before closing</span>
  </el-dialog>
</template>

<script setup>
import { ref } from 'vue'
import { ElMessageBox } from 'element-plus'

const dialogVisible = ref(false)

const handleClose = (done) => {
  ElMessageBox.confirm('Are you sure to close this dialog?')
    .then(() => {
      done()
    })
    .catch(() => {
      // catch error
    })
}
</script>
```

### 关闭时销
```vue
<template>
  <el-dialog v-model="dialogVisible" destroy-on-close>
    <span>Content will be destroyed when closed</span>
  </el-dialog>
</template>
```

### 遮罩层配合
```vue
<template>
  <!-- 无遮罩层 -->
  <el-dialog v-model="dialogVisible" :modal="false">
    <span>Dialog without modal overlay</span>
  </el-dialog>
</template>
```

### 自定义动
```vue
<template>
  <el-dialog v-model="dialogVisible" transition="el-zoom-in-top">
    <span>Dialog with custom animation</span>
  </el-dialog>
</template>
```

## 常见问题

### 1. 作用域样式不生效

对话框使Teleport,因此样式应该是全局

```vue
<style>
/* 对话框全局样式 */
.my-dialog .el-dialog__body {
  padding: 20px;
}
</style>
```

### 2. 页面滚动问题

正确使用 `lock-scroll`:

```vue
<el-dialog v-model="dialogVisible" lock-scroll>
  <!-- content -->
</el-dialog>
```

### 3. 嵌套对话框定
对嵌套对话框使用 `append-to-body`:

```vue
<el-dialog v-model="outer">
  <el-dialog v-model="inner" append-to-body />
</el-dialog>
```

## 组件交互

### 与表单配合
```vue
<template>
  <el-dialog v-model="dialogVisible" title="Edit User">
    <el-form :model="form" :rules="rules" ref="formRef">
      <el-form-item label="Name" prop="name">
        <el-input v-model="form.name" />
      </el-form-item>
    </el-form>
    <template #footer>
      <el-button @click="dialogVisible = false">Cancel</el-button>
      <el-button type="primary" @click="handleSubmit">Save</el-button>
    </template>
  </el-dialog>
</template>

<script setup>
const handleSubmit = async () => {
  try {
    await formRef.value.validate()
    await saveUser(form)
    dialogVisible.value = false
  } catch (error) {
    console.error('Validation failed')
  }
}
</script>
```

### 与表格配合
```vue
<template>
  <el-dialog v-model="dialogVisible" title="Select Items" width="70%">
    <el-table :data="tableData" @selection-change="handleSelection">
      <el-table-column type="selection" />
      <el-table-column prop="name" label="Name" />
    </el-table>
  </el-dialog>
</template>
```

## 最佳实践
1. **使用 v-model**: 始终使用 `v-model` 控制可见2. **设置宽度**: 为内容设置适当宽3. **使用 before-close**: 优雅地处理未保存更4. **关闭时销*: 使用 `destroy-on-close` 提高性能
5. **嵌套对话*: 对嵌套对话框始终使用 `append-to-body`
6. **无障碍访问*: 使用正确`title` `aria-level`
