---
name: "el-button"
description: "具有多种类型、尺寸和样式按钮组件。当用户需要实现按钮、按钮组或自定义按钮外观时调用
---

# Element Plus Button 组件

用于用户交互的按钮组件，支持多种类型、尺寸和样式
## 何时调用

在以下情况下调用此技能
- 用户需要实现具有特定类型或样式的按钮- 用户想要创建按钮- 用户需要加载或禁用状态的按钮
- 用户想要自定义按钮颜色- 用户询问按钮图标或无障碍访问

## 功能特性
- **多种类型**: default、primary、success、warning、danger、info
- **多种尺寸**: large、default、small
- **样式变体**: plain、round、circle、dashed、text、link
- **状态*: loading、disabled
- **自定义颜色*: 自动计算 hover/active 颜色
- **按钮*: 水平垂直分- **图标支持**: 前缀图标和自定义加载图标
- **标签自定*: 自定义元素标签button、a、router-link 

## API 参考
### Button 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| size | 按钮尺寸 | `'large' \| 'default' \| 'small'` | |
| type | 按钮类型,当设`color` 后者优| `'default' \| 'primary' \| 'success' \| 'warning' \| 'danger' \| 'info' \| '' \| 'text' (已弃` | |
| plain | 是否为朴素按钮| `boolean` | false |
| text | 是否为文本按钮| `boolean` | false |
| bg | 文本按钮背景色是否始终显| `boolean` | false |
| link | 是否为链接按钮| `boolean` | false |
| round | 是否为圆角按钮| `boolean` | false |
| circle | 是否为圆形按钮| `boolean` | false |
| dashed | 是否为虚线边框按钮| `boolean` | false |
| loading | 是否为加载中状态| `boolean` | false |
| loading-icon | 自定义加载图标组件| `string \| Component` | Loading |
| disabled | 是否禁用按钮 | `boolean` | false |
| icon | 图标组件 | `string \| Component` | |
| autofocus | 同原生按钮 `autofocus` | `boolean` | false |
| native-type | 同原生按钮 `type` | `'button' \| 'submit' \| 'reset'` | button |
| auto-insert-space | 自动在两个中文字符之间插入空| `boolean` | false |
| color | 自定义按钮颜自动计算 `hover` `active` 颜色 | `string` | |
| dark | 暗黑模式,自动`color` 转换为暗黑模式颜色| `boolean` | false |
| tag | 自定义元素标签| `string \| Component` | button |

### Button 插槽

| Name | Description |
|------|-------------|
| default | 自定义默认内容|
| loading | 自定义加载组件|
| icon | 自定义图标组件|

### Button 暴露

| Name | Description | Type |
|------|-------------|------|
| ref | 按钮 HTML 元素 | `Ref<HTMLButtonElement>` |
| size | 按钮尺寸 | `ComputedRef<'' \| 'small' \| 'default' \| 'large'>` |
| type | 按钮类型 | `ComputedRef<'' \| 'default' \| 'primary' \| 'success' \| 'warning' \| 'info' \| 'danger' \| 'text'>` |
| disabled | 按钮禁用状态| `ComputedRef<boolean>` |
| shouldAddSpace | 是否添加空格 | `ComputedRef<boolean>` |

### ButtonGroup 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| size | 控制按钮组中按钮尺寸| `'large' \| 'default' \| 'small'` | |
| type | 控制按钮组中按钮类型| `'primary' \| 'success' \| 'warning' \| 'danger' \| 'info'` | |
| direction | 显示方向 | `'horizontal' \| 'vertical'` | horizontal |

### ButtonGroup 插槽

| Name | Description | Subtags |
|------|-------------|---------|
| default | 自定义按钮组内容 | Button |

## 使用示例

### 基本按钮类型

```vue
<template>
  <el-row class="mb-4">
    <el-button>Default</el-button>
    <el-button type="primary">Primary</el-button>
    <el-button type="success">Success</el-button>
    <el-button type="warning">Warning</el-button>
    <el-button type="danger">Danger</el-button>
    <el-button type="info">Info</el-button>
  </el-row>
</template>
```

### 朴素按钮

```vue
<template>
  <el-row class="mb-4">
    <el-button plain>Plain</el-button>
    <el-button type="primary" plain>Primary</el-button>
    <el-button type="success" plain>Success</el-button>
    <el-button type="warning" plain>Warning</el-button>
    <el-button type="danger" plain>Danger</el-button>
    <el-button type="info" plain>Info</el-button>
  </el-row>
</template>
```

### 圆角按钮

```vue
<template>
  <el-row class="mb-4">
    <el-button round>Round</el-button>
    <el-button type="primary" round>Primary</el-button>
    <el-button type="success" round>Success</el-button>
    <el-button type="warning" round>Warning</el-button>
    <el-button type="danger" round>Danger</el-button>
    <el-button type="info" round>Info</el-button>
  </el-row>
</template>
```

### 圆形按钮

```vue
<template>
  <el-row class="mb-4">
    <el-button :icon="Search" circle />
    <el-button type="primary" :icon="Edit" circle />
    <el-button type="success" :icon="Check" circle />
    <el-button type="info" :icon="Message" circle />
    <el-button type="warning" :icon="Star" circle />
    <el-button type="danger" :icon="Delete" circle />
  </el-row>
</template>

<script setup>
import { Search, Edit, Check, Message, Star, Delete } from '@element-plus/icons-vue'
</script>
```

