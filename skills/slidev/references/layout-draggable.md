---
name: draggable-elements
description: 通过拖动在演示期间移动、调整大小和旋转元素
---

# 可拖动元素

通过拖动在演示期间移动、调整大小和旋转元素。

## 指令用法

### 使用 Frontmatter 位置

```md
---
dragPos:
  square: 左,上,宽,高,旋转
---

<img v-drag="'square'" src="https://sli.dev/logo.png">
```

### 内联位置

```md
<img v-drag="[左,上,宽,高,旋转]" src="https://sli.dev/logo.png">
```

## 组件用法

```md
---
dragPos:
  foo: 左,上,宽,高,旋转
---

<v-drag pos="foo" text-3xl>
  可拖动内容
</v-drag>
```

## 可拖动箭头

```md
<v-drag-arrow />
```

## 控制

- 双击：开始拖动
- 箭头键：移动元素
- Shift + 拖动：保持宽高比
- 单击外部：停止拖动

## 自动高度

将高度设置为 `NaN` 或 `_` 以根据内容自动调整高度。
