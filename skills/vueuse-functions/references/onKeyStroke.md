---
category: Sensors
---

# onKeyStroke

监听键盘按键。默认情况下,在 `window` 上监听 `keydown` 事件。

## 用法

```ts
import { onKeyStroke } from '@vueuse/core'

onKeyStroke('ArrowDown', (e) => {
  e.preventDefault()
})
```

有关所有键代码,请参阅[此表](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key/Key_Values)。

### 返回值

返回一个停止函数来移除事件监听器。

```ts
const stop = onKeyStroke('Escape', handler)

// 稍后,停止监听
stop()
```

### 监听多个键

```ts
import { onKeyStroke } from '@vueuse/core'

onKeyStroke(['s', 'S', 'ArrowDown'], (e) => {
  e.preventDefault()
})

// 通过传递 `true` 或跳过 key 参数来监听所有键
onKeyStroke(true, (e) => {
  e.preventDefault()
})
onKeyStroke((e) => {
  e.preventDefault()
})
```

### 自定义键谓词

您可以传递自定义函数来确定哪些键应该触发处理程序。

```ts
import { onKeyStroke } from '@vueuse/core'

onKeyStroke(
  e => e.key === 'A' && e.shiftKey,
  (e) => {
    console.log('Shift+A pressed')
  },
)
```

### 自定义事件目标

```ts
import { onKeyStroke } from '@vueuse/core'

onKeyStroke('A', (e) => {
  console.log('Key A pressed on document')
}, { target: document })
```

### 忽略重复事件

当按下 `A` 并**按住**时,回调只会触发一次。`dedupe` 选项也可以是响应式 ref。

```ts
import { onKeyStroke } from '@vueuse/core'

onKeyStroke('A', (e) => {
  console.log('Key A pressed')
}, { dedupe: true })
```

参考: [KeyboardEvent.repeat](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/repeat)

### 被动模式

设置 `passive: true` 以使用被动事件监听器。

```ts
import { onKeyStroke } from '@vueuse/core'

onKeyStroke('A', handler, { passive: true })
```

## 指令用法
