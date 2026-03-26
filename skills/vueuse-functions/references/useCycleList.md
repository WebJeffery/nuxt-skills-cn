---
category: Utilities
---

# useCycleList

循环遍历项目列表。

<CourseLink href="https://vueschool.io/lessons/create-an-image-carousel-with-vueuse?friend=vueuse">通过 Vue School 的这个免费视频课程学习如何使用 useCycleList 创建图片轮播！</CourseLink>

## 用法

```ts
import { useCycleList } from '@vueuse/core'

const { state, next, prev, go } = useCycleList([
  'Dog',
  'Cat',
  'Lizard',
  'Shark',
  'Whale',
  'Dolphin',
  'Octopus',
  'Seal',
])

console.log(state.value) // 'Dog'

prev()

console.log(state.value) // 'Seal'

go(3)

console.log(state.value) // 'Shark'
```

## 类型声明

```ts
export interface UseCycleListOptions<T> {
  /**
   * state 的初始值。
   * 可以提供一个 ref 来重用。
   */
  initialValue?: MaybeRef<T>
  /**
   * 时的默认索引
   */
  fallbackIndex?: number
  /**
   * 获取当前值索引的自定义函数。
   */
  getIndexOf?: (value: T, list: T[]) => number
}
/**
 * 循环遍历项目列表
 *
 * @see https://vueuse.org/useCycleList
 */
export declare function useCycleList<T>(
  list: MaybeRefOrGetter<T[]>,
  options?: UseCycleListOptions<T>,
): UseCycleListReturn<T>
export interface UseCycleListReturn<T> {
  state: ShallowRef<T>
  index: WritableComputedRef<number>
  next: (n?: number) => T
  prev: (n?: number) => T
  /**
   * 转到特定索引
   */
  go: (i: number) => T
}
```
