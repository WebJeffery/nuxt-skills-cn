---
category: '@Integrations'
---

# useNProgress

[`nprogress`](https://github.com/rstacruz/nprogress) 的响应式包装器。

## 安装

```bash
npm i nprogress@^0
```

## 用法

```ts {6}
import { useNProgress } from '@vueuse/integrations/useNProgress'

const { isLoading } = useNProgress()

function toggle() {
  isLoading.value = !isLoading.value
}
```

### 传递进度百分比

您可以传递一个百分比来指示进度条应该从哪里开始。

```ts {3}
import { useNProgress } from '@vueuse/integrations/useNProgress'

const { progress } = useNProgress(0.5)

function done() {
  progress.value = 1.0
}
```

> 要更改进度百分比，请设置 `progress.value = n`，其中 n 是 0..1 之间的数字。

### 自定义

只需编辑 [nprogress.css](https://github.com/rstacruz/nprogress/blob/master/nprogress.css) 以满足您的喜好。提示：您可能只想查找并替换 #29d 的出现次数。

您可以通过将对象作为第二个参数传递来[配置](https://github.com/rstacruz/nprogress#configuration)它。

```ts {4}
import { useNProgress } from '@vueuse/integrations/useNProgress'

useNProgress(null, {
  minimum: 0.1,
  // ...
})
```

## 类型声明

```ts
export type UseNProgressOptions = Partial<NProgressOptions>
export interface UseNProgressReturn {
  isLoading: WritableComputedRef<boolean, boolean>
  progress: Ref<number | null | undefined>
  start: () => NProgress
  done: (force?: boolean) => NProgress
  remove: () => void
}
/**
 * 响应式进度条。
 *
 * @see https://vueuse.org/useNProgress
 */
export declare function useNProgress(
  currentProgress?: MaybeRefOrGetter<number | null | undefined>,
  options?: UseNProgressOptions,
): UseNProgressReturn
```
