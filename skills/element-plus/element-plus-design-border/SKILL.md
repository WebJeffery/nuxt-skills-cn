---
name: "element-plus-design-border"
description: "Element Plus 组件的边框设计规范。当用户需要了解边框样式、半径选项和 Element Plus 中使用的阴影样式时调用---

# 边框设计规范

Element Plus 标准化了按钮、卡片、弹出窗口和其他组件中使用边框，以实现一致视觉设计
## 何时使用

- 了解边框样式选项
- 应用一致半径样式
- 使用阴影样式增加深度
- 保持设计一致
---

## 边框样式

Element Plus 提供了几种边框样式选项
### 边框宽度

| 变量 | | 用|
|----------|-------|-------|
| `--el-border-width` | `1px` | 标准边框宽度 |
| `--el-border-width-light` | `1px` | 浅边|
| `--el-border-width-lighter` | `1px` | 更浅边框 |
| `--el-border-width-extra-light` | `1px` | 超浅边框 |

### 边框样式

| 变量 | | 用|
|----------|-------|-------|
| `--el-border-style` | `solid` | 默认实现线边框 |
| `--el-border-color` | `var(--el-border-color-base)` | 默认边框颜色 |

### 边框颜色

| 变量 | 用|
|----------|-------|
| `--el-border-color` | 基础边框颜色 |
| `--el-border-color-light` | 浅边框颜色|
| `--el-border-color-lighter` | 更浅边框颜色 |
| `--el-border-color-extra-light` | 超浅边框颜色 |
| `--el-border-color-dark` | 深边框颜色|
| `--el-border-color-darker` | 更深边框颜色 |

---

## 边框半径

Element Plus 提供标准化边框半径值：

| 变量 | | 用|
|----------|-------|-------|
| `--el-border-radius-base` | `4px` | 组件基础半径 |
| `--el-border-radius-small` | `2px` | 按钮、标签小半径|
| `--el-border-radius-round` | `20px` | 头像、药丸形状圆角半径 |
| `--el-border-radius-circle` | `100%` | 头像圆|

### 使用示例

```css
/* 基础半径 */
.card {
  border-radius: var(--el-border-radius-base);
}

/* 小半径*/
.button {
  border-radius: var(--el-border-radius-small);
}

/* 圆角元素 */
.pill-button {
  border-radius: var(--el-border-radius-round);
}

/* 圆形元素 */
.avatar {
  border-radius: var(--el-border-radius-circle);
}
```

---

## 阴影样式

Element Plus 提供阴影样式以增加深度和高度
| 变量 | 用|
|----------|-------|
| `--el-box-shadow` | 卡片、下拉菜单基础阴影 |
| `--el-box-shadow-light` | 用于微妙高度浅阴影 |
| `--el-box-shadow-lighter` | 用于最小高度更浅阴影 |
| `--el-box-shadow-dark` | 模态框、对话框深阴影 |

### 阴影
```css
:root {
  --el-box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  --el-box-shadow-light: 0 2px 8px 0 rgba(0, 0, 0, 0.06);
  --el-box-shadow-lighter: 0 1px 4px 0 rgba(0, 0, 0, 0.04);
  --el-box-shadow-dark: 0 4px 16px 0 rgba(0, 0, 0, 0.12);
}
```

### 使用示例

```css
/* 卡片阴影 */
.card {
  box-shadow: var(--el-box-shadow-light);
}

/* 下拉菜单阴影 */
.dropdown {
  box-shadow: var(--el-box-shadow);
}

/* 模态框阴影 */
.modal {
  box-shadow: var(--el-box-shadow-dark);
}
```

---

## CSS 变量汇
```css
:root {
  /* 边框 */
  --el-border-width: 1px;
  --el-border-style: solid;
  --el-border-color: var(--el-border-color-base);

  /* 边框颜色 */
  --el-border-color-base: #dcdfe6;
  --el-border-color-light: #e4e7ed;
  --el-border-color-lighter: #ebeef5;
  --el-border-color-extra-light: #f2f6fc;
  --el-border-color-dark: #d4d7de;
  --el-border-color-darker: #cdd0d6;

  /* 边框半径 */
  --el-border-radius-base: 4px;
  --el-border-radius-small: 2px;
  --el-border-radius-round: 20px;
  --el-border-radius-circle: 100%;

  /* 盒阴*/
  --el-box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  --el-box-shadow-light: 0 2px 8px 0 rgba(0, 0, 0, 0.06);
  --el-box-shadow-lighter: 0 1px 4px 0 rgba(0, 0, 0, 0.04);
  --el-box-shadow-dark: 0 4px 16px 0 rgba(0, 0, 0, 0.12);
}
```

---

## 最佳实践
1. **使用 CSS 变量**：始终使用预定义的 CSS 变量以保持一致性2. **匹配上下文*：根据元素重要性选择适当的阴影深度3. **响应式半径*：考虑为移动界面使用较小的半径4. **暗黑模式**：边框颜色在暗黑模式下会自动调整
