---
name: "el-input"
description: "用于文本输入输入框组件,支持多种类型、验证和格式化。当用户需要实现文本输入、文本域密码输入时调用---

# Element Plus Input 组件

用于通过鼠标签键盘输入文本数据输入框组件
## 何时调用

在以下情况下调用此技能
- 用户需要实现文本输入框
- 用户想要创建自动调整大小文本域
- 用户需要可切换可见性密码输入- 用户想要添加前缀/后缀图标内容- 用户需要输入验证格式- 用户询问输入长度限制

## 功能特性
- **多种类型**: text、textarea、password、number、email、search、tel、url
- **尺寸变体**: large、default、small
- **可清*: 一键清空功- **密码切换**: 显示/隐藏密码
- **字数限制**: 字符计数显示
- **自动调整文本*: 自适应高度
- **格式化器**: 值格式化和解- **前缀/后缀**: 图标自定义内容
- **前置/后置**: 复合输入
## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| type | 输入框类型| `'text' \| 'textarea' \| 'number' \| 'password' \| 'email' \| 'search' \| 'tel' \| 'url'` | text |
| model-value / v-model | 绑定| `string \| number` | |
| model-modifiers | v-model 修饰| `{ lazy?: true, number?: true, trim?: true }` | |
| maxlength | 同原input `maxlength` | `string \| number` | |
| minlength | 同原input `minlength` | `string \| number` | |
| show-word-limit | 是否显示字数统计 | `boolean` | false |
| word-limit-position | 字数统计位置 | `'inside' \| 'outside'` | inside |
| placeholder | 输入框占位文| `string` | |
| clearable | 是否显示清空按钮 | `boolean` | false |
| clear-icon | 自定义清空图标组件| `string \| Component` | CircleClose |
| formatter | 指定值格式 | `(value: string \| number) => string` | |
| parser | 指定从格式化器输入中提取| `(value: string) => string` | |
| show-password | 是否显示可切换密码输入| `boolean` | false |
| disabled | 是否禁用输入| `boolean` | false |
| size | 输入框尺寸| `'large' \| 'default' \| 'small'` | |
| prefix-icon | 前置图标组件 | `string \| Component` | |
| suffix-icon | 后置图标组件 | `string \| Component` | |
| rows | 文本域行| `number` | 2 |
| autosize | 文本域是否自适应高度 | `boolean \| { minRows?: number, maxRows?: number }` | false |
| autocomplete | 同原input `autocomplete` | `string` | off |
| name | 同原input `name` | `string` | |
| readonly | 同原input `readonly` | `boolean` | false |
| max | 同原input `max` | | |
| min | 同原input `min` | | |
| step | 同原input `step` | | |
| resize | 控制可调整大| `'none' \| 'both' \| 'horizontal' \| 'vertical'` | |
| autofocus | 同原input `autofocus` | `boolean` | false |
| form | 同原input `form` | `string` | |
| aria-label | 同原input `aria-label` | `string` | |
| tabindex | 输入tabindex | `string \| number` | |
| validate-event | 是否触发表单验证 | `boolean` | true |
| input-style | 输入框元素样式 | `string \| CSSProperties \| CSSProperties[] \| string[]` | {} |
| inputmode | 同原input `inputmode` | `string` | |

### 事件

| Name | Description | Type |
|------|-------------|------|
| blur | Input 失去焦点时触| `(event: FocusEvent) => void` |
| focus | Input 获得焦点时触| `(event: FocusEvent) => void` |
| change | 当输入框失去焦点用户按下回车时触发 | `(value: string \| number, evt?: Event) => void` |
| input | Input 值改变时触发 | `(value: string \| number) => void` |
| clear | Input 被清空时触发 | `(evt?: MouseEvent) => void` |
| keydown | 当按下键盘键时触| `(event: KeyboardEvent \| Event) => void` |
| mouseleave | 当鼠标离开 Input 元素时触| `(event: MouseEvent) => void` |
| mouseenter | 当鼠标进Input 元素时触| `(event: MouseEvent) => void` |

### 插槽

| Name | Description |
|------|-------------|
| prefix | 输入框前置内容仅在 `type` 不是 'textarea' 时有|
| suffix | 输入框后置内容仅在 `type` 不是 'textarea' 时有|
| prepend | 输入框前置标签仅在 `type` 不是 'textarea' 时有|
| append | 输入框后置标签仅在 `type` 不是 'textarea' 时有|
| password-icon | 输入框密码图标内容仅在 `show-password` true 时有|

### 暴露

| Name | Description | Type |
|------|-------------|------|
| blur | 使输入框失去焦点 | `() => void` |
| clear | 清空输入框| `() => void` |
| focus | 使输入框获得焦点 | `() => void` |
| input | HTML input 元素 | `Ref<HTMLInputElement>` |
| ref | HTML 元素,input textarea | `Ref<HTMLInputElement \| HTMLTextAreaElement>` |
| resizeTextarea | 调整文本域大| `() => void` |
| select | 选中输入框中文| `() => void` |
| textarea | HTML textarea 元素 | `Ref<HTMLTextAreaElement>` |
| textareaStyle | 文本域样式| `Ref<StyleValue>` |
| isComposing | 是否正在输入 | `Ref<boolean>` |

## 使用示例

### 基本输入
```vue
<template>
  <el-input v-model="input" placeholder="Please input" />
</template>

<script setup>
import { ref } from 'vue'

const input = ref('')
</script>
```

### 禁用输入
```vue
<template>
  <el-input v-model="input" disabled placeholder="Disabled input" />
</template>
```

### 可清空输入框

```vue
<template>
  <el-input v-model="input" clearable placeholder="Clearable input" />
</template>
```

