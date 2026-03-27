---
name: "el-config-provider"
description: "用于全局配置配置提供者组件。当用户需要配置全局设置、国际化、主题组件默认值时调用---

# ConfigProvider 组件

ConfigProvider 提供可在整个应用程序中访问全局配置
## 何时使用

- 国际- 全局组件尺寸
- 主题配置
- 组件默认
## i18n 配置

```vue
<template>
  <el-config-provider :locale="locale">
    <App />
  </el-config-provider>
</template>

<script setup>
import { ref } from 'vue'
import zhCn from 'element-plus/dist/locale/zh-cn.mjs'

const locale = ref(zhCn)
</script>
```

## 全局尺寸

```vue
<template>
  <el-config-provider size="small">
    <App />
  </el-config-provider>
</template>
```

## 按钮配置

```vue
<template>
  <el-config-provider :button="{ autoInsertSpace: true, type: 'primary' }">
    <App />
  </el-config-provider>
</template>
```

## 消息配置

```vue
<template>
  <el-config-provider :message="{ max: 3 }">
    <App />
  </el-config-provider>
</template>
```

## 空值配合
```vue
<template>
  <el-config-provider
    :empty-values="[null, undefined]"
    :value-on-clear="null"
  >
    <App />
  </el-config-provider>
</template>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| locale | 语言环境对象 | `{ name: string, el: TranslatePair }` | English |
| size | 全局组件尺寸 | `'large' \| 'default' \| 'small'` | `'default'` |
| zIndex | 初始 zIndex | `number` | |
| namespace | 组件类前缀 | `string` | `'el'` |
| button | 按钮配置 | `{ autoInsertSpace?, type?, plain?, text?, round?, dashed? }` | |
| link | 链接配置 | `{ type?, underline? }` | |
| dialog | 对话框配合| `{ alignCenter?, draggable?, overflow?, transition? }` | |
| message | 消息配置 | `{ max?, grouping?, duration?, showClose?, offset?, plain?, placement? }` | |
| table | 表格配置 | `{ showOverflowTooltip?, tooltipEffect?, tooltipOptions?, tooltipFormatter? }` | |
| empty-values | 组件空| `array` | `['', null, undefined]` |
| value-on-clear | 清除时| `string \| number \| boolean \| Function` | `undefined` |

### 插槽

| Name | Description | Type |
|------|-------------|------|
| default | 默认内容 | `{ config }` |

## 最佳实践
1. 用于全局 i18n 配置
2. 设置 `size` 以获得一致组件尺寸
3. 使用 `empty-values` 用于表单处理
