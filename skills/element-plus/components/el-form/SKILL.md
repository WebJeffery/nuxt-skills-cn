---
name: "el-form"
description: "用于数据收集、验证和提交表单组件。当用户需要创建表单、实现验证处理表单数据时调用---

# Element Plus Form 组件

表单由输入框、单选框、选择框、复选框等组成。通过表单,您可以收集、验证和提交数据
## 何时调用

在以下情况下调用此技能
- 用户需要创建包含多个输入字段表单
- 用户想要实现表单验证
- 用户需要处理表单提- 用户想要创建行内表单
- 用户询问表单项布局和对- 用户需要动态表单项(添加/删除)

## 功能特性
- **灵活布局**: 行内、垂直水平标签对齐
- **全面验证**: 使用 async-validator 内置验- **自定义规*: 支持自定义验证函- **动态项**: 动态添加删除表单- **尺寸控制**: 所有表单组件一致尺寸- **无障碍访问*: 支持屏幕阅读器 ARIA
- **错误显示**: 行内容工具提示错误消
## API 参考
### Form 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model | 表单数据对象 | `Record<string, any>` | |
| rules | 表单验证规则 | `FormRules` | |
| inline | 是否为行内表| `boolean` | false |
| label-position | 表单域标签位置 | `'left' \| 'right' \| 'top'` | right |
| label-width | 标签宽| `string \| number` | '' |
| label-suffix | 标签后缀 | `string` | '' |
| hide-required-asterisk | 是否隐藏必填星号 | `boolean` | false |
| require-asterisk-position | 星号位| `'left' \| 'right'` | left |
| show-message | 是否显示校验错误信息 | `boolean` | true |
| inline-message | 是否以行内形式显示校验信| `boolean` | false |
| status-icon | 是否在输入框中显示校验结果反馈图| `boolean` | false |
| validate-on-rule-change | 是否rules 属性改变后立即触发一次验| `boolean` | true |
| size | 用于控制该表单内组件尺寸| `'' \| 'large' \| 'default' \| 'small'` | |
| disabled | 是否禁用该表单内容所有组件| `boolean` | false |
| scroll-to-error | 当校验失败时,滚动到第一个错误表单项 | `boolean` | false |
| scroll-into-view-options | scrollIntoView 选项 | `ScrollIntoViewOptions \| boolean` | true |

### Form 事件

| Name | Description | Type |
|------|-------------|------|
| validate | 任一表单项被校验后触| `(prop: FormItemProp, isValid: boolean, message: string) => void` |

### Form 插槽

| Name | Description | Subtags |
|------|-------------|---------|
| default | 自定义默认内容| FormItem |

### Form 暴露

| Name | Description | Type |
|------|-------------|------|
| validate | 对整个表单进行校| `(callback?) => Promise<void>` |
| validateField | 校验指定表单项 | `(props?, callback?) => FormValidationResult` |
| resetFields | 重置指定表单项 | `(props?) => void` |
| scrollToField | 滚动到指定表单| `(prop: FormItemProp) => void` |
| clearValidate | 清除校验结果 | `(props?) => void` |
| fields | 获取所有表单项上下| `FormItemContext[]` |
| getField | 获取表单项上下文 | `(prop: FormItemProp) => FormItemContext \| undefined` |
| setInitialValues | 为表单字段设置初始| `(initModel: Record<string, any>) => void` |

### FormItem 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| prop | 表单model 字段属性名 | `string \| string[]` | |
| label | 标签文本 | `string` | |
| label-position | 表单项标签位置 | `'left' \| 'right' \| 'top'` | '' |
| label-width | 标签宽| `string \| number` | |
| required | 是否必填 | `boolean` | |
| rules | 表单验证规则 | `Arrayable<FormItemRule>` | |
| error | 表单域验证错误信| `string` | |
| show-message | 是否显示校验错误信息 | `boolean` | true |
| inline-message | 是否以行内形式展示校验信| `boolean` | false |
| size | 用于控制该表单域下组件尺寸 | `'' \| 'large' \| 'default' \| 'small'` | |
| for | 等同于原label 标签for 属性| `string` | |
| validate-status | 校验状态| `'' \| 'error' \| 'validating' \| 'success'` | |

### FormItemRule

| Name | Description | Type | Default |
|------|-------------|------|---------|
| required | 是否必填 | `boolean` | |
| message | 错误提示信息 | `string` | |
| trigger | 校验触发方式 | `'blur' \| 'change'` | |
| min | 最小长| `number` | |
| max | 最大长| `number` | |
| len | 精确长度 | `number` | |
| type | 字段类型 | `'string' \| 'number' \| 'boolean' \| 'email' \| ...` | 'string' |
| pattern | 正则表达| `RegExp` | |
| validator | 自定义校验函| `(rule, value, callback) => void` | |
| enum | 枚举| `any[]` | |
| whitespace | 将空白视为空 | `boolean` | |
| transform | 校验前转换| `(value) => any` | |
| fields | 嵌套对象校验 | `Record<string, Rule>` | |

### FormItem 插槽

