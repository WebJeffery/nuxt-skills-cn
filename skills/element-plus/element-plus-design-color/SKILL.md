---
name: "element-plus-design-color"
description: "Element Plus 的颜色设计规范。当用户需要了解调色板、主要颜色、次要颜色和 Element Plus 中使用的中性颜色时调用。"
---

# 颜色设计规范

Element Plus 使用一组特定调色板来指定颜色，为你构建产品提供一致外观和感觉。

## 使用场景

- 了解颜色系统
- 正确应用品牌颜色
- 使用语义颜色（success、warning、danger）
- 使用中性颜色进行文本和背景设置

---

## 主要颜色

Element Plus 主要颜色是一种明亮友好蓝色：

| 颜色 | 变量 | 值 | 用途 |
|-------|----------|-------|-------|
| Primary | `--el-color-primary` | `#409eff` | 主要品牌颜色 |
| Primary Light-3 | `--el-color-primary-light-3` | `#79bbff` | 悬停状态 |
| Primary Light-5 | `--el-color-primary-light-5` | `#a0cfff` | 较浅变体 |
| Primary Light-7 | `--el-color-primary-light-7` | `#c6e2ff` | 背景 |
| Primary Light-8 | `--el-color-primary-light-8` | `#d9ecff` | 浅背景 |
| Primary Light-9 | `--el-color-primary-light-9` | `#ecf5ff` | 超浅背景 |
| Primary Dark-2 | `--el-color-primary-dark-2` | `#337ecc` | 激活状态 |

---

## 次要颜色

次要颜色用于不同场景（例如，危险颜色表示危险操作）。

### Success

| 变量 | 值 | 用途 |
|----------|-------|-------|
| `--el-color-success` | `#67c23a` | 成功操作 |
| `--el-color-success-light-3` | `#95d475` | 悬停状态 |
| `--el-color-success-light-5` | `#b3e19d` | 较浅变体 |
| `--el-color-success-light-7` | `#d1edc4` | 背景 |
| `--el-color-success-light-8` | `#e1f3d8` | 浅背景 |
| `--el-color-success-light-9` | `#f0f9eb` | 超浅背景 |
| `--el-color-success-dark-2` | `#529b2e` | 激活状态 |

### Warning

| 变量 | 值 | 用途 |
|----------|-------|-------|
| `--el-color-warning` | `#e6a23c` | 警告操作 |
| `--el-color-warning-light-3` | `#eebe77` | 悬停状态 |
| `--el-color-warning-light-5` | `#f3d19e` | 较浅变体 |
| `--el-color-warning-light-7` | `#f8e3c5` | 背景 |
| `--el-color-warning-light-8` | `#faecd8` | 浅背景 |
| `--el-color-warning-light-9` | `#fdf6ec` | 超浅背景 |
| `--el-color-warning-dark-2` | `#b88230` | 激活状态 |

### Danger

| 变量 | 值 | 用途 |
|----------|-------|-------|
| `--el-color-danger` | `#f56c6c` | 危险/错误操作 |
| `--el-color-danger-light-3` | `#f89898` | 悬停状态 |
| `--el-color-danger-light-5` | `#fab6b6` | 较浅变体 |
| `--el-color-danger-light-7` | `#fcd3d3` | 背景 |
| `--el-color-danger-light-8` | `#fde2e2` | 浅背景 |
| `--el-color-danger-light-9` | `#fef0f0` | 超浅背景 |
| `--el-color-danger-dark-2` | `#c45656` | 激活状态 |

### Info

| 变量 | 值 | 用途 |
|----------|-------|-------|
| `--el-color-info` | `#909399` | 信息操作 |
| `--el-color-info-light-3` | `#b1b3b8` | 悬停状态 |
| `--el-color-info-light-5` | `#c8c9cc` | 较浅变体 |
| `--el-color-info-light-7` | `#dedfe0` | 背景 |
| `--el-color-info-light-8` | `#e9e9eb` | 浅背景 |
| `--el-color-info-light-9` | `#f4f4f5` | 超浅背景 |
| `--el-color-info-dark-2` | `#73767a` | 激活状态 |

