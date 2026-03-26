---
category: Sensors
---

# onClickOutside

监听元素外部的点击。适用于模态框或下拉菜单。

## 用法

```vue
<script setup lang="ts">
import { onClickOutside } from '@vueuse/core'
import { useTemplateRef } from 'vue'

const target = useTemplateRef('target')

onClickOutside(target, event => console.log(event))
</script>

<template>
  <div ref="target">
    Hello world
  </div>
  <div>Outside element</div>
</template>
```

### 返回值

默认情况下,`onClickOutside` 返回一个 `stop` 函数来移除事件监听器。

```ts
const stop = onClickOutside(target, handler)

// 稍后,停止监听
stop()
```

### 控制

如果您需要更多控制触发处理程序,可以使用 `controls` 选项。这将返回一个包含 `stop`、`cancel` 和 `trigger` 函数的对象。

```ts
const { stop, cancel, trigger } = onClickOutside(
  modalRef,
  (event) => {
    modal.value = false
  },
  { controls: true },
)

// cancel 阻止下一次点击触发处理程序
cancel()

// trigger 手动触发处理程序
trigger(event)

// stop 移除所有事件监听器
stop()
```

### 忽略元素

使用 `ignore` 选项来阻止某些元素触发处理程序。将元素作为 Ref 数组或 CSS 选择器提供。

```ts
const ignoreElRef = useTemplateRef('ignoreEl')

onClickOutside(
  target,
  event => console.log(event),
  { ignore: [ignoreElRef, '.ignore-class', '#ignore-id'] },
)
```

### 捕获阶段

默认情况下,事件监听器使用捕获阶段(`capture: true`)。设置 `capture: false` 以改用冒泡阶段。

```ts
onClickOutside(target, handler, { capture: false })
```

### 检测 Iframe 点击

默认情况下,不会检测 iframe 内部的点击。启用 `detectIframe` 以在焦点移动到 iframe 时也触发处理程序。

```ts
onClickOutside(target, handler, { detectIframe: true })
```

## 组件用法

```vue
<template>
  <OnClickOutside :options="{ ignore: [/* ... */] }" @trigger="count++">
    <div>
      Click Outside of Me
    </div>
  </OnClickOutside>
</template>
```

## 指令用法

```vue
<script setup lang="ts">
import { vOnClickOutside } from '@vueuse/components'
import { shallowRef } from 'vue'

const modal = shallowRef(false)
function closeModal() {
  modal.value = false
}
</script>

<template>
  <button @click="modal = true">
    Open Modal
  </button>
  <div v-if="modal" v-on-click-outside="closeModal">
    Hello World
  </div>
</template>
```

您还可以将处理程序设置为数组来设置指令的配置项。

```vue
<script setup lang="ts">
import { vOnClickOutside } from '@vueuse/components'
import { shallowRef, useTemplateRef } from 'vue'

const modal = shallowRef(false)

const ignoreElRef = useTemplateRef('ignoreEl')

const onClickOutsideHandler = [
  (ev) => {
    console.log(ev)
    modal.value = false
  },
  { ignore: [ignoreElRef] },
]
</script>

<template>
  <button @click="modal = true">
    Open Modal
  </button>

  <div ref="ignoreElRef">
    click outside ignore element
  </div>

  <div v-if="modal" v-on-click-outside="onClickOutsideHandler">
    Hello World
  </div>
</template>
```

## 类型声明

```ts
export interface OnClickOutsideOptions<
  Controls extends boolean = false,
> extends ConfigurableWindow {
  /**
   * 不应触发事件的元素列表,
   * 作为 Refs 或 CSS 选择器提供。
   */
  ignore?: MaybeRefOrGetter<(MaybeElementRef | string)[]>
  /**
   * 对内部事件监听器使用捕获阶段。
   * @default true
   */
  capture?: boolean
  /**
   * 如果焦点移动到 iframe,则运行处理程序函数。
   * @default false
   */
  detectIframe?: boolean
  /**
   * 使用控件来取消/触发监听器。
   * @default false
   */
  controls?: Controls
}
export type OnClickOutsideHandler<
  T extends OnClickOutsideOptions<boolean> = OnClickOutsideOptions,
> = (
  event:
    | (T["detectIframe"] extends true ? FocusEvent : never)
    | (T["controls"] extends true ? Event : never)
    | PointerEvent,
) => void
export type OnClickOutsideReturn<Controls extends boolean = false> =
  Controls extends false
    ? Fn
    : {
        stop: Fn
        cancel: Fn
        trigger: (event: Event) => void
      }
/**
 * 监听元素外部的点击。
 *
 * @see https://vueuse.org/onClickOutside
 * @param target
 * @param handler
 * @param options
 */
export declare function onClickOutside<T extends OnClickOutsideOptions>(
  target: MaybeComputedElementRef,
  handler: OnClickOutsideHandler<T>,
  options?: T,
): Fn
export declare function onClickOutside<T extends OnClickOutsideOptions<true>>(
  target: MaybeComputedElementRef,
  handler: OnClickOutsideHandler<T>,
  options: T,
): {
  stop: Fn
  cancel: Fn
  trigger: (event: Event) => void
}
```