### 禁用按钮

```vue
<template>
  <el-row class="mb-4">
    <el-button disabled>Default</el-button>
    <el-button type="primary" disabled>Primary</el-button>
    <el-button type="success" disabled>Success</el-button>
  </el-row>
</template>
```

### 加载中按钮
```vue
<template>
  <el-row class="mb-4">
    <el-button type="primary" :loading="loading" @click="handleClick">
      {{ loading ? 'Loading...' : 'Click me' }}
    </el-button>
  </el-row>
</template>

<script setup>
import { ref } from 'vue'

const loading = ref(false)

const handleClick = () => {
  loading.value = true
  setTimeout(() => {
    loading.value = false
  }, 2000)
}
</script>
```

### 按钮尺寸

```vue
<template>
  <el-row class="mb-4">
    <el-button size="large">Large</el-button>
    <el-button>Default</el-button>
    <el-button size="small">Small</el-button>
  </el-row>
</template>
```

### 按钮
```vue
<template>
  <el-button-group>
    <el-button type="primary" :icon="ArrowLeft">Previous</el-button>
    <el-button type="primary">
      Next<el-icon class="el-icon--right"><ArrowRight /></el-icon>
    </el-button>
  </el-button-group>

  <el-button-group class="ml-4">
    <el-button type="primary" :icon="Edit" />
    <el-button type="primary" :icon="Share" />
    <el-button type="primary" :icon="Delete" />
  </el-button-group>
</template>

<script setup>
import { ArrowLeft, ArrowRight, Edit, Share, Delete } from '@element-plus/icons-vue'
</script>
```

### 垂直按钮
```vue
<template>
  <el-button-group direction="vertical">
    <el-button type="primary">Top</el-button>
    <el-button type="primary">Middle</el-button>
    <el-button type="primary">Bottom</el-button>
  </el-button-group>
</template>
```

### 自定义颜色
```vue
<template>
  <el-row class="mb-4">
    <el-button color="#626aef">Default</el-button>
    <el-button color="#626aef" plain>Plain</el-button>
    <el-button color="#626aef" disabled>Disabled</el-button>
  </el-row>
</template>
```

### 文本按钮

```vue
<template>
  <el-row class="mb-4">
    <el-button text>Text Button</el-button>
    <el-button text bg>Text Button with Background</el-button>
    <el-button type="primary" text>Primary Text</el-button>
  </el-row>
</template>
```

### 链接按钮

```vue
<template>
  <el-row class="mb-4">
    <el-button link>Default Link</el-button>
    <el-button type="primary" link>Primary Link</el-button>
    <el-button type="success" link>Success Link</el-button>
    <el-button type="warning" link>Warning Link</el-button>
    <el-button type="danger" link>Danger Link</el-button>
    <el-button type="info" link>Info Link</el-button>
  </el-row>
</template>
```

### 自定义标签
```vue
<template>
  <el-row class="mb-4">
    <el-button tag="div">Div Button</el-button>
    <el-button type="primary" tag="a" href="https://element-plus.org">
      A Link Button
    </el-button>
    <el-button type="success" tag="router-link" to="/home">
      Router Link
    </el-button>
  </el-row>
</template>
```

## 常见问题

### 1. 按钮宽度不一
使用图标加载状态时,按钮宽度可能会变化。设置固定宽

```vue
<el-button style="width: 100px">Button</el-button>
```

### 2. 文本按钮弃用警告

`type="text"` 已弃用。使`text` 属性代

```vue
<!-- 已弃-->
<el-button type="text">Text Button</el-button>

<!-- 推荐 -->
<el-button text>Text Button</el-button>
```

### 3. 暗黑模式下自定义颜色
使用 `dark` 属性启用暗黑模式颜色转

```vue
<el-button color="#626aef" dark>Dark Mode Button</el-button>
```

## 组件交互

### 与表单配合
```vue
<template>
  <el-form>
    <el-form-item>
      <el-button type="primary" native-type="submit">Submit</el-button>
      <el-button native-type="reset">Reset</el-button>
    </el-form-item>
  </el-form>
</template>
```

### 与对话框配合

```vue
<template>
  <el-button type="primary" @click="dialogVisible = true">
    Open Dialog
  </el-button>
  
  <el-dialog v-model="dialogVisible">
    <template #footer>
      <el-button @click="dialogVisible = false">Cancel</el-button>
      <el-button type="primary" @click="handleConfirm">Confirm</el-button>
    </template>
  </el-dialog>
</template>
```

### 与加载状态配合
```vue
<template>
  <el-button 
    type="primary" 
    :loading="submitting" 
    @click="handleSubmit"
  >
    {{ submitting ? 'Submitting...' : 'Submit' }}
  </el-button>
</template>

<script setup>
import { ref } from 'vue'

const submitting = ref(false)

const handleSubmit = async () => {
  submitting.value = true
  try {
    await submitData()
  } finally {
    submitting.value = false
  }
}
</script>
```

## 最佳实践
1. **使用语义化类型*: 为不同操作使用适当类型主要操作使用 primary,破坏性操作使danger)
2. **提供反馈**: 为异步操作使用加载状态3. **无障碍访问*: 为仅包含图标按钮添aria-label
4. **一致尺寸**: 在同一上下文中使用一致尺寸
5. **按钮*: 对相关操作使ButtonGroup
