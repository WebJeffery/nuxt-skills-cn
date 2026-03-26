---
name: presenter-timer
description: 演讲者模式中的计时器和进度条
---

# 演讲者计时器

演讲者模式中的计时器和进度条。

## 配置

```yaml
---
duration: 30min
timer: stopwatch
---
```

## 选项

- `duration`：演示文稿长度（默认：`30min`）
- `timer`：模式 - `stopwatch` 或 `countdown`（默认：`stopwatch`）

## 功能

- 开始、暂停、重置控制
- 显示已用/剩余时间的进度条
- 仅在演讲者模式下可见

## 持续时间格式

- `30min` - 30 分钟
- `1h` - 1 小时
- `45min` - 45 分钟
