---
category: Browser
---

# useScriptTag

创建一个 script 标签，支持自动卸载（删除）script 标签在卸载时。

如果给定 URL 的 script 标签已存在，`useScriptTag()` 将不会创建另一个 script 标签，但请记住，根据您如何使用它，`useScriptTag()` 可能已经加载然后从 `useScriptTag()` 的先前调用中卸载了该特定 JS 文件。

## 用法

```ts
import { useScriptTag } from '@vueuse/core'

useScriptTag(
  'https://player.twitch.tv/js/embed/v1.js',
  // 在 script 标签加载时。
  (el: HTMLScriptElement) => {
    // 做一些事情
  },
)
```

script 将在组件挂载时自动加载，并在组件卸载时删除。

## 配置

设置 `manual: true` 以对加载脚本的时机进行手动控制。

```ts
import { useScriptTag } from '@vueuse/core'

const { scriptTag, load, unload } = useScriptTag(
  'https://player.twitch.tv/js/embed/v1.js',
  () => {
    // 做一些事情
  },
  { manual: true },
)

// 手动控制
await load()
await unload()
```

## Type Declarations

```ts
export interface UseScriptTagOptions extends ConfigurableDocument {
  /**
   * Load the script immediately
   *
   * @default true
   */
  immediate?: boolean
  /**
   * Add `async` attribute to the script tag
   *
   * @default true
   */
  async?: boolean
  /**
   * Script type
   *
   * @default 'text/javascript'
   */
  type?: string
  /**
   * Manual controls the timing of loading and unloading
   *
   * @default false
   */
  manual?: boolean
  crossOrigin?: "anonymous" | "use-credentials"
  referrerPolicy?:
    | "no-referrer"
    | "no-referrer-when-downgrade"
    | "origin"
    | "origin-when-cross-origin"
    | "same-origin"
    | "strict-origin"
    | "strict-origin-when-cross-origin"
    | "unsafe-url"
  noModule?: boolean
  defer?: boolean
  /**
   * Add custom attribute to the script tag
   *
   */
  attrs?: Record<string, string>
  /**
   * Nonce value for CSP (Content Security Policy)
   * @default undefined
   */
  nonce?: string
}
export interface UseScriptTagReturn {
  scriptTag: ShallowRef<HTMLScriptElement | null>
  load: (waitForScriptLoad?: boolean) => Promise<HTMLScriptElement | boolean>
  unload: () => void
}
/**
 * Async script tag loading.
 *
 * @see https://vueuse.org/useScriptTag
 * @param src
 * @param onLoaded
 * @param options
 */
export declare function useScriptTag(
  src: MaybeRefOrGetter<string>,
  onLoaded?: (el: HTMLScriptElement) => void,
  options?: UseScriptTagOptions,
): UseScriptTagReturn
```
