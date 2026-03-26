---
category: State
related: useRefHistory
---

# useManualRefHistory

在使用调用 `commit()` 时手动跟踪 ref 的更改历史记录，还提供撤销和重做功能

## 用法

```ts {5} twoslash include usage
import { useManualRefHistory } from '@vueuse/core'
import { shallowRef } from 'vue'

const counter = shallowRef(0)
const { history, commit, undo, redo } = useManualRefHistory(counter)

counter.value += 1
commit()

console.log(history.value)
/* [
  { snapshot:1, timestamp: 1601912898062 },
  { snapshot:0, timestamp: 1601912898061 }
] */
```

您可以使用 `undo` 将 ref 值重置为最后一个历史记录点。

```ts
// @include: usage
// ---cut---
console.log(counter.value) // 1
undo()
console.log(counter.value) // 0
```

#### 可变对象的历史记录

如果您要更改源，则需要传递自定义克隆函数或使用 `clone` `true` 作为参数，这是一个最小克隆函数 `x => JSON.parse(JSON.stringify(x))` 的快捷方式，它将在 `dump` 和 `parse` 中使用。

```ts {5}
import { useManualRefHistory } from '@vueuse/core'
import { ref } from 'vue'

const counter = ref({ foo: 1, bar: 2 })
const { history, commit, undo, redo } = useManualRefHistory(counter, { clone: true })

counter.value.foo += 1
commit()
```

#### 自定义克隆函数

要使用功能齐全或自定义克隆函数，您可以通过 `clone` 选项进行设置。

例如，使用 [structuredClone](https://developer.mozilla.org/en-US/docs/Web/API/structuredClone)：

```ts
import { useManualRefHistory } from '@vueuse/core'

const refHistory = useManualRefHistory(target, { clone: structuredClone })
```

或使用 [lodash 的 `cloneDeep`](https://lodash.com/docs/4.17.15#cloneDeep)：

```ts
import { useManualRefHistory } from '@vueuse/core'
import { cloneDeep } from 'lodash-es'

const refHistory = useManualRefHistory(target, { clone: cloneDeep })
```

或更轻量级的 [`klona`](https://github.com/lukeed/klona)：

```ts
import { useManualRefHistory } from '@vueuse/core'
import { klona } from 'klona'

const refHistory = useManualRefHistory(target, { clone: klona })
```

#### 自定义转储和解析函数

除了使用 `clone` 选项外，您还可以传递自定义函数来控制序列化和解析。如果您不需要历史记录值为对象，这可以在撤销时节省额外的克隆。如果您希望快照已经字符串化以保存到本地存储，这也很有用。

```ts
import { useManualRefHistory } from '@vueuse/core'

const refHistory = useManualRefHistory(target, {
  dump: JSON.stringify,
  parse: JSON.parse,
})
```

### 历史记录容量

默认情况下，我们将保留所有历史记录（无限制），直到您显式清除它们，您可以通过 `capacity` 选项设置要保留的历史记录的最大数量。

```ts
import { useManualRefHistory } from '@vueuse/core'

const refHistory = useManualRefHistory(target, {
  capacity: 15, // 限制为 15 个历史记录
})

refHistory.clear() // 显式清除所有历史记录
```

## Type Declarations

```ts
export interface UseRefHistoryRecord<T> {
  snapshot: T
  timestamp: number
}
export interface UseManualRefHistoryOptions<Raw, Serialized = Raw> {
  /**
   * Maximum number of history to be kept. Default to unlimited.
   */
  capacity?: number
  /**
   * Clone when taking a snapshot, shortcut for dump: JSON.parse(JSON.stringify(value)).
   * Default to false
   *
   * @default false
   */
  clone?: boolean | CloneFn<Raw>
  /**
   * Serialize data into the history
   */
  dump?: (v: Raw) => Serialized
  /**
   * Deserialize data from the history
   */
  parse?: (v: Serialized) => Raw
  /**
   * set data source
   */
  setSource?: (source: Ref<Raw>, v: Raw) => void
}
export interface UseManualRefHistoryReturn<Raw, Serialized> {
  /**
   * Bypassed tracking ref from the argument
   */
  source: Ref<Raw>
  /**
   * An array of history records for undo, newest comes to first
   */
  history: Ref<UseRefHistoryRecord<Serialized>[]>
  /**
   * Last history point, source can be different if paused
   */
  last: Ref<UseRefHistoryRecord<Serialized>>
  /**
   * Same as {@link UseManualRefHistoryReturn.history | history}
   */
  undoStack: Ref<UseRefHistoryRecord<Serialized>[]>
  /**
   * Records array for redo
   */
  redoStack: Ref<UseRefHistoryRecord<Serialized>[]>
  /**
   * A ref representing if undo is possible (non empty undoStack)
   */
  canUndo: ComputedRef<boolean>
  /**
   * A ref representing if redo is possible (non empty redoStack)
   */
  canRedo: ComputedRef<boolean>
  /**
   * Undo changes
   */
  undo: () => void
  /**
   * Redo changes
   */
  redo: () => void
  /**
   * Clear all the history
   */
  clear: () => void
  /**
   * Create a new history record
   */
  commit: () => void
  /**
   * Reset ref's value with latest history
   */
  reset: () => void
}
/**
 * Track the change history of a ref, also provides undo and redo functionality.
 *
 * @see https://vueuse.org/useManualRefHistory
 * @param source
 * @param options
 */
export declare function useManualRefHistory<Raw, Serialized = Raw>(
  source: Ref<Raw>,
  options?: UseManualRefHistoryOptions<Raw, Serialized>,
): UseManualRefHistoryReturn<Raw, Serialized>
```
