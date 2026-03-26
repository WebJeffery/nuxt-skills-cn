---
name: slide-hooks
description: 幻灯片组件的生命周期钩子
---

# 幻灯片钩子

幻灯片组件的生命周期钩子。

## 可用钩子

```ts
import { onSlideEnter, onSlideLeave, useIsSlideActive } from '@slidev/client'

const isActive = useIsSlideActive()

onSlideEnter(() => {
  // 幻灯片变为活动状态时调用
})

onSlideLeave(() => {
  // 幻灯片变为非活动状态时调用
})
```

## 重要

不要在幻灯片中使用 `onMounted` / `onUnmounted` - 即使幻灯片处于非活动状态，组件实例也会持久存在。

请改用 `onSlideEnter` 和 `onSlideLeave`。

## 使用场景

- 启动/停止动画
- 播放/暂停媒体
- 初始化/清理资源
- 跟踪分析
