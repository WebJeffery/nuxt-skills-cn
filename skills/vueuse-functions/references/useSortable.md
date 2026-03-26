---
category: '@Integrations'
---

# useSortable

[`sortable`](https://github.com/SortableJS/Sortable) 的包装器。

有关可以传递哪些选项的更多信息，请参阅 `Sortable` 文档中的 [`Sortable.options`](https://github.com/SortableJS/Sortable#options)。

::: warning
目前，`useSortable` 仅实现单个列表的拖放排序。
:::

## 安装

```bash
npm i sortablejs@^1
```

## 用法

### 使用模板 ref

```vue
<script setup lang="ts">
import { useSortable } from '@vueuse/integrations/useSortable'
import { shallowRef, useTemplateRef } from 'vue'

const el = useTemplateRef('el')
const list = shallowRef([{ id: 1, name: 'a' }, { id: 2, name: 'b' }, { id: 3, name: 'c' }])

useSortable(el, list)
</script>

<template>
  <div ref="el">
    <div v-for="item in list" :key="item.id">
      {{ item.name }}
    </div>
  </div>
</template>
```

### 使用指定的选择器进行操作

```vue
<script setup lang="ts">
import { useSortable } from '@vueuse/integrations/useSortable'
import { shallowRef, useTemplateRef } from 'vue'

const el = useTemplateRef('el')
const list = shallowRef([{ id: 1, name: 'a' }, { id: 2, name: 'b' }, { id: 3, name: 'c' }])

const animation = 200

const { option } = useSortable(el, list, {
  handle: '.handle',
  // 或选项设置
  // animation
})

// 您可以使用 option 方法来设置和获取 Sortable 的选项
option('animation', animation)
// option('animation') // 200
</script>

<template>
  <div ref="el">
    <div v-for="item in list" :key="item.id">
      <span>{{ item.name }}</span>
      <span class="handle">*</span>
    </div>
  </div>
</template>
```

### 使用选择器获取根元素

```vue
<script setup lang="ts">
import { useSortable } from '@vueuse/integrations/useSortable'
import { shallowRef } from 'vue'

const list = shallowRef([{ id: 1, name: 'a' }, { id: 2, name: 'b' }, { id: 3, name: 'c' }])

useSortable('#dv', list)
</script>

<template>
  <div id="dv">
    <div v-for="item in list" :key="item.id">
      <span>{{ item.name }}</span>
    </div>
  </div>
</template>
```

### 返回值

| 属性 | 描述                                                      |
| -------- | ---------------------------------------------------------------- |
| `start`  | 初始化 Sortable 实例（在挂载时自动调用） |
| `stop`   | 销毁 Sortable 实例                                    |
| `option` | 在运行时获取或设置 Sortable 选项                           |

```ts
const { start, stop, option } = useSortable(el, list)

// 停止排序
stop()

// 再次开始排序
start()

// 获取/设置选项
option('animation', 200) // 设置
const animation = option('animation') // 获取
```

### 监视元素更改

使用 `watchElement` 选项在元素更改时自动重新初始化 Sortable（对 `v-if` 有用）。

```ts
import { useSortable } from '@vueuse/integrations/useSortable'

useSortable(el, list, {
  watchElement: true, // 在元素更改时自动重新初始化
})
```

### 自定义更新处理程序

如果您想自己处理 `onUpdate`，可以传入 `onUpdate` 参数，我们还公开了一个函数来移动项目位置。

```ts
import { moveArrayElement, useSortable } from '@vueuse/integrations/useSortable'

useSortable(el, list, {
  onUpdate: (e) => {
    // 做一些事情
    moveArrayElement(list, e.oldIndex, e.newIndex, e)
    // 这里需要 nextTick，因为 moveArrayElement 在微任务中执行
    // 所以我们需要等待直到下一个刻度完成。
    nextTick(() => {
      /* 做一些事情 */
    })
  }
})
```

### 辅助函数

还导出了以下辅助函数：

| 函数                                   | 描述                                           |
| ------------------------------------------ | ----------------------------------------------------- |
| `moveArrayElement(list, from, to, event?)` | 将数组中的元素从一个索引移动到另一个索引 |
| `insertNodeAt(parent, element, index)`     | 在特定索引处插入 DOM 节点                 |
| `removeNode(node)`                         | 从其父级中删除 DOM 节点                     |

## Type Declarations

```ts
export interface UseSortableReturn {
  /**
   * start sortable instance
   */
  start: () => void
  /**
   * destroy sortable instance
   */
  stop: () => void
  /**
   * Options getter/setter
   * @param name a Sortable.Options property.
   * @param value a value.
   */
  option: (<K extends keyof Sortable.Options>(
    name: K,
    value: Sortable.Options[K],
  ) => void) &
    (<K extends keyof Sortable.Options>(name: K) => Sortable.Options[K])
}
export interface UseSortableOptions extends Options, ConfigurableDocument {
  /**
   * Watch the element reference for changes and automatically reinitialize Sortable
   * when the element changes.
   *
   * When `false` (default), Sortable is only initialized once on mount.
   * You must manually call `start()` if the element reference changes.
   *
   * When `true`, automatically watches the element reference and reinitializes
   * Sortable whenever it changes (e.g., conditional rendering with v-if).
   *
   * @default false
   */
  watchElement?: boolean
}
export declare function useSortable<T>(
  selector: string,
  list: MaybeRef<T[]>,
  options?: UseSortableOptions,
): UseSortableReturn
export declare function useSortable<T>(
  el: MaybeRefOrGetter<MaybeElement>,
  list: MaybeRef<T[]>,
  options?: UseSortableOptions,
): UseSortableReturn
/**
 * Inserts a element into the DOM at a given index.
 * @param parentElement
 * @param element
 * @param {number} index
 * @see https://github.com/Alfred-Skyblue/vue-draggable-plus/blob/a3829222095e1949bf2c9a20979d7b5930e66f14/src/utils/index.ts#L81C1-L94C2
 */
export declare function insertNodeAt(
  parentElement: Element,
  element: Element,
  index: number,
): void
/**
 * Removes a node from the DOM.
 * @param {Node} node
 * @see https://github.com/Alfred-Skyblue/vue-draggable-plus/blob/a3829222095e1949bf2c9a20979d7b5930e66f14/src/utils/index.ts#L96C1-L102C2
 */
export declare function removeNode(node: Node): void
export declare function moveArrayElement<T>(
  list: MaybeRef<T[]>,
  from: number,
  to: number,
  e?: Sortable.SortableEvent | null,
): void
```
