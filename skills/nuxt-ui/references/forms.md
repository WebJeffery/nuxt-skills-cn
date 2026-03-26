# 表单

## 表单组件

| 组件        | 目的             | 关键属性                                     | 版本 |
| ---------------- | ------------------- | --------------------------------------------- | ------- |
| `UInput`         | 文本输入          | `type`, `placeholder`, `icon`, `loading`      |         |
| `UTextarea`      | 多行文本     | `rows`, `autoresize`, `maxrows`               |         |
| `USelect`        | 原生选择       | `options`, `placeholder`                      |         |
| `USelectMenu`    | 自定义选择       | `options`, `searchable`, `multiple`           |         |
| `UInputMenu`     | 自动完成        | `options`, `searchable`                       |         |
| `UCheckbox`      | 单个复选框     | `label`, `description`                        |         |
| `UCheckboxGroup` | 多个复选框 | `items`, `orientation`                        |         |
| `URadioGroup`    | 单选选项       | `items`, `orientation`                        |         |
| `USwitch`        | 切换开关       | `label`, `description`, `on-icon`, `off-icon` |         |
| `USlider`        | 范围滑块        | `min`, `max`, `step`                          |         |
| `UPinInput`      | 代码/OTP 输入      | `length`, `type`, `mask`                      |         |
| `UInputNumber`   | 数字输入        | `min`, `max`, `step`, `format-options`        |         |
| `UInputDate`     | 日期选择器         | `mode`, `range`, `locale`                     | v4.2+   |
| `UInputTime`     | 时间选择器         | `hour-cycle`, `minute-step`                   | v4.2+   |
| `UInputTags`     | 标签输入           | `max`, `placeholder`                          |         |
| `UColorPicker`   | 颜色选择     | `format`, `swatches`                          |         |
| `UFileUpload`    | 文件上传         | `accept`, `multiple`, `max-files`             |         |

## 基本输入示例

```vue
<script setup>
const email = ref('')
const bio = ref('')
const country = ref('')
</script>

<template>
  <!-- 文本输入 -->
  <UInput v-model="email" type="email" placeholder="邮箱" icon="i-heroicons-envelope" />

  <!-- 带验证状态 -->
  <UInput v-model="email" :color="emailError ? 'error' : undefined" />

  <!-- 文本区域 -->
  <UTextarea v-model="bio" placeholder="简介" :rows="3" autoresize />

  <!-- 选择 -->
  <USelect v-model="country" :options="['美国', '加拿大', '墨西哥']" placeholder="国家" />
</template>
```

## SelectMenu（自定义下拉菜单）

```vue
<script setup>
const selected = ref()
const options = [
  { label: '张三', value: 'john', avatar: { src: '/john.png' } },
  { label: '李四', value: 'jane', avatar: { src: '/jane.png' } }
]
</script>

<template>
  <USelectMenu
    v-model="selected"
    :options="options"
    searchable
    clear
    placeholder="选择用户"
  >
    <template #option="{ option }">
      <UAvatar v-bind="option.avatar" size="xs" />
      <span>{{ option.label }}</span>
    </template>
  </USelectMenu>
</template>
```

### SelectMenu/InputMenu 属性（v4.4+）

```vue
<USelectMenu
  v-model="selected"
  :options="options"
  clear                <!-- 添加清除按钮（v4.4+） -->
  :viewport-ref="ref"  <!-- 用于无限滚动（v4.4+） -->
/>
```

## 复选框和单选框

```vue
<script setup>
const agreed = ref(false)
const plan = ref('free')
const features = ref([])
</script>

<template>
  <!-- 单个复选框 -->
  <UCheckbox v-model="agreed" label="我同意条款" description="必填" />

  <!-- 单选框组 -->
  <URadioGroup
    v-model="plan"
    :items="[
      { label: '免费', value: 'free', description: '$0/月' },
      { label: '专业版', value: 'pro', description: '$10/月' }
    ]"
  />

  <!-- 复选框组 -->
  <UCheckboxGroup
    v-model="features"
    :items="[
      { label: '深色模式', value: 'dark' },
      { label: '通知', value: 'notifications' }
    ]"
  />
</template>
```

## 表单验证

使用标准模式（Zod、Valibot、Yup、Joi 等）

### 使用 Zod

```vue
<script setup lang="ts">
import { z } from 'zod'

const schema = z.object({
  email: z.string().email('无效的邮箱'),
  password: z.string().min(8, '最少 8 个字符')
})

type Schema = z.output<typeof schema>

const state = reactive<Partial<Schema>>({
  email: '',
  password: ''
})

const form = ref()

async function onSubmit() {
  await form.value.validate()
  // 提交逻辑
}
</script>

<template>
  <UForm ref="form" :schema="schema" :state="state" @submit="onSubmit">
    <UFormField name="email" label="邮箱">
      <UInput v-model="state.email" type="email" />
    </UFormField>

    <UFormField name="password" label="密码">
      <UInput v-model="state.password" type="password" />
    </UFormField>

    <UButton type="submit">提交</UButton>
  </UForm>
</template>
```

