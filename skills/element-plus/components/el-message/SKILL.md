---
name: "el-message"
description: "用于显示反馈消息消息通知组件。当用户需要显示成功、警告、错误信息消息时调用---

# Element Plus Message 组件

显示全局消息作为对用户操作反馈
## 何时调用

在以下情况下调用此技能
- 用户需要显示反馈消- 用户想要显示成功/错误通知
- 用户需要可关闭消- 用户想要自定义消息内容- 用户询问消息定位

## 功能特性
- **多种类型**: success、warning、info、error
- **可关*: 手动自动关- **自定义持续时*: 可配置显示时间
- **HTML 内容**: 支持 HTML 字符- **自定义图*: 自定义图标支- **多种位置**: 顶部、底部、左侧、右侧位- **分组**: 合并相似消息
- **VNode 支持**: 渲染 Vue 组件作为内容

## API 参考
### Message 选项

| Name | Description | Type | Default |
|------|-------------|------|---------|
| message | 消息文本 | `string \| VNode \| Function` | '' |
| type | 消息类型 | `'primary' \| 'success' \| 'warning' \| 'info' \| 'error'` | info |
| icon | 自定义图标组件| `string \| Component` | |
| dangerouslyUseHTMLString | 是否message 属性作HTML 片段处理 | `boolean` | false |
| customClass | 自定义类型| `string` | '' |
| duration | 显示时间(毫秒),0 表示不自动关| `number` | 3000 |
| showClose | 是否显示关闭按钮 | `boolean` | false |
| center | 文字是否居中 | `boolean` | false |
| onClose | 关闭时回调函数 | `() => void` | |
| offset | 距离视口边缘偏移量 | `number` | 16 |
| appendTo | 消息挂载元| `string \| HTMLElement` | |
| grouping | 合并内容相同消| `boolean` | false |
| placement | 消息位置 | `'top' \| 'top-left' \| 'top-right' \| 'bottom' \| 'bottom-left' \| 'bottom-right'` | top |

### Message 方法

| Method | Description | Parameters | Return |
|--------|-------------|------------|--------|
| ElMessage | 显示消息 | `options` | Message 实现例 |
| ElMessage.success | 显示成功消息 | `message \| options` | Message 实现例 |
| ElMessage.warning | 显示警告消息 | `message \| options` | Message 实现例 |
| ElMessage.info | 显示信息消息 | `message \| options` | Message 实现例 |
| ElMessage.error | 显示错误消息 | `message \| options` | Message 实现例 |
| ElMessage.closeAll | 关闭所有消| | |

### Message 实现例

| Method | Description | Type |
|--------|-------------|------|
| close | 关闭消息 | `() => void` |

## 使用示例

### 基本用法

```vue
<template>
  <el-button @click="open">Show Message</el-button>
</template>

<script setup>
import { ElMessage } from 'element-plus'

const open = () => {
  ElMessage('This is a message.')
}
</script>
```

### 不同类型

```vue
<template>
  <el-button @click="openSuccess">Success</el-button>
  <el-button @click="openWarning">Warning</el-button>
  <el-button @click="openInfo">Info</el-button>
  <el-button @click="openError">Error</el-button>
</template>

<script setup>
import { ElMessage } from 'element-plus'

const openSuccess = () => {
  ElMessage.success('Success message')
}

const openWarning = () => {
  ElMessage.warning('Warning message')
}

const openInfo = () => {
  ElMessage.info('Info message')
}

const openError = () => {
  ElMessage.error('Error message')
}
</script>
```

### 可关闭消
```vue
<template>
  <el-button @click="open">Show Closable Message</el-button>
</template>

<script setup>
import { ElMessage } from 'element-plus'

const open = () => {
  ElMessage({
    showClose: true,
    message: 'This is a closable message.',
    type: 'success'
  })
}
</script>
```

### 自定义持续时
```vue
<template>
  <el-button @click="open">Show Message (5s)</el-button>
</template>

<script setup>
import { ElMessage } from 'element-plus'

const open = () => {
  ElMessage({
    message: 'This message will close after 5 seconds.',
    duration: 5000
  })
}
</script>
```

### 居中文本

```vue
<template>
  <el-button @click="open">Centered Message</el-button>
</template>

<script setup>
import { ElMessage } from 'element-plus'

const open = () => {
  ElMessage({
    message: 'Centered text',
    center: true
  })
}
</script>
```

### HTML 内容

```vue
<template>
  <el-button @click="open">HTML Message</el-button>
</template>

<script setup>
import { ElMessage } from 'element-plus'

const open = () => {
  ElMessage({
    dangerouslyUseHTMLString: true,
    message: '<strong>This is <i>HTML</i> content</strong>'
  })
}
</script>
```

