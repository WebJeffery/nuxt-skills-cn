---
category: Utilities
---

# useOffsetPagination

响应式偏移分页。

## 用法

```ts
import { useOffsetPagination } from '@vueuse/core'

function fetchData({ currentPage, currentPageSize }: { currentPage: number, currentPageSize: number }) {
  fetch(currentPage, currentPageSize).then((responseData) => {
    data.value = responseData
  })
}

const {
  currentPage,
  currentPageSize,
  pageCount,
  isFirstPage,
  isLastPage,
  prev,
  next,
} = useOffsetPagination({
  total: database.value.length,
  page: 1,
  pageSize: 10,
  onPageChange: fetchData,
  onPageSizeChange: fetchData,
})
```

## 组件用法

```vue
<template>
  <UseOffsetPagination
    v-slot="{
      currentPage,
      currentPageSize,
      next,
      prev,
      pageCount,
      isFirstPage,
      isLastPage,
    }"
    :total="database.length"
    @page-change="fetchData"
    @page-size-change="fetchData"
  >
    <div class="gap-x-4 gap-y-2 grid-cols-2 inline-grid items-center">
      <div opacity="50">
        total:
      </div>
      <div>{{ database.length }}</div>
      <div opacity="50">
        pageCount:
      </div>
      <div>{{ pageCount }}</div>
      <div opacity="50">
        currentPageSize:
      </div>
      <div>{{ currentPageSize }}</div>
      <div opacity="50">
        currentPage:
      </div>
      <div>{{ currentPage }}</div>
      <div opacity="50">
        isFirstPage:
      </div>
      <div>{{ isFirstPage }}</div>
      <div opacity="50">
        isLastPage:
      </div>
      <div>{{ isLastPage }}</div>
    </div>
    <div>
      <button :disabled="isFirstPage" @click="prev">
        prev
      </button>
      <button :disabled="isLastPage" @click="next">
        next
      </button>
    </div>
  </UseOffsetPagination>
</template>
```

组件事件支持 props 事件回调和事件监听器。

事件监听器：

```vue
<template>
  <UseOffsetPagination
    v-slot="{
      currentPage,
      currentPageSize,
      next,
      prev,
      pageCount,
      isFirstPage,
      isLastPage,
    }"
    :total="database.length"
    @page-change="fetchData"
    @page-size-change="fetchData"
    @page-count-change="onPageCountChange"
  >
    <!-- 您的代码 -->
  </UseOffsetPagination>
</template>
```

或 props 事件回调：

```vue
<template>
  <UseOffsetPagination
    v-slot="{
      currentPage,
      currentPageSize,
      next,
      prev,
      pageCount,
      isFirstPage,
      isLastPage,
    }"
    :total="database.length"
    :on-page-change="fetchData"
    :on-page-size-change="fetchData"
    :on-page-count-change="onPageCountChange"
  >
    <!-- 您的代码 -->
  </UseOffsetPagination>
</template>
```

## 类型声明

```ts
export interface UseOffsetPaginationOptions {
  /**
   * 项目总数。
   */
  total?: MaybeRefOrGetter<number>
  /**
   * 每页显示的项目数。
   * @default 10
   */
  pageSize?: MaybeRefOrGetter<number>
  /**
   * 当前页码。
   * @default 1
   */
  page?: MaybeRef<number>
  /**
   * 当 `page` 更改时的回调。
   */
  onPageChange?: (
    returnValue: UnwrapNestedRefs<UseOffsetPaginationReturn>,
  ) => unknown
  /**
   * 当 `pageSize` 更改时的回调。
   */
  onPageSizeChange?: (
    returnValue: UnwrapNestedRefs<UseOffsetPaginationReturn>,
  ) => unknown
  /**
   * 当 `pageCount` 更改时的回调。
   */
  onPageCountChange?: (
    returnValue: UnwrapNestedRefs<UseOffsetPaginationReturn>,
  ) => unknown
}
export interface UseOffsetPaginationReturn {
  currentPage: Ref<number>
  currentPageSize: Ref<number>
  pageCount: ComputedRef<number>
  isFirstPage: ComputedRef<boolean>
  isLastPage: ComputedRef<boolean>
  prev: () => void
  next: () => void
}
export type UseOffsetPaginationInfinityPageReturn = Omit<
  UseOffsetPaginationReturn,
  "isLastPage"
>
export declare function useOffsetPagination(
  options: Omit<UseOffsetPaginationOptions, "total">,
): UseOffsetPaginationInfinityPageReturn
export declare function useOffsetPagination(
  options: UseOffsetPaginationOptions,
): UseOffsetPaginationReturn
```