### 密码输入
```vue
<template>
  <el-input 
    v-model="password" 
    type="password" 
    show-password 
    placeholder="Please input password" 
  />
</template>

<script setup>
import { ref } from 'vue'

const password = ref('')
</script>
```

### 带图标输入
```vue
<template>
  <el-input 
    v-model="input" 
    placeholder="Pick a date"
    :prefix-icon="Calendar"
  />
  
  <el-input 
    v-model="search" 
    placeholder="Search"
    :suffix-icon="Search"
  />
</template>

<script setup>
import { ref } from 'vue'
import { Calendar, Search } from '@element-plus/icons-vue'

const input = ref('')
const search = ref('')
</script>
```

### 带插槽输入
```vue
<template>
  <el-input v-model="input" placeholder="Please input">
    <template #prepend>Http://</template>
  </el-input>
  
  <el-input v-model="input2" placeholder="Please input">
    <template #append>.com</template>
  </el-input>
  
  <el-input v-model="input3" placeholder="Please input">
    <template #prepend>
      <el-select v-model="select" style="width: 100px">
        <el-option label="Restaurant" value="1" />
        <el-option label="Order No." value="2" />
      </el-select>
    </template>
    <template #append>
      <el-button :icon="Search" />
    </template>
  </el-input>
</template>

<script setup>
import { ref } from 'vue'
import { Search } from '@element-plus/icons-vue'

const input = ref('')
const input2 = ref('')
const input3 = ref('')
const select = ref('1')
</script>
```

### 文本
```vue
<template>
  <el-input
    v-model="textarea"
    type="textarea"
    :rows="4"
    placeholder="Please input"
  />
</template>

<script setup>
import { ref } from 'vue'

const textarea = ref('')
</script>
```

### 自动调整大小文本域

```vue
<template>
  <el-input
    v-model="textarea"
    type="textarea"
    :autosize="{ minRows: 2, maxRows: 6 }"
    placeholder="Auto-resize textarea"
  />
</template>
```

### 字数限制

```vue
<template>
  <el-input
    v-model="input"
    maxlength="20"
    show-word-limit
    placeholder="Please input"
  />
  
  <el-input
    v-model="textarea"
    type="textarea"
    maxlength="100"
    show-word-limit
    placeholder="Please input"
  />
</template>
```

### 格式化器

```vue
<template>
  <el-input
    v-model="input"
    :formatter="value => `$ ${value}`.replace(/\B(?=(\d{3})+(?!\d))/g, ',')"
    :parser="value => value.replace(/\$\s?|(,*)/g, '')"
    placeholder="Please input amount"
  />
</template>
```

### 尺寸

```vue
<template>
  <el-input v-model="input1" size="large" placeholder="Large" />
  <el-input v-model="input2" placeholder="Default" />
  <el-input v-model="input3" size="small" placeholder="Small" />
</template>
```

### v-model 修饰
```vue
<template>
  <!-- Lazy: change 事件时更-->
  <el-input v-model.lazy="lazyValue" placeholder="Lazy update" />
  
  <!-- Number: 转换为数-->
  <el-input v-model.number="numberValue" type="number" placeholder="Number" />
  
  <!-- Trim: 去除首尾空格 -->
  <el-input v-model.trim="trimValue" placeholder="Trimmed" />
</template>
```

## 常见问题

### 1. 输入框宽度随可清空图标扩
当可清空图标出现输入框宽度可能会扩展。设置固定宽

```vue
<el-input v-model="input" clearable style="width: 200px" />
```

### 2. 文本域高度问
使用 `autosize` 实现动态高

```vue
<el-input type="textarea" :autosize="{ minRows: 2, maxRows: 6 }" />
```

### 3. 中文输入法问
使用 `isComposing` 暴露属性检查输入状态

```vue
<template>
  <el-input ref="inputRef" v-model="input" @keydown="handleKeydown" />
</template>

<script setup>
import { ref } from 'vue'

const inputRef = ref()

const handleKeydown = () => {
  if (inputRef.value?.isComposing) return
  // 处理 keydown
}
</script>
```

## 组件交互

### 与表单验证配合
```vue
<template>
  <el-form :model="form" :rules="rules" ref="formRef">
    <el-form-item label="Username" prop="username">
      <el-input v-model="form.username" />
    </el-form-item>
    <el-form-item label="Password" prop="password">
      <el-input v-model="form.password" type="password" show-password />
    </el-form-item>
  </el-form>
</template>

<script setup>
import { ref, reactive } from 'vue'

const formRef = ref()
const form = reactive({
  username: '',
  password: ''
})

const rules = {
  username: [
    { required: true, message: 'Please input username', trigger: 'blur' }
  ],
  password: [
    { required: true, message: 'Please input password', trigger: 'blur' },
    { min: 6, message: 'Password must be at least 6 characters', trigger: 'blur' }
  ]
}
</script>
```

### 与搜索配合
```vue
<template>
  <el-input
    v-model="searchQuery"
    placeholder="Search..."
    :prefix-icon="Search"
    clearable
    @input="handleSearch"
  />
</template>

<script setup>
import { ref } from 'vue'
import { Search } from '@element-plus/icons-vue'

const searchQuery = ref('')

const handleSearch = (value) => {
  // 防抖搜索
  console.log('Searching:', value)
}
</script>
```

## 最佳实践
1. **使用适当类型*: 使用正确输入类型以获得更好移动端键盘
2. **提供占位*: 始终提供有意义占位符文3. **验证输入**: 对必填字段使用表单验4. **限制长度**: 对有界输入使maxlength
5. **搜索可清*: 使搜索输入框可清6. **密码可见*: 对密码字段使show-password
