---
category: Sensors
---

# useKeyModifier

响应式 [Modifier State](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/getModifierState)。跟踪任何[支持的修饰键](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/getModifierState#browser_compatibility)的状态 - 请参阅浏览器兼容性说明。

<CourseLink href="https://vueschool.io/lessons/alt-drag-to-clone-tasks?friend=vueuse">通过 Vue School 的这个免费视频课程学习 useKeyModifier！</CourseLink>

## 用法

```ts
import { useKeyModifier } from '@vueuse/core'

const capsLockState = useKeyModifier('CapsLock')

console.log(capsLockState.value)
```

## 事件

您可以自定义哪些事件将提示状态更新。默认情况下，这些事件是 `mouseup`、`mousedown`、`keyup`、`keydown`。要自定义这些事件：

```ts
import { useKeyModifier } from '@vueuse/core'

const capsLockState = useKeyModifier('CapsLock', { events: ['mouseup', 'mousedown'] })

console.log(capsLockState) // null

// 通过按键打开 Caps Lock
console.log(capsLockState) // null

// 点击鼠标按钮
console.log(capsLockState) // true
```

## 初始状态

默认情况下，返回的 ref 将是 `Ref<null>`，直到接收到第一个事件。您可以通过以下方式显式传递初始状态：

```ts
import { useKeyModifier } from '@vueuse/core'
// ---cut---
const capsLockState1 = useKeyModifier('CapsLock') // Ref<boolean | null>
const capsLockState2 = useKeyModifier('CapsLock', { initial: false }) // Ref<boolean>
```

## Type Declarations

```ts
export type KeyModifier =
  | "Alt"
  | "AltGraph"
  | "CapsLock"
  | "Control"
  | "Fn"
  | "FnLock"
  | "Meta"
  | "NumLock"
  | "ScrollLock"
  | "Shift"
  | "Symbol"
  | "SymbolLock"
export interface UseModifierOptions<Initial> extends ConfigurableDocument {
  /**
   * Event names that will prompt update to modifier states
   *
   * @default ['mousedown', 'mouseup', 'keydown', 'keyup']
   */
  events?: WindowEventName[]
  /**
   * Initial value of the returned ref
   *
   * @default null
   */
  initial?: Initial
}
export type UseKeyModifierReturn<Initial> = ShallowRef<
  Initial extends boolean ? boolean : boolean | null
>
export declare function useKeyModifier<Initial extends boolean | null>(
  modifier: KeyModifier,
  options?: UseModifierOptions<Initial>,
): UseKeyModifierReturn<Initial>
```
