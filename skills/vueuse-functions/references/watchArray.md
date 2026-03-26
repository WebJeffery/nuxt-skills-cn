---
category: Watch
---

# watchArray

监视数组的添加和删除。

## 用法

与 `watch` 类似，但向回调函数提供添加和删除的元素。如果列表使用 `push`、`splice` 等就地更新，请传递 `{ deep: true }`。

```ts
import { watchArray } from '@vueuse/core'

const list = ref([1, 2, 3])

watchArray(list, (newList, oldList, added, removed) => {
  console.log(newList) // [1, 2, 3, 4]
  console.log(oldList) // [1, 2, 3]
  console.log(added) // [4]
  console.log(removed) // []
})

onMounted(() => {
  list.value = [...list.value, 4]
})
```

## 类型声明

```ts
export declare type WatchArrayCallback<V = any, OV = any> = (
  value: V,
  oldValue: OV,
  added: V,
  removed: OV,
  onCleanup: (cleanupFn: () => void) => void,
) => any
/**
 * 监视数组的添加和删除。
 *
 * @see https://vueuse.org/watchArray
 */
export declare function watchArray<
  T,
  Immediate extends Readonly<boolean> = false,
>(
  source: WatchSource<T[]> | T[],
  cb: WatchArrayCallback<T[], Immediate extends true ? T[] | undefined : T[]>,
  options?: WatchOptions<Immediate>,
): WatchHandle
```
