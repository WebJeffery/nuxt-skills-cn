---
name: "element-plus-design-typography"
description: "Element Plus 的字体设计规范。当用户需要了解字体约定、font-family 设置和文本样式指南时调用。"
---

# 字体设计规范

Element Plus 创建了字体约定，以确保在不同平台上最佳展示效果。

## 使用场景

- 了解字体约定
- 应用一致文本样式
- 设置 font-family 以实现跨平台兼容性
- 管理字体大小和行高

---

## 字体族

Element Plus 使用精心选择字体堆栈以实现最佳跨平台显示：

```css
font-family:
  Inter, 'Helvetica Neue', Helvetica, 'PingFang SC', 'Hiragino Sans GB',
  'Microsoft YaHei', '微软雅黑', Arial, sans-serif;
```

### 字体堆栈说明

| 字体 | 平台 | 用途 |
|------|----------|---------|
| Inter | 所有 | 主要现代字体 |
| Helvetica Neue | macOS | 系统回退 |
| Helvetica | macOS/iOS | 系统回退 |
| PingFang SC | macOS（中文） | 中文字符 |
| Hiragino Sans GB | macOS（中文） | 中文字符 |
| Microsoft YaHei | Windows（中文） | 中文字符 |
| Arial | 所有 | 通用回退 |
| sans-serif | 所有 | 通用回退 |

---

## 字体大小

Element Plus 提供标准化字体大小变量：

| 变量 | 值 | 用途 |
|----------|-------|-------|
| `--el-font-size-extra-large` | `20px` | 超大标题 |
| `--el-font-size-large` | `18px` | 大标题 |
| `--el-font-size-medium` | `16px` | 中等文本、副标题 |
| `--el-font-size-base` | `14px` | 基础正文 |
| `--el-font-size-small` | `13px` | 小文本、说明文字 |
| `--el-font-size-extra-small` | `12px` | 超小文本、提示 |

### 使用示例

```css
/* 标题 */
h1 {
  font-size: var(--el-font-size-extra-large);
}

/* 副标题 */
h2 {
  font-size: var(--el-font-size-large);
}

/* 正文 */
p {
  font-size: var(--el-font-size-base);
}

/* 说明文字 */
.caption {
  font-size: var(--el-font-size-small);
}

/* 提示文本 */
.hint {
  font-size: var(--el-font-size-extra-small);
}
```

---

## 字体粗细

| 变量 | 值 | 用途 |
|----------|-------|-------|
| `--el-font-weight-primary` | `500` | 主要粗细，用于强调 |
| `--el-font-weight-secondary` | `100` | 次要粗细，用于浅色文本 |

```css
/* 强调文本 */
.emphasis {
  font-weight: var(--el-font-weight-primary);
}

/* 浅色文本 */
.light {
  font-weight: var(--el-font-weight-secondary);
}
```

---

## 行高

Element Plus 提供行高变量以保持一致垂直节奏：

| 变量 | 值 | 用途 |
|----------|-------|-------|
| `--el-font-line-height-primary` | `24px` | 主要行高 |
| `--el-font-line-height-secondary` | `16px` | 次要行高 |

### 行高指南

```css
/* 正文 */
p {
  line-height: var(--el-font-line-height-primary);
}

/* 紧凑文本 */
.compact {
  line-height: var(--el-font-line-height-secondary);
}
```

---

## CSS 变量汇总

```css
:root {
  /* 字体族 */
  --el-font-family: Inter, 'Helvetica Neue', Helvetica, 'PingFang SC',
    'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', Arial, sans-serif;

  /* 字体大小 */
  --el-font-size-extra-large: 20px;
  --el-font-size-large: 18px;
  --el-font-size-medium: 16px;
  --el-font-size-base: 14px;
  --el-font-size-small: 13px;
  --el-font-size-extra-small: 12px;

  /* 字体粗细 */
  --el-font-weight-primary: 500;
  --el-font-weight-secondary: 100;

  /* 行高 */
  --el-font-line-height-primary: 24px;
  --el-font-line-height-secondary: 16px;
}
```

---

## 文本组件

Element Plus 提供了用于样式化文本显示 Text 组件：

```vue
<template>
  <!-- 文本类型 -->
  <el-text>默认文本</el-text>
  <el-text type="primary">主要文本</el-text>
  <el-text type="success">成功文本</el-text>
  <el-text type="warning">警告文本</el-text>
  <el-text type="danger">危险文本</el-text>
  <el-text type="info">信息文本</el-text>

  <!-- 文本大小 -->
  <el-text size="large">大号文本</el-text>
  <el-text size="default">默认文本</el-text>
  <el-text size="small">小号文本</el-text>

  <!-- 截断文本 -->
  <el-text truncated>将被截断长文本...</el-text>

  <!-- 行限制文本 -->
  <el-text :line-clamp="2">限制为 2 行长文本...</el-text>
</template>
```

---

## 最佳实践

1. **使用 CSS 变量**：始终使用预定义字体变量以保持一致性
2. **跨平台**：字体堆栈确保跨平台一致显示
3. **层次结构**：使用字体大小建立视觉层次
4. **行高**：为正文保持可读的行高
5. **暗黑模式**：字体在暗黑模式下自动调整
6. **中文支持**：字体堆栈包含针对中文优化的字体

---

## 相关资源

| 资源 | 描述 |
|----------|-------------|
| [Text 组件](./components/el-text/SKILL.md) | Text 组件文档 |
| [颜色设计](./element-plus-design-color/SKILL.md) | 文本颜色变量 |
| [边框设计](./element-plus-design-border/SKILL.md) | 相关设计规范 |
