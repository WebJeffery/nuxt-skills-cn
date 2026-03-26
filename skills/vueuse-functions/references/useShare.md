---
category: Browser
---

# useShare

响应式 [Web Share API](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/share)。浏览器提供了可以以文本或文件形式共享内容的功能。

> `share` 方法必须在用户手势（如按钮点击）之后调用。例如，它不能在页面加载时简单地调用。这是为了帮助防止滥用。

## 用法

```ts
import { useShare } from '@vueuse/core'

const { share, isSupported } = useShare()

function startShare() {
  share({
    title: 'Hello',
    text: 'Hello my friend!',
    url: location.href,
  })
}
```

### 传递源 ref

您可以向它传递一个 `ref`，来自源 ref 的更改将反映到您的共享选项中。

```ts {6}
import { ref } from 'vue'

const shareOptions = ref<ShareOptions>({ text: 'foo' })
const { share, isSupported } = useShare(shareOptions)

shareOptions.value.text = 'bar'

share()
```

## Type Declarations

```ts
export interface UseShareOptions {
  title?: string
  files?: File[]
  text?: string
  url?: string
}
export interface UseShareReturn extends Supportable {
  share: (overrideOptions?: MaybeRefOrGetter<UseShareOptions>) => Promise<void>
}
/**
 * Reactive Web Share API.
 *
 * @see https://vueuse.org/useShare
 * @param shareOptions
 * @param options
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useShare(
  shareOptions?: MaybeRefOrGetter<UseShareOptions>,
  options?: ConfigurableNavigator,
): UseShareReturn
```
