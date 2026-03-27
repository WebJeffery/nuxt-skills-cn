---
name: "el-tooltip"
description: "用于在鼠标悬停时显示提示信息工具提示组件。当用户需要在悬停时显示提示、提示附加信息时调用---

# Element Plus Tooltip 组件

显示鼠标悬停时提示信息
## 何时调用

在以下情况下调用此技能
- 用户需要在悬停时显示工具提- 用户想要显示提示技能- 用户需要在不同方向定位工具提示
- 用户想要自定义工具提示内容- 用户询问带有 HTML 内容工具提
## 功能特性
- **9 种位*: 顶部、底部、左侧、右侧及对齐选项
- **两种主题**: 深色和浅- **HTML 内容**: 支持 HTML 字符- **VNode 内容**: Vue 组件渲染为内容- **虚拟触发**: 分离触发器和内容元素
- **受控模式**: 以编程方式控制可见- **自定义动*: 可自定义过渡效果

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| append-to | 工具提示内容附加到哪个元| `CSSSelector \| HTMLElement` | |
| effect | 工具提示主题 | `'dark' \| 'light'` | dark |
| content | 显示内容 | `string` | '' |
| raw-content | 内容是否HTML 字符| `boolean` | false |
| placement | Tooltip 位| `'top' \| 'top-start' \| 'top-end' \| 'bottom' \| 'bottom-start' \| 'bottom-end' \| 'left' \| 'left-start' \| 'left-end' \| 'right' \| 'right-start' \| 'right-end'` | bottom |
| fallback-placements | 可能位置列| `Placement[]` | |
| visible / v-model:visible | Tooltip 可见| `boolean` | |
| disabled | Tooltip 是否禁用 | `boolean` | |
| offset | Tooltip 偏移量 | `number` | 12 |
| transition | 动画名称 | `string` | |
| popper-options | popper.js 参数 | `object` | {} |
| arrow-offset | 工具提示箭头偏移量 | `number` | 5 |
| show-after | 出现延迟(ms) | `number` | 0 |
| show-arrow | 工具提示是否有箭| `boolean` | true |
| hide-after | 消失延迟(ms) | `number` | 200 |
| auto-close | 隐藏工具提示超时时ms) | `number` | 0 |
| popper-class | Tooltip popper 自定义类名 | `string` | |
| popper-style | Tooltip popper 自定义样式 | `string \| object` | |
| enterable | 鼠标是否可以进入工具提示 | `boolean` | true |
| teleported | 工具提示内容是否传| `boolean` | true |
| trigger | 工具提示如何触发 | `'hover' \| 'click' \| 'focus' \| 'contextmenu' \| Array` | hover |
| virtual-triggering | 是否启用虚拟触发 | `boolean` | |
| virtual-ref | 虚拟触发引用元| `HTMLElement` | |
| trigger-keys | 控制显示键盘代| `Array` | ['Enter', 'Space'] |
| persistent | 工具提示在不活动时是否持| `boolean` | |
| aria-label | aria-label 相同 | `string` | |
| focus-on-target | 悬停时是否聚焦触发元| `boolean` | false |

### 事件

| Name | Description | Type |
|------|-------------|------|
| before-show | 工具提示显示前触| `(event?) => void` |
| show | 工具提示显示时触| `(event?) => void` |
| before-hide | 工具提示隐藏前触| `(event?) => void` |
| hide | 工具提示隐藏时触| `(event?) => void` |

### 插槽

| Name | Description |
|------|-------------|
| default | 工具提示触发元素,仅接受单个根元素 |
| content | 自定义内容|

### 暴露

| Name | Description | Type |
|------|-------------|------|
| popperRef | el-popper 组件实现例 | `Ref<PopperInstance \| undefined>` |
| contentRef | el-tooltip-content 组件实现例 | `Ref<TooltipContentInstance \| undefined>` |
| isFocusInsideContent | 验证工具提示内容焦点 | `() => boolean \| undefined` |
| updatePopper | 更新 el-popper 组件 | `() => void` |
| onOpen | 打开工具提示 | `(event?) => void` |
| onClose | 关闭工具提示 | `(event?) => void` |
| hide | 隐藏工具提示 | `(event?) => void` |

