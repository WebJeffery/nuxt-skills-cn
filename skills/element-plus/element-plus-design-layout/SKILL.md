---
name: "element-plus-design-layout"
description: "使用 24 列网格系统的 Element Plus 布局网格系统。当用户需要创建响应式布局、了解网格系统或使用 Row 和 Col 组件时调用。"
---

# 布局网格系统

Element Plus 提供了 24 列网格系统，用于快速轻松地创建响应式布局。

## 使用场景

- 创建响应式网格布局
- 构建页面结构
- 实现基于列设计
- 使用断点响应式设计

---

## 基本概念

- **24 列系统**：基本单位是 1，最大为 24，最小为 0
- **Flex 布局**：默认使用 flex 布局
- **响应式**：预设了五个断点：xs、sm、md、lg、xl

---

## 基本布局

使用带有 `span` 属性 Row 和 Col 组件创建基本网格布局：

```vue
<template>
  <el-row>
    <el-col :span="24"><div class="grid-content">24</div></el-col>
  </el-row>
  <el-row>
    <el-col :span="12"><div class="grid-content">12</div></el-col>
    <el-col :span="12"><div class="grid-content">12</div></el-col>
  </el-row>
  <el-row>
    <el-col :span="8"><div class="grid-content">8</div></el-col>
    <el-col :span="8"><div class="grid-content">8</div></el-col>
    <el-col :span="8"><div class="grid-content">8</div></el-col>
  </el-row>
</template>
```

---

## 列间距

Row 提供 `gutter` 属性来指定列之间间距：

```vue
<template>
  <el-row :gutter="20">
    <el-col :span="6"><div class="grid-content">6</div></el-col>
    <el-col :span="6"><div class="grid-content">6</div></el-col>
    <el-col :span="6"><div class="grid-content">6</div></el-col>
    <el-col :span="6"><div class="grid-content">6</div></el-col>
  </el-row>
</template>
```

---

## 列偏移

使用 `offset` 属性指定列偏移：

```vue
<template>
  <el-row :gutter="20">
    <el-col :span="6"><div class="grid-content">6</div></el-col>
    <el-col :span="6" :offset="6"><div class="grid-content">6 偏移 6</div></el-col>
  </el-row>
</template>
```

---

## 对齐方式

使用 flex 布局和 `justify` 属性进行灵活对齐：

```vue
<template>
  <el-row justify="center">
    <el-col :span="6"><div class="grid-content">居中</div></el-col>
  </el-row>
  <el-row justify="end">
    <el-col :span="6"><div class="grid-content">结束</div></el-col>
  </el-row>
  <el-row justify="space-between">
    <el-col :span="6"><div class="grid-content">两端对齐</div></el-col>
    <el-col :span="6"><div class="grid-content">两端对齐</div></el-col>
  </el-row>
</template>
```

### Justify 选项

| 值 | 描述 |
|-------|-------------|
| `start` | 将项目对齐到开头 |
| `end` | 将项目对齐到末尾 |
| `center` | 居中项目 |
| `space-around` | 项目周围有相等空间 |
| `space-between` | 项目之间有相等空间 |
| `space-evenly` | 项目之间和周围有相等空间 |

---

## 响应式布局

根据 Bootstrap 响应式设计预设了五个断点：

| 断点 | 屏幕宽度 |
|------------|--------------|
| `xs` | `< 768px` |
| `sm` | `≥ 768px` |
| `md` | `≥ 992px` |
| `lg` | `≥ 1200px` |
| `xl` | `≥ 1920px` |

```vue
<template>
  <el-row :gutter="10">
    <el-col :xs="24" :sm="12" :md="8" :lg="6" :xl="4">
      <div class="grid-content">响应式</div>
    </el-col>
    <el-col :xs="24" :sm="12" :md="8" :lg="6" :xl="4">
      <div class="grid-content">响应式</div>
    </el-col>
  </el-row>
</template>
```

### 响应式对象语法

```vue
<template>
  <el-col :xs="{ span: 24, offset: 0 }" :sm="{ span: 12, offset: 2 }">
    <div class="grid-content">带偏移响应式</div>
  </el-col>
</template>
```

---

## 隐藏元素实现用类

导入 CSS 文件以使用隐藏实现用类：

```js
import 'element-plus/theme-chalk/display.css'
```

### 可用类

| 类 | 描述 |
|-------|-------------|
| `hidden-xs-only` | 仅在超小视口上隐藏 |
| `hidden-sm-only` | 仅在小视口上隐藏 |
| `hidden-sm-and-down` | 在小视口及以下隐藏 |
| `hidden-sm-and-up` | 在小视口及以上隐藏 |
| `hidden-md-only` | 仅在中等视口上隐藏 |
| `hidden-md-and-down` | 在中等视口及以下隐藏 |
| `hidden-md-and-up` | 在中等视口及以上隐藏 |
| `hidden-lg-only` | 仅在大视口上隐藏 |
| `hidden-lg-and-down` | 在大视口及以下隐藏 |
| `hidden-lg-and-up` | 在大视口及以上隐藏 |
| `hidden-xl-only` | 仅在超大视口上隐藏 |

---

## API 参考

### Row 属性

| 名称 | 描述 | 类型 | 默认值 |
|------|-------------|------|---------|
| gutter | 网格间距 | `number` | `0` |
| justify | 水平对齐方式 | `'start' \| 'end' \| 'center' \| 'space-around' \| 'space-between' \| 'space-evenly'` | `'start'` |
| align | 垂直对齐方式 | `'top' \| 'middle' \| 'bottom'` | - |
| tag | 自定义元素标签 | `string` | `'div'` |

### Col 属性

| 名称 | 描述 | 类型 | 默认值 |
|------|-------------|------|---------|
| span | 网格跨越列数 | `number` | `24` |
| offset | 左侧间距列数 | `number` | `0` |
| push | 向右移动列数 | `number` | `0` |
| pull | 向左移动列数 | `number` | `0` |
| xs | `<768px` 响应式列 | `number \| object` | - |
| sm | `≥768px` 响应式列 | `number \| object` | - |
| md | `≥992px` 响应式列 | `number \| object` | - |
| lg | `≥1200px` 响应式列 | `number \| object` | - |
| xl | `≥1920px` 响应式列 | `number \| object` | - |
| tag | 自定义元素标签 | `string` | `'div'` |

---

## 最佳实践

1. **使用 gutter**：在列之间添加间距以提高可读性
2. **响应式设计**：使用断点属性进行移动优先设计
3. **避免内联样式**：父容器应避免内联样式
4. **与 Container 结合**：与 Container 一起使用以实现完整页面布局