### 使用 Valibot

```vue
<script setup lang="ts">
import * as v from 'valibot'

const schema = v.object({
  email: v.pipe(v.string(), v.email('无效的邮箱')),
  password: v.pipe(v.string(), v.minLength(8, '最少 8 个字符'))
})

type Schema = v.InferOutput<typeof schema>

const state = reactive<Partial<Schema>>({
  email: '',
  password: ''
})
</script>

<template>
  <UForm :schema="schema" :state="state" @submit="onSubmit">
    <!-- 同上 -->
  </UForm>
</template>
```

## UFormField 属性

```vue
<UFormField
  name="email"              <!-- 字段名称（匹配 state 键） -->
  label="邮箱"             <!-- 标签文本 -->
  description="您的邮箱"  <!-- 帮助文本 -->
  hint="可选"           <!-- 右对齐提示 -->
  required                  <!-- 显示星号 -->
  :help="error?.message"    <!-- 错误消息 -->
>
  <UInput v-model="state.email" />
</UFormField>
```

## UFieldGroup（分组字段）

```vue
<UFieldGroup label="姓名">
  <UInput v-model="firstName" placeholder="名" />
  <UInput v-model="lastName" placeholder="姓" />
</UFieldGroup>
```

## 输入状态

```vue
<!-- 禁用 -->
<UInput disabled placeholder="禁用" />

<!-- 加载中 -->
<UInput :loading="true" placeholder="加载中..." />

<!-- 带图标 -->
<UInput icon="i-heroicons-magnifying-glass" placeholder="搜索" />

<!-- 尾部图标 -->
<UInput trailing-icon="i-heroicons-x-mark" placeholder="可清除" />
```

## 文件上传

```vue
<script setup>
const { files, open, reset } = useFileUpload()
</script>

<template>
  <UFileUpload v-model="files" accept="image/*" multiple :max-files="5">
    <UButton @click="open">上传</UButton>
  </UFileUpload>
</template>
```

**注意（v4.4）：** FileUpload 在清除文件时现在发出 `null`（之前发出空数组）。

## 日期和时间选择器（v4.2+）

### 日期选择器

```vue
<script setup>
const date = ref(new Date())
const range = ref({ start: new Date(), end: new Date() })
</script>

<template>
  <!-- 单个日期 -->
  <UInputDate v-model="date" />

  <!-- 日期范围 -->
  <UInputDate v-model="range" mode="range" />

  <!-- 带语言环境 -->
  <UInputDate v-model="date" locale="es" />
</template>
```

### 时间选择器

```vue
<script setup>
import { Time } from '@internationalized/date'

const time = ref(new Time(12, 0))
</script>

<template>
  <!-- 基本时间选择器 -->
  <UInputTime v-model="time" />

  <!-- 24 小时格式 -->
  <UInputTime v-model="time" hour-cycle="24" />

  <!-- 自定义步长（分钟） -->
  <UInputTime v-model="time" :minute-step="15" />
</template>
```

## 常见模式

### 登录表单

```vue
<UForm :schema="loginSchema" :state="state" @submit="login">
  <UFormField name="email" label="邮箱" required>
    <UInput v-model="state.email" type="email" icon="i-heroicons-envelope" />
  </UFormField>
  <UFormField name="password" label="密码" required>
    <UInput v-model="state.password" type="password" icon="i-heroicons-lock-closed" />
  </UFormField>
  <UButton type="submit" block :loading="loading">登录</UButton>
</UForm>
```

### 设置表单

```vue
<UForm :state="settings" @submit="save">
  <UFormField name="notifications" label="通知">
    <USwitch v-model="settings.notifications" label="启用通知" />
  </UFormField>
  <UFormField name="theme" label="主题">
    <URadioGroup v-model="settings.theme" :items="themeOptions" />
  </UFormField>
  <UButton type="submit">保存</UButton>
</UForm>
```

## 最佳实践

| 做                                | 不要                      |
| --------------------------------- | -------------------------- |
| 使用 UForm + UFormField            | 构建自定义表单包装器 |
| 使用标准模式（Zod/Valibot） | 编写自定义验证    |
| 在表单输入上使用 v-model        | 手动同步表单状态   |
| 使用 `required` 属性显示星号 | 手动添加星号     |
| 使用 `description` 作为帮助文本   | 使用单独的帮助元素 |
