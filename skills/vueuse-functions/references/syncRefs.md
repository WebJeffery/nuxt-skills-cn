---
category: Reactivity
related: syncRef
---

# syncRefs

使目标 refs 与源 ref 保持同步

## 用法

```ts
import { syncRefs } from '@vueuse/core'
import { shallowRef } from 'vue'

const source = shallowRef('hello')
const target = shallowRef('target')

const stop = syncRefs(source, target)

console.log(target.value) // hello

source.value = 'foo'

console.log(target.value) // foo
```

### 与多个目标同步

您还可以传递一个 ref 数组进行同步。

```ts
import { syncRefs } from '@vueuse/core'
import { shallowRef } from 'vue'

const source = shallowRef('hello')
const target1 = shallowRef('target1')
const target2 = shallowRef('target2')

const stop = syncRefs(source, [target1, target2])

console.log(target1.value) // hello
console.log(target2.value) // hello

source.value = 'foo'

console.log(target1.value) // foo
console.log(target2.value) // foo
```

## Watch 选项

`syncRefs` 的选项类似于 `watch` 的 `WatchOptions`,但默认值不同。

```ts
export interface SyncRefOptions {
  /**
   * 同步的时间,与 watch 的 flush 选项相同
   *
   * @default 'sync'
   */
  flush?: WatchOptionFlush
  /**
   * 深度观察
   *
   * @default false
   */
  deep?: boolean
  /**
   * 立即同步值
   *
   * @default true
   */
  immediate?: boolean
}
```

当设置 `{ flush: 'pre' }` 时,目标引用将在渲染开始之前的[当前"tick"结束时](https://vuejs.org/guide/essentials/watchers.html#callback-flush-timing)更新。

```ts
import { syncRefs } from '@vueuse/core'
import { nextTick, shallowRef } from 'vue'

const source = shallowRef('hello')
const target = shallowRef('target')

syncRefs(source, target, { flush: 'pre' })

console.log(target.value) // hello

source.value = 'foo'

console.log(target.value) // hello <- 仍然未更改,因为 flush 'pre'

await nextTick()

console.log(target.value) // foo <- 已更改!
```

## 类型声明

```ts
export interface SyncRefsOptions extends ConfigurableFlushSync {
  /**
   * 深度观察
   *
   * @default false
   */
  deep?: boolean
  /**
   * 立即同步值
   *
   * @default true
   */
  immediate?: boolean
}
/**
 * 使目标 ref(s) 与源 ref 保持同步
 *
 * @param source 源 ref
 * @param targets
 */
export declare function syncRefs<T>(
  source: WatchSource<T>,
  targets: Ref<T> | Ref<T>[],
  options?: SyncRefsOptions,
): WatchHandle
```
