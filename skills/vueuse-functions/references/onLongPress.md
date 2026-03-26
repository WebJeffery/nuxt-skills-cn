---
category: Sensors
---

# onLongPress

监听元素上的长按。返回一个停止函数。

## 用法

```vue
<script setup lang="ts">
import { onLongPress } from '@vueuse/core'
import { shallowRef, useTemplateRef } from 'vue'

const htmlRefHook = useTemplateRef('htmlRefHook')
const longPressedHook = shallowRef(false)

function onLongPressCallbackHook(e: PointerEvent) {
  longPressedHook.value = true
}
function resetHook() {
  longPressedHook.value = false
}

onLongPress(
  htmlRefHook,
  onLongPressCallbackHook,
  {
    modifiers: {
      prevent: true
    }
  }
)
</script>

<template>
  <p>Long Pressed: {{ longPressedHook }}</p>

  <button ref="htmlRefHook" class="ml-2 button small">
    Press long
  </button>

  <button class="ml-2 button small" @click="resetHook">
    Reset
  </button>
</template>
```

### 自定义延迟

默认情况下,处理程序在 500ms 后触发。您可以使用 `delay` 选项自定义它。它可以是一个数字或接收 `PointerEvent` 的函数。

```ts
import { onLongPress } from '@vueuse/core'

// 固定延迟
onLongPress(target, handler, { delay: 1000 })

// 基于事件的动态延迟
onLongPress(target, handler, {
  delay: ev => ev.pointerType === 'touch' ? 800 : 500,
})
```

### 距离阈值

如果指针移动超过阈值(默认:10 像素),长按将被取消。设置为 `false` 以禁用移动检测。

```ts
import { onLongPress } from '@vueuse/core'

// 自定义阈值
onLongPress(target, handler, { distanceThreshold: 20 })

// 禁用移动检测
onLongPress(target, handler, { distanceThreshold: false })
```

### 鼠标抬起回调

您可以提供 `onMouseUp` 回调,以便在指针释放时收到通知。

```ts
import { onLongPress } from '@vueuse/core'

onLongPress(target, handler, {
  onMouseUp(duration, distance, isLongPress) {
    console.log(`Held for ${duration}ms, moved ${distance}px, long press: ${isLongPress}`)
  },
})
```

### 修饰符

以下修饰符可用:

| Modifier  | Description                                  |
| --------- | -------------------------------------------- |
| `stop`    | 调用 `event.stopPropagation()`              |
