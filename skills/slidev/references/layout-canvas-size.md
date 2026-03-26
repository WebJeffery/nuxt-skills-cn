---
name: canvas-size
description: 配置幻灯片画布尺寸和宽高比
---

# 幻灯片画布大小

为所有幻灯片设置画布尺寸。

## 配置

```md
---
aspectRatio: 16/9
canvasWidth: 980
---
```

- `aspectRatio`：宽高比（默认：`16/9`）
- `canvasWidth`：画布宽度（像素）（默认：`980`）

## 相关功能

- 缩放单张幻灯片：使用 `zoom` frontmatter 选项
- 缩放元素：使用 `<Transform>` 组件
