---
name: "el-backtop"
description: "用于返回顶部按钮返回顶部组件。当用户需要为长页面提供快速导航返回顶部时调用
---

# Backtop 组件

Backtop 组件提供一个按钮用于滚动回页面顶部
## 何时使用

- 长页面导- 快速滚动到顶部
- 改善用户体验
- 文档阅读
## 基本用法

```vue
<template>
  <el-backtop />
</template>
```

## 自定
```vue
<template>
  <el-backtop :bottom="100">
    <div class="backtop-custom">
      UP
    </div>
  </el-backtop>
</template>

<style>
.backtop-custom {
  height: 100%;
  width: 100%;
  background-color: var(--el-bg-color-overlay);
  box-shadow: var(--el-box-shadow-lighter);
  text-align: center;
  line-height: 40px;
  color: #1989fa;
}
</style>
```

## 目标容器

```vue
<template>
  <div class="scroll-container" style="height: 400px; overflow: auto;">
    <el-backtop target=".scroll-container" :visibility-height="100">
      UP
    </el-backtop>
  </div>
</template>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| target | 触发滚动目| `string` | |
| visibility-height | 显示按钮最小高| `number` | `200` |
| right | 右侧距离 | `number` | `40` |
| bottom | 底部距离 | `number` | `40` |

### 事件

| Name | Description | Type |
|------|-------------|------|
| click | 点击时触| `(evt: MouseEvent) => void` |

### 插槽

| Name | Description |
|------|-------------|
| default | 自定义默认内容|

## 最佳实践
1. 为布局设置适当`visibility-height`
2. 使用 `right` `bottom` 自定义位3. 使用 `target` 用于可滚动容