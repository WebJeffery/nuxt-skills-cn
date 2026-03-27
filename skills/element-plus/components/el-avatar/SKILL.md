---
name: "el-avatar"
description: "用于使用图片、图标字符表示人员对象头像组件。当用户需要显示用户头像、个人资料图片实现体表示时调用
---

# 头像组件

头像组件显示用户头像实现体表支持图片、图标字符
## 何时使用

- 用户个人资料图片
- 团队成员显示
- 实现体表示
- 团队头像
## 基本用法

```vue
<template>
  <el-avatar :size="50" :src="avatarUrl" />
  <el-avatar shape="square" :size="50" :src="avatarUrl" />
</template>

<script setup>
const avatarUrl = 'https://example.com/avatar.jpg'
</script>
```

## 类型

```vue
<template>
  <el-avatar :icon="UserFilled" />
  <el-avatar :src="avatarUrl" />
  <el-avatar>user</el-avatar>
</template>

<script setup>
import { UserFilled } from '@element-plus/icons-vue'
</script>
```

## 失败回退

```vue
<template>
  <el-avatar :src="invalidUrl" @error="errorHandler">
    <img src="fallback.jpg" />
  </el-avatar>
</template>

<script setup>
const errorHandler = () => true
</script>
```

## 适应容器

```vue
<template>
  <el-avatar shape="square" :size="100" fit="fill" :src="avatarUrl" />
  <el-avatar shape="square" :size="100" fit="contain" :src="avatarUrl" />
  <el-avatar shape="square" :size="100" fit="cover" :src="avatarUrl" />
</template>
```

## 头像
```vue
<template>
  <el-avatar-group>
    <el-avatar :src="url1" />
    <el-avatar :src="url2" />
    <el-avatar :src="url3" />
  </el-avatar-group>
</template>
```

## API 参考
### Avatar 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| icon | 图标表示 | `string \| Component` | |
| size | 头像尺寸 | `number \| 'large' \| 'default' \| 'small'` | |
| shape | 头像形状 | `'circle' \| 'square'` | |
| src | 图片| `string` | |
| src-set | 原生 srcset 属性| `string` | |
| alt | 原生 alt 属性| `string` | |
| fit | 图片如何适应容器 | `'fill' \| 'contain' \| 'cover' \| 'none' \| 'scale-down'` | `'cover'` |

### Avatar 事件

| Name | Description | Type |
|------|-------------|------|
| error | 图片加载错误时触| `(e: Event) => void` |

### Avatar 插槽

| Name | Description |
|------|-------------|
| default | 自定义头像内容|

### AvatarGroup 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| size | 控制组中头像尺寸| `number \| 'large' \| 'default' \| 'small'` | |
| shape | 控制组中头像形| `'circle' \| 'square'` | |
| collapse-avatars | 是否折叠头像 | `boolean` | `false` |
| collapse-avatars-tooltip | 悬停时显示折叠头像 | `boolean` | `false` |
| max-collapse-avatars | 折叠前显示最大头像数 | `number` | `1` |
| effect | 工具提示主题 | `'dark' \| 'light'` | `'light'` |
| placement | 工具提示位置 | Placement enum | `'top'` |

## 最佳实践
1. 使用 `fit="cover"` 保持一致头像外观
2. 为图片加载错误提供回退内容
3. 使用 AvatarGroup 显示团队,并使用折叠功