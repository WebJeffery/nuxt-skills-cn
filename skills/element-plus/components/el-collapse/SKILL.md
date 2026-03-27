---
name: "el-collapse"
description: "用于存储可展开内容折叠面板组件。当用户需要创建手风琴面板、常见问题解答部分可折叠内容区域时调用---

# Collapse 组件

Collapse 组件将内容存储在可展开面板中,支持手风琴模式和自定义样式
## 何时使用

- 常见问题解答部分
- 手风琴面- 可折叠内容区- 可展开列表

## 基本用法

```vue
<template>
  <el-collapse v-model="activeNames">
    <el-collapse-item title="Consistency" name="1">
      <div>Content of panel 1</div>
    </el-collapse-item>
    <el-collapse-item title="Feedback" name="2">
      <div>Content of panel 2</div>
    </el-collapse-item>
    <el-collapse-item title="Efficiency" name="3">
      <div>Content of panel 3</div>
    </el-collapse-item>
  </el-collapse>
</template>

<script setup>
import { ref } from 'vue'

const activeNames = ref(['1'])
</script>
```

## 手风琴模
```vue
<template>
  <el-collapse v-model="activeName" accordion>
    <el-collapse-item title="Panel 1" name="1">
      <div>Content</div>
    </el-collapse-item>
    <el-collapse-item title="Panel 2" name="2">
      <div>Content</div>
    </el-collapse-item>
  </el-collapse>
</template>

<script setup>
import { ref } from 'vue'

const activeName = ref('1')
</script>
```

## 自定义标签
```vue
<template>
  <el-collapse v-model="activeNames">
    <el-collapse-item name="1">
      <template #title>
        <span>Custom Title</span>
        <el-icon><InfoFilled /></el-icon>
      </template>
      <div>Content</div>
    </el-collapse-item>
  </el-collapse>
</template>
```

## 自定义图
```vue
<template>
  <el-collapse v-model="activeNames">
    <el-collapse-item title="Panel" name="1" :icon="CaretRight">
      <div>Content</div>
    </el-collapse-item>
  </el-collapse>
</template>

<script setup>
import { CaretRight } from '@element-plus/icons-vue'
</script>
```

## 防止折叠

```vue
<template>
  <el-collapse v-model="activeNames" :before-collapse="beforeCollapse">
    <el-collapse-item title="Panel" name="1">
      <div>Content</div>
    </el-collapse-item>
  </el-collapse>
</template>

<script setup>
const beforeCollapse = () => {
  return confirm('Are you sure you want to collapse?')
}
</script>
```

## API 参考
### Collapse 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 激活面板 | `string \| array` | `[]` |
| accordion | 启用手风琴模| `boolean` | `false` |
| expand-icon-position | 展开图标位| `'left' \| 'right'` | `'right'` |
| before-collapse | 折叠前钩子 | `() => Promise<boolean> \| boolean` | |

### Collapse 事件

| Name | Description | Type |
|------|-------------|------|
| change | 激活面板改变时触发 | `(activeNames: array \| string) => void` |

### Collapse 暴露

| Name | Description | Type |
|------|-------------|------|
| activeNames | 当前激活面板名称 | `ComputedRef<(string \| number)[]>` |
| setActiveNames | 设置激活面板名称 | `(activeNames) => void` |

### CollapseItem 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| name | 唯一标识| `string \| number` | |
| title | 面板标题 | `string` | `''` |
| icon | 项目图标 | `string \| Component` | `ArrowRight` |
| disabled | 禁用项目 | `boolean` | `false` |

### CollapseItem 插槽

| Name | Description | Type |
|------|-------------|------|
| default | Collapse Item 内容| |
| title | 标题内容 | `{ isActive: boolean }` |
| icon | 图标内容 | `{ isActive: boolean }` |

## 最佳实践
1. 使用 `accordion` 模式用于单面板展开
2. 使用 `before-collapse` 用于确认对话3. 使用 `expand-icon-position` 自定义图标位