---
name: "el-steps"
description: "用于引导用户完成流程步骤组件。当用户需要显示步骤进度、向导界面流程工作流时调用---

# Steps 组件

Steps 组件根据流程引导用户完成任务
## 何时使用

- 向导界面
- 流程工作- 步骤进度指示- 多步骤表
## 基本用法

```vue
<template>
  <el-steps :active="active" finish-status="success">
    <el-step title="Step 1" />
    <el-step title="Step 2" />
    <el-step title="Step 3" />
  </el-steps>
  
  <el-button @click="next">Next step</el-button>
</template>

<script setup>
import { ref } from 'vue'

const active = ref(0)

const next = () => {
  if (active.value++ > 2) active.value = 0
}
</script>
```

## 带状态
```vue
<template>
  <el-steps :active="1">
    <el-step title="Step 1" description="Some description" />
    <el-step title="Step 2" description="Some description" status="process" />
    <el-step title="Step 3" description="Some description" status="wait" />
  </el-steps>
</template>
```

## 居中

```vue
<template>
  <el-steps :active="2" align-center>
    <el-step title="Step 1" />
    <el-step title="Step 2" />
    <el-step title="Step 3" />
  </el-steps>
</template>
```

## 带描
```vue
<template>
  <el-steps :active="1">
    <el-step title="Step 1" description="Description for step 1" />
    <el-step title="Step 2" description="Description for step 2" />
    <el-step title="Step 3" description="Description for step 3" />
  </el-steps>
</template>
```

## 带图
```vue
<template>
  <el-steps :active="1">
    <el-step title="Step 1" :icon="Edit" />
    <el-step title="Step 2" :icon="Upload" />
    <el-step title="Step 3" :icon="Picture" />
  </el-steps>
</template>

<script setup>
import { Edit, Upload, Picture } from '@element-plus/icons-vue'
</script>
```

## 垂直

```vue
<template>
  <el-steps :active="1" direction="vertical">
    <el-step title="Step 1" />
    <el-step title="Step 2" />
    <el-step title="Step 3" />
  </el-steps>
</template>
```

## 简
```vue
<template>
  <el-steps :active="1" simple>
    <el-step title="Step 1" />
    <el-step title="Step 2" />
    <el-step title="Step 3" />
  </el-steps>
</template>
```

## API 参考
### Steps 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| space | 每个步骤间| `number \| string` | `''` |
| direction | 显示方向 | `'vertical' \| 'horizontal'` | `'horizontal'` |
| active | 当前激活步| `number` | `0` |
| process-status | 当前步骤状态| `'wait' \| 'process' \| 'finish' \| 'error' \| 'success'` | `'process'` |
| finish-status | 已完成步骤状态| `'wait' \| 'process' \| 'finish' \| 'error' \| 'success'` | `'finish'` |
| align-center | 标题和描述居| `boolean` | |
| simple | 应用简洁主| `boolean` | |

### Step 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| title | 步骤标题 | `string` | `''` |
| description | 步骤描述 | `string` | `''` |
| icon | 自定义图| `string \| Component` | |
| status | 当前状态| `'' \| 'wait' \| 'process' \| 'finish' \| 'error' \| 'success'` | `''` |

### Step 插槽

| Name | Description |
|------|-------------|
| icon | 自定义图|
| title | 步骤标题 |
| description | 步骤描述 |

## 最佳实践
1. 使用 `active` 跟踪当前步骤
2. 使用 `direction="vertical"` 用于有限宽度
3. 使用 `simple` 用于最小化 UI
