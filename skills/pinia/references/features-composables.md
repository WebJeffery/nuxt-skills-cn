---
name: composables-in-stores
description: 在 Pinia stores 中使用 Vue composables
---

# 在 Stores 中使用 Composables

Pinia stores 可以利用 Vue composables 来实现可复用的有状态逻辑。

## Option Stores

在 `state` 属性中调用 composables，但只能使用那些返回可写 ref 的:

```ts
import { defineStore } from 'pinia'
import { useLocalStorage } from '@vueuse/core'

export const useAuthStore = defineStore('auth', {
  state: () => ({
    user: useLocalStorage('pinia/auth/login', 'bob'),
  }),
})
```

**有效:** 返回 `ref()` 的 composables:
- `useLocalStorage`
- `useAsyncState`

**在 Option Stores 中无效:**
- 暴露函数的 composables
- 暴露只读数据的 composables

## Setup Stores

更灵活 - 可以使用几乎任何 composable:

```ts
import { defineStore } from 'pinia'
import { useMediaControls } from '@vueuse/core'
import { ref } from 'vue'

export const useVideoPlayer = defineStore('video', () => {
  const videoElement = ref<HTMLVideoElement>()
  const src = ref('/data/video.mp4')
  const { playing, volume, currentTime, togglePictureInPicture } =
    useMediaControls(videoElement, { src })

  function loadVideo(element: HTMLVideoElement, newSrc: string) {
    videoElement.value = element
    src.value = newSrc
  }

  return {
    src,
    playing,
    volume,
    currentTime,
    loadVideo,
    togglePictureInPicture,
  }
})
```

**注意:** 不要返回不可序列化的 DOM ref 如 `videoElement` - 它们是内部实现细节。

## SSR 考量

### 带有 hydrate() 的 Option Stores

定义一个 `hydrate()` 函数来处理客户端水合:

```ts
import { defineStore } from 'pinia'
import { useLocalStorage } from '@vueuse/core'

export const useAuthStore = defineStore('auth', {
  state: () => ({
    user: useLocalStorage('pinia/auth/login', 'bob'),
  }),

  hydrate(state, initialState) {
    // 忽略服务器状态，从浏览器读取
    state.user = useLocalStorage('pinia/auth/login', 'bob')
  },
})
```

### 带有 skipHydrate() 的 Setup Stores

标记不应从服务器水合的状态:

```ts
import { defineStore, skipHydrate } from 'pinia'
import { useEyeDropper, useLocalStorage } from '@vueuse/core'

export const useColorStore = defineStore('colors', () => {
  const { isSupported, open, sRGBHex } = useEyeDropper()
  const lastColor = useLocalStorage('lastColor', sRGBHex)

  return {
    // 跳过客户端专用状态的水合
    lastColor: skipHydrate(lastColor),
    open,       // 函数 - 不需要水合
    isSupported, // 布尔值 - 不是响应式的
  }
})
```

`skipHydrate()` 仅适用于状态属性 (refs)，不适用于函数或非响应式值。

<!--
源引用:
- https://pinia.vuejs.org/cookbook/composables.html
-->