---

## 中性颜色

中性颜色用于文本、背景和边框颜色。使用不同中性颜色来表示层次结构。

### 文本颜色

| 变量 | 值 | 用途 |
|----------|-------|-------|
| `--el-text-color-primary` | `#303133` | 主要文本 |
| `--el-text-color-regular` | `#606266` | 常规文本 |
| `--el-text-color-secondary` | `#909399` | 次要文本 |
| `--el-text-color-placeholder` | `#a8abb2` | 占位符文本 |
| `--el-text-color-disabled` | `#c0c4cc` | 禁用文本 |

### 背景颜色

| 变量 | 值 | 用途 |
|----------|-------|-------|
| `--el-bg-color` | `#ffffff` | 默认背景 |
| `--el-bg-color-page` | `#f2f3f5` | 页面背景 |
| `--el-bg-color-overlay` | `#ffffff` | 遮罩背景 |
| `--el-fill-color` | `#f0f2f5` | 填充颜色 |
| `--el-fill-color-light` | `#f5f7fa` | 浅填充 |
| `--el-fill-color-lighter` | `#fafafa` | 更浅填充 |
| `--el-fill-color-extra-light` | `#fafcff` | 超浅填充 |
| `--el-fill-color-dark` | `#ebedf0` | 深填充 |
| `--el-fill-color-darker` | `#e6e8eb` | 更深填充 |
| `--el-fill-color-blank` | `#ffffff` | 空白填充 |

---

## 颜色使用指南

### 语义化使用

```css
/* 成功状态 */
.success-message {
  color: var(--el-color-success);
  background-color: var(--el-color-success-light-9);
}

/* 警告状态 */
.warning-message {
  color: var(--el-color-warning);
  background-color: var(--el-color-warning-light-9);
}

/* 错误状态 */
.error-message {
  color: var(--el-color-danger);
  background-color: var(--el-color-danger-light-9);
}

/* 信息状态 */
.info-message {
  color: var(--el-color-info);
  background-color: var(--el-color-info-light-9);
}
```

### 文本层次结构

```css
/* 主要文本 - 标题、重要内容 */
h1, h2, h3 {
  color: var(--el-text-color-primary);
}

/* 常规文本 - 正文内容 */
p {
  color: var(--el-text-color-regular);
}

/* 次要文本 - 说明、提示 */
.caption {
  color: var(--el-text-color-secondary);
}

/* 占位符文本 */
input::placeholder {
  color: var(--el-text-color-placeholder);
}
```

---

## CSS 变量汇总

```css
:root {
  /* 主要颜色 */
  --el-color-primary: #409eff;

  /* 语义颜色 */
  --el-color-success: #67c23a;
  --el-color-warning: #e6a23c;
  --el-color-danger: #f56c6c;
  --el-color-info: #909399;

  /* 文本颜色 */
  --el-text-color-primary: #303133;
  --el-text-color-regular: #606266;
  --el-text-color-secondary: #909399;
  --el-text-color-placeholder: #a8abb2;
  --el-text-color-disabled: #c0c4cc;

  /* 背景颜色 */
  --el-bg-color: #ffffff;
  --el-bg-color-page: #f2f3f5;
  --el-bg-color-overlay: #ffffff;

  /* 填充颜色 */
  --el-fill-color: #f0f2f5;
  --el-fill-color-light: #f5f7fa;
  --el-fill-color-lighter: #fafafa;
}
```

---

## 最佳实践

1. **使用 CSS 变量**：始终使用预定义的颜色变量以保持一致性
2. **语义颜色**：为预期目的使用适当语义颜色
3. **文本层次结构**：使用文本颜色变量建立视觉层次
4. **暗黑模式**：颜色在暗黑模式下自动调整
5. **自定义**：覆盖主要颜色以匹配你的品牌
