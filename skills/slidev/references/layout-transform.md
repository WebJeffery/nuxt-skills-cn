---
name: transform-component
description: 使用 Transform 组件缩放元素而不影响幻灯片布局
---

# Transform 组件

缩放元素而不影响幻灯片布局。

## 用法

```md
<Transform :scale="0.5" origin="top center">
  <YourElements />
</Transform>
```

## 属性

- `scale`：缩放因子（0.5 = 50%，2 = 200%）
- `origin`：变换原点（CSS transform-origin 值）

## 使用场景

- 缩小大型图表
- 缩放代码块
- 适应超大的内容
- 创建强调效果

## 相关功能

- 缩放所有幻灯片：在 headmatter 中使用 `canvasWidth` / `aspectRatio`
- 缩放单张幻灯片：使用 `zoom` frontmatter 选项