## 使用示例

### 基本用法

```vue
<template>
  <el-tooltip content="Top center" placement="top">
    <el-button>Top</el-button>
  </el-tooltip>
  
  <el-tooltip content="Bottom center" placement="bottom">
    <el-button>Bottom</el-button>
  </el-tooltip>
</template>
```

### 主题

```vue
<template>
  <el-tooltip content="Dark theme" effect="dark">
    <el-button>Dark</el-button>
  </el-tooltip>
  
  <el-tooltip content="Light theme" effect="light">
    <el-button>Light</el-button>
  </el-tooltip>
</template>
```

### 丰富内容

```vue
<template>
  <el-tooltip placement="top">
    <template #content>
      <div>Multiple lines</div>
      <div>Second line</div>
      <div>Third line</div>
    </template>
    <el-button>Rich Content</el-button>
  </el-tooltip>
</template>
```

### HTML 内容

```vue
<template>
  <el-tooltip
    raw-content
    content="<strong>Bold</strong> and <i>italic</i>"
  >
    <el-button>HTML Content</el-button>
  </el-tooltip>
</template>
```

### 高级用法

```vue
<template>
  <el-tooltip
    :disabled="disabled"
    :hide-after="0"
    placement="bottom"
  >
    <template #content>
      <span>Custom content</span>
    </template>
    <el-button>Advanced</el-button>
  </el-tooltip>
</template>

<script setup>
import { ref } from 'vue'

const disabled = ref(false)
</script>
```

### 虚拟触发

```vue
<template>
  <el-button ref="triggerRef">Trigger</el-button>
  
  <el-tooltip
    virtual-triggering
    :virtual-ref="triggerRef"
    content="Virtual tooltip"
  />
</template>

<script setup>
import { ref } from 'vue'

const triggerRef = ref()
</script>
```

### 受控可见
```vue
<template>
  <el-tooltip
    v-model:visible="visible"
    content="Controlled tooltip"
  >
    <el-button @click="visible = !visible">
      Toggle Tooltip
    </el-button>
  </el-tooltip>
</template>

<script setup>
import { ref } from 'vue'

const visible = ref(false)
</script>
```

### 自定义动
```vue
<template>
  <el-tooltip
    content="Custom animation"
    transition="el-zoom-in-top"
  >
    <el-button>Custom Animation</el-button>
  </el-tooltip>
</template>
```

### 点击触发

```vue
<template>
  <el-tooltip
    content="Click to show"
    trigger="click"
  >
    <el-button>Click Trigger</el-button>
  </el-tooltip>
</template>
```

### 与表单元素配合
```vue
<template>
  <!-- 将禁用元素包装在容器中 -->
  <el-tooltip content="This field is disabled">
    <div>
      <el-input disabled placeholder="Disabled input" />
    </div>
  </el-tooltip>
</template>
```

## 常见问题

### 1. 工具提示在禁用元素上不显
将禁用元素包装在容器中:

```vue
<el-tooltip content="Tooltip">
  <div>
    <el-button disabled>Disabled Button</el-button>
  </div>
</el-tooltip>
```

### 2. 工具提示中 Router-link 不工
改用 `vm.$router.push`:

```vue
<el-tooltip>
  <template #content>
    <span @click="$router.push('/path')">Link</span>
  </template>
</el-tooltip>
```

### 3. 嵌套输入框中空格键不工作

`trigger-keys` 设置为空数组:

```vue
<el-tooltip :trigger-keys="[]">
  <el-input v-model="value" />
</el-tooltip>
```

## 最佳实践
1. **保持内容简*: 工具提示应该简2. **使用适当位*: 将工具提示定位在触发器附3. **避免 HTML 内容**: 出于安全考虑,优先使用 VNode
4. **禁用元素处理**: 将禁用元素包装在容器中
5. **受控模式**: 使用 v-model:visible 进行编程控制
