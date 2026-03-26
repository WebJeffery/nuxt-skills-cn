---
name: drawing
description: 在演示期间绘制和注释幻灯片
---

# 绘图和注释

在演示期间绘制和注释幻灯片。由 drauu 提供支持。

## 启用绘图

点击导航栏中的笔图标或按 `C`。

## 手写笔支持

手写笔（iPad + Apple Pencil）自动工作 - 用笔绘图，用手指导航。

## 持久化绘图

将绘图保存为 SVG 并包含在导出中：

```md
---
drawings:
  persist: true
---
```

绘图保存到 `.slidev/drawings/`。

## 禁用绘图

完全禁用：
```md
---
drawings:
  enabled: false
---
```

仅在开发中：
```md
---
drawings:
  enabled: dev
---
```

仅在演讲者模式中：
```md
---
drawings:
  presenterOnly: true
---
```

## 同步设置

禁用跨实例同步：

```md
---
drawings:
  syncAll: false
---
```

仅演讲者的绘图同步到其他人。
