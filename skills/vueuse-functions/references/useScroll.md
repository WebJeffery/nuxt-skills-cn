---
category: Sensors
---

# useScroll

响应式滚动位置和状态。

## 用法

```vue
<script setup lang="ts">
import { useScroll } from '@vueuse/core'
import { useTemplateRef } from 'vue'

const el = useTemplateRef('el')
const { x, y, isScrolling, arrivedState, directions } = useScroll(el)
</script>

<template>
  <div ref="el" />
</template>
```

### 使用偏移量

```ts
import { useScroll } from '@vueuse/core'
// ---cut---
const { x, y, isScrolling, arrivedState, directions } = useScroll(el, {
  offset: { top: 30, bottom: 30, right: 30, left: 30 },
})
```

### 设置滚动位置

设置 `x` 和 `y` 值以使元素滚动到该位置。

```vue
<script setup lang="ts">
import { useScroll } from '@vueuse/core'
import { useTemplateRef } from 'vue'

const el = useTemplateRef('el')
const { x, y } = useScroll(el)
</script>

<template>
  <div ref="el" />
  <button @click="x += 10">
    Scroll right 10px
  </button>
  <button @click="y += 10">
    Scroll down 10px
  </button>
</template>
```

### 平滑滚动

设置 `behavior: smooth` 以启用平滑滚动。`behavior` 选项默认为 `auto`，这意味着没有平滑滚动。有关更多信息，请参阅 [`window.scrollTo()`](https://developer.mozilla.org/en-US/docs/Web/API/Window/scrollTo) 上的 `behavior` 选项。

```ts
import { useScroll } from '@vueuse/core'
import { useTemplateRef } from 'vue'

const el = useTemplateRef('el')
const { x, y } = useScroll(el, { behavior: 'smooth' })

// 或者作为 `ref`:
const smooth = ref(false)
const behavior = computed(() => smooth.value ? 'smooth' : 'auto')
const { x, y } = useScroll(el, { behavior })
```

### 重新计算滚动状态

您可以调用 `measure()` 方法来随时手动更新滚动位置和 `arrivedState`。

这很有用，例如，在动态内容更改之后，或者当您想在滚动事件之外重新计算滚动状态时。

```ts
import { useScroll } from '@vueuse/core'
import { nextTick, onMounted, useTemplateRef, watch } from 'vue'

const el = useTemplateRef('el')
const reactiveValue = shallowRef(false)

const { measure } = useScroll(el)

// 在 watcher 中
watch(reactiveValue, () => {
  measure()
})

// 或者在任何函数内部
function updateScrollState() {
  // ...some logic
  nextTick(() => {
    measure()
  })
}
```

> [!注意]
> 建议在 `nextTick()` 内调用 `measure()`，以确保首先更新 DOM。
> 滚动状态在 `onMount` 时自动初始化。
> 如果您想在某些动态更改后重新计算状态，则只需要手动调用 `measure()`。

## 指令用法

```vue
<script setup lang="ts">
import type { UseScrollReturn } from '@vueuse/core'
import { vScroll } from '@vueuse/components'

const data = ref([1, 2, 3, 4, 5, 6])

function onScroll(state: UseScrollReturn) {
  console.log(state) // {x, y, isScrolling, arrivedState, directions}
}
</script>

<template>
  <div v-scroll="onScroll">
    <div v-for="item in data" :key="item">
      {{ item }}
    </div>
  </div>

  <!-- 带选项 -->
  <div v-scroll="[onScroll, { throttle: 10 }]">
    <div v-for="item in data" :key="item">
      {{ item }}
    </div>
  </div>
</template>
```

## 类型声明

```ts
export interface UseScrollOptions extends ConfigurableWindow {
  /**
   * 滚动事件的节流时间，默认禁用。
   *
   * @default 0
   */
  throttle?: number
  /**
   * 滚动结束时的检查时间。
   * 当配置了 `throttle` 时，此配置将设置为 (throttle + idle)。
   *
   * @default 200
   */
  idle?: number
  /**
   * 以 x 像素偏移到达状态
   *
   */
  offset?: {
    left?: number
    right?: number
    top?: number
    bottom?: number
  }
  /**
   * 使用 MutationObserver 监视特定的 DOM 更改，
   * 例如属性修改、子节点添加或删除，或子树更改。
   * @default { mutation: boolean }
   */
  observe?:
    | boolean
    | {
        mutation?: boolean
      }
  /**
   * 滚动时触发。
   *
   */
  onScroll?: (e: Event) => void
  /**
   * 滚动结束时触发。
   *
   */
  onStop?: (e: Event) => void
  /**
   * 滚动事件的监听器选项。
   *
   * @default {capture: false, passive: true}
   */
  eventListenerOptions?: boolean | AddEventListenerOptions
  /**
   * 可选指定 `auto`（默认，无平滑滚动）或
   * `smooth`（用于平滑滚动）的滚动行为，这在更改 `x` 或 `y` refs 时生效。
   *
   * @default 'auto'
   */
  behavior?: MaybeRefOrGetter<ScrollBehavior>
  /**
   * 错误回调
   *
   * 默认将错误记录到 `console.error`
   */
  onError?: (error: unknown) => void
}
export interface UseScrollReturn {
  x: WritableComputedRef<number>
  y: WritableComputedRef<number>
  isScrolling: ShallowRef<boolean>
  arrivedState: {
    left: boolean
    right: boolean
    top: boolean
    bottom: boolean
  }
  directions: {
    left: boolean
    right: boolean
    top: boolean
    bottom: boolean
  }
  measure: () => void
}
/**
 * 响应式滚动。
 *
 * @see https://vueuse.org/useScroll
 * @param element
 * @param options
 */
export declare function useScroll(
  element: MaybeRefOrGetter<
    HTMLElement | SVGElement | Window | Document | null | undefined
  >,
  options?: UseScrollOptions,
): UseScrollReturn
```