| Name | Description | Type |
|------|-------------|------|
| default | 表单项内容 | |
| label | 标签位置显示内容| `{ label: string }` |
| error | 自定义校验结果显示内容 | `{ error: string }` |

### FormItem 暴露

| Name | Description | Type |
|------|-------------|------|
| size | 表单项尺寸| `ComputedRef<'' \| 'large' \| 'default' \| 'small'>` |
| validateMessage | 校验消息 | `Ref<string>` |
| validateState | 校验状态| `Ref<'' \| 'error' \| 'validating' \| 'success'>` |
| validate | 校验表单| `(trigger, callback?) => FormValidationResult` |
| resetField | 重置当前字段 | `() => void` |
| clearValidate | 移除校验状态| `() => void` |
| setInitialValue | 设置初始| `(value: any) => void` |

## 使用示例

### 基本表单

```vue
<template>
  <el-form :model="form" label-width="120px">
    <el-form-item label="Activity name">
      <el-input v-model="form.name" />
    </el-form-item>
    <el-form-item label="Activity zone">
      <el-select v-model="form.region" placeholder="Select">
        <el-option label="Zone one" value="shanghai" />
        <el-option label="Zone two" value="beijing" />
      </el-select>
    </el-form-item>
    <el-form-item label="Activity time">
      <el-col :span="11">
        <el-date-picker v-model="form.date1" type="date" placeholder="Pick a date" style="width: 100%" />
      </el-col>
      <el-col :span="2" class="text-center">
        <span class="text-gray-500">-</span>
      </el-col>
      <el-col :span="11">
        <el-time-picker v-model="form.date2" placeholder="Pick a time" style="width: 100%" />
      </el-col>
    </el-form-item>
    <el-form-item label="Instant delivery">
      <el-switch v-model="form.delivery" />
    </el-form-item>
    <el-form-item label="Activity type">
      <el-checkbox-group v-model="form.type">
        <el-checkbox label="Online activities" name="type" />
        <el-checkbox label="Promotion activities" name="type" />
      </el-checkbox-group>
    </el-form-item>
    <el-form-item label="Resources">
      <el-radio-group v-model="form.resource">
        <el-radio label="Sponsor" />
        <el-radio label="Venue" />
      </el-radio-group>
    </el-form-item>
    <el-form-item label="Activity form">
      <el-input v-model="form.desc" type="textarea" />
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="onSubmit">Create</el-button>
      <el-button>Cancel</el-button>
    </el-form-item>
  </el-form>
</template>

<script setup>
import { reactive } from 'vue'

const form = reactive({
  name: '',
  region: '',
  date1: '',
  date2: '',
  delivery: false,
  type: [],
  resource: '',
  desc: '',
})

const onSubmit = () => {
  console.log('submit!', form)
}
</script>
```

### 表单验证

```vue
<template>
  <el-form :model="form" :rules="rules" ref="formRef" label-width="120px">
    <el-form-item label="Name" prop="name">
      <el-input v-model="form.name" />
    </el-form-item>
    <el-form-item label="Email" prop="email">
      <el-input v-model="form.email" />
    </el-form-item>
    <el-form-item label="Age" prop="age">
      <el-input v-model.number="form.age" />
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="submitForm">Submit</el-button>
      <el-button @click="resetForm">Reset</el-button>
    </el-form-item>
  </el-form>
</template>

<script setup>
import { ref, reactive } from 'vue'

const formRef = ref()

const form = reactive({
  name: '',
  email: '',
  age: ''
})

const rules = reactive({
  name: [
    { required: true, message: 'Please input name', trigger: 'blur' },
    { min: 3, max: 10, message: 'Length 3-10 characters', trigger: 'blur' }
  ],
  email: [
    { required: true, message: 'Please input email', trigger: 'blur' },
    { type: 'email', message: 'Please input valid email', trigger: 'blur' }
  ],
  age: [
    { required: true, message: 'Please input age', trigger: 'blur' },
    { type: 'number', message: 'Age must be a number', trigger: 'blur' }
  ]
})

const submitForm = async () => {
  try {
    await formRef.value.validate()
    console.log('Form submitted:', form)
  } catch (error) {
    console.log('Validation failed')
  }
}

const resetForm = () => {
  formRef.value.resetFields()
}
</script>
```

### 自定义验
```vue
<template>
  <el-form :model="form" :rules="rules" ref="formRef">
    <el-form-item label="Password" prop="pass">
      <el-input v-model="form.pass" type="password" />
    </el-form-item>
    <el-form-item label="Confirm" prop="checkPass">
      <el-input v-model="form.checkPass" type="password" />
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="submitForm">Submit</el-button>
    </el-form-item>
  </el-form>
</template>

<script setup>
import { ref, reactive } from 'vue'

const formRef = ref()

const validatePass = (rule, value, callback) => {
  if (value === '') {
    callback(new Error('Please input password'))
  } else {
    if (form.checkPass !== '') {
      formRef.value?.validateField('checkPass')
    }
    callback()
  }
}

const validatePass2 = (rule, value, callback) => {
  if (value === '') {
    callback(new Error('Please input password again'))
  } else if (value !== form.pass) {
    callback(new Error("Two inputs don't match!"))
  } else {
    callback()
  }
}

const form = reactive({
  pass: '',
  checkPass: ''
})

const rules = reactive({
  pass: [{ validator: validatePass, trigger: 'blur' }],
  checkPass: [{ validator: validatePass2, trigger: 'blur' }]
})

const submitForm = () => {
  formRef.value.validate((valid) => {
    if (valid) {
      console.log('submit!')
    } else {
      console.log('error submit!')
    }
  })
}
</script>
```