### 自定义图
```vue
<template>
  <el-button @click="open">Custom Icon</el-button>
</template>

<script setup>
import { ElMessage } from 'element-plus'
import { SuccessFilled } from '@element-plus/icons-vue'

const open = () => {
  ElMessage({
    message: 'Custom icon message',
    icon: SuccessFilled
  })
}
</script>
```

### 自定义偏
```vue
<template>
  <el-button @click="open">Offset Message</el-button>
</template>

<script setup>
import { ElMessage } from 'element-plus'

const open = () => {
  ElMessage({
    message: 'This message has offset 100px',
    offset: 100
  })
}
</script>
```

### 不同位置

```vue
<template>
  <el-button @click="openTop">Top</el-button>
  <el-button @click="openTopLeft">Top Left</el-button>
  <el-button @click="openTopRight">Top Right</el-button>
  <el-button @click="openBottom">Bottom</el-button>
</template>

<script setup>
import { ElMessage } from 'element-plus'

const openTop = () => {
  ElMessage({ message: 'Top message', placement: 'top' })
}

const openTopLeft = () => {
  ElMessage({ message: 'Top left message', placement: 'top-left' })
}

const openTopRight = () => {
  ElMessage({ message: 'Top right message', placement: 'top-right' })
}

const openBottom = () => {
  ElMessage({ message: 'Bottom message', placement: 'bottom' })
}
</script>
```

### 分组消息

```vue
<template>
  <el-button @click="open">Grouping</el-button>
</template>

<script setup>
import { ElMessage } from 'element-plus'

const open = () => {
  ElMessage({
    message: 'This message will be grouped',
    grouping: true
  })
}
// 多次点击只会显示一条消}
</script>
```

### VNode 内容

```vue
<template>
  <el-button @click="open">VNode Message</el-button>
</template>

<script setup>
import { ElMessage, h } from 'element-plus'

const open = () => {
  ElMessage({
    message: h('p', null, [
      h('span', null, 'Message can be '),
      h('i', { style: 'color: teal' }, 'VNode')
    ])
  })
}
</script>
```

### 关闭所有消
```vue
<template>
  <el-button @click="open">Show Multiple</el-button>
  <el-button @click="closeAll">Close All</el-button>
</template>

<script setup>
import { ElMessage } from 'element-plus'

const open = () => {
  ElMessage.success('Message 1')
  ElMessage.warning('Message 2')
  ElMessage.info('Message 3')
}

const closeAll = () => {
  ElMessage.closeAll()
}
</script>
```

### 使用实现例

```vue
<template>
  <el-button @click="open">Show Message</el-button>
  <el-button @click="close">Close Message</el-button>
</template>

<script setup>
import { ElMessage } from 'element-plus'

let messageInstance = null

const open = () => {
  messageInstance = ElMessage({
    message: 'This message will be closed programmatically',
    duration: 0  // 不自动关  })
}

const close = () => {
  if (messageInstance) {
    messageInstance.close()
    messageInstance = null
  }
}
</script>
```

### 与异步操作配合
```vue
<template>
  <el-button @click="saveData">Save</el-button>
</template>

<script setup>
import { ElMessage } from 'element-plus'

const saveData = async () => {
  const loading = ElMessage({
    message: 'Saving...',
    duration: 0
  })
  
  try {
    await api.save()
    loading.close()
    ElMessage.success('Saved successfully!')
  } catch (error) {
    loading.close()
    ElMessage.error('Save failed!')
  }
}
</script>
```

### 表单验证反馈

```vue
<template>
  <el-form ref="formRef" :model="form" :rules="rules">
    <el-form-item prop="email">
      <el-input v-model="form.email" />
    </el-form-item>
    <el-button @click="submit">Submit</el-button>
  </el-form>
</template>

<script setup>
import { ref, reactive } from 'vue'
import { ElMessage } from 'element-plus'

const formRef = ref()
const form = reactive({ email: '' })

const submit = async () => {
  try {
    await formRef.value.validate()
    await api.submit(form)
    ElMessage.success('Form submitted successfully!')
  } catch (error) {
    ElMessage.error('Please check form for errors')
  }
}
</script>
```

## 常见问题

### 1. 消息不显
确保已导Element Plus 样式:

```js
import 'element-plus/dist/index.css'
```

### 2. 多条消息堆叠

使用 `grouping: true` 合并相似消息:

```js
ElMessage({ message: 'Same message', grouping: true })
```

### 3. 消息不关
检`duration` 设置。使`duration: 0` 进行手动关闭:

```js
const msg = ElMessage({ message: 'Manual close', duration: 0 })
// 稍后
msg.close()
```

## 最佳实践
1. **使用适当类型*: 适当使用 success、warning、error、info
2. **保持消息简*: 消息应简洁明3. **使用分组**: 对重复操作启用分4. **处理异步操作**: 在异步操作期间显示加载消5. **编程方式关闭**: 使用实现例 `close()` 进行手动控制
6. **避免 HTML**: 出于安全考虑,优先使用 VNode 而不HTML 字符