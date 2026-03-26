---
name: zoom-slides
description: 使用 zoom frontmatter 选项缩放单张幻灯片内容
---

# 缩放幻灯片

缩放单张幻灯片内容。

## 用法

```md
---
zoom: 0.8
---

# 包含大量内容的幻灯片

---

# 其他幻灯片不受影响
```

## 值

- `zoom: 0.8` - 80% 大小（容纳更多内容）
- `zoom: 1.2` - 120% 大小（更大，内容更少）
- `zoom: 1` - 正常（默认）

## 使用场景

- 将密集内容放在一张幻灯片上
- 使文本更易读
- 针对不同的内容密度进行调整

## 相关功能

- 缩放所有幻灯片：在 headmatter 中使用 `canvasWidth` / `aspectRatio`
- 缩放元素：使用 `<Transform>` 组件