### 行内表单

```vue
<template>
  <el-form :inline="true" :model="form">
    <el-form-item label="Approved by">
      <el-input v-model="form.user" placeholder="Approved by" />
    </el-form-item>
    <el-form-item label="Activity zone">
      <el-select v-model="form.region" placeholder="Activity zone">
        <el-option label="Zone one" value="shanghai" />
        <el-option label="Zone two" value="beijing" />
      </el-select>
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="onSubmit">Query</el-button>
    </el-form-item>
  </el-form>
</template>
```

### 动态表单项

```vue
<template>
  <el-form :model="form" ref="formRef">
    <el-form-item
      v-for="(item, index) in form.items"
      :key="index"
      :label="'Item ' + (index + 1)"
      :prop="'items.' + index + '.value'"
      :rules="{
        required: true,
        message: 'Item cannot be null',
        trigger: 'blur'
      }"
    >
      <el-input v-model="item.value" />
      <el-button @click="removeItem(index)">Delete</el-button>
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="addItem">Add Item</el-button>
      <el-button type="primary" @click="submitForm">Submit</el-button>
    </el-form-item>
  </el-form>
</template>

<script setup>
import { ref, reactive } from 'vue'

const formRef = ref()

const form = reactive({
  items: [{ value: '' }]
})

const addItem = () => {
  form.items.push({ value: '' })
}

const removeItem = (index) => {
  if (form.items.length > 1) {
    form.items.splice(index, 1)
  }
}

const submitForm = async () => {
  try {
    await formRef.value.validate()
    console.log('Form submitted:', form)
  } catch (error) {
    console.log('Validation failed')
  }
}
</script>
```

### 标签对齐

```vue
<template>
  <el-radio-group v-model="labelPosition" class="mb-4">
    <el-radio-button value="left">Left</el-radio-button>
    <el-radio-button value="right">Right</el-radio-button>
    <el-radio-button value="top">Top</el-radio-button>
  </el-radio-group>

  <el-form :model="form" :label-position="labelPosition" label-width="auto">
    <el-form-item label="Name">
      <el-input v-model="form.name" />
    </el-form-item>
    <el-form-item label="Activity zone">
      <el-input v-model="form.region" />
    </el-form-item>
  </el-form>
</template>

<script setup>
import { ref, reactive } from 'vue'

const labelPosition = ref('right')
const form = reactive({
  name: '',
  region: ''
})
</script>
```

## 常见问题

### 1. 回车提交表单

防止回车键提交表

```vue
<el-form @submit.prevent>
  <!-- form items -->
</el-form>
```

### 2. 嵌套表单
嵌套表单项时,在嵌套项上设`label-width`:

```vue
<el-form-item label="Outer">
  <el-form-item label="Inner" label-width="100px">
    <el-input />
  </el-form-item>
</el-form-item>
```

### 3. 数字验证

使用 `.number` 修饰符进行数字验

```vue
<el-input v-model.number="form.age" />
```

### 4. 验证未触
确保 `prop` model 键完全匹

```vue
<el-form :model="form">
  <el-form-item prop="name">  <!-- 必须匹配 form.name -->
    <el-input v-model="form.name" />
  </el-form-item>
</el-form>
```

## 组件交互

### Dialog 配合

```vue
<template>
  <el-button @click="dialogVisible = true">Open Form</el-button>
  
  <el-dialog v-model="dialogVisible" title="Form Dialog">
    <el-form :model="form" :rules="rules" ref="formRef">
      <!-- form items -->
    </el-form>
    <template #footer>
      <el-button @click="dialogVisible = false">Cancel</el-button>
      <el-button type="primary" @click="handleSubmit">Submit</el-button>
    </template>
  </el-dialog>
</template>
```

### API 提交配合

```vue
<script setup>
const handleSubmit = async () => {
  try {
    await formRef.value.validate()
    loading.value = true
    await api.submitForm(form)
    ElMessage.success('Submitted successfully')
  } catch (error) {
    if (error !== 'validation') {
      ElMessage.error('Submission failed')
    }
  } finally {
    loading.value = false
  }
}
</script>
```

## 最佳实践
1. **使用 reactive 处理表单数据**: 使用 `reactive()` 作为表单模型
2. **单独定义规则**: 将验证规则保存在单独响应式对象3. **使用 prop 属性*: 始终在表单项上设`prop` 以进行验4. **处理异步验证**: 使用 async/await 进行验证
5. **提交后重*: 成功提交后重置表6. **提交时禁*: API 调用期间禁用提交按钮
