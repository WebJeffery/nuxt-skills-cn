---
category: '@Integrations'
---

# useFocusTrap

[`focus-trap`](https://github.com/focus-trap/focus-trap) 的响应式包装器。

有关可以传递哪些选项的更多信息，请参阅 `focus-trap` 文档中的 [`createOptions`](https://github.com/focus-trap/focus-trap#createoptions)。

## 安装

```bash
npm i focus-trap@^7
```

## 用法

**基本用法**

```vue
<script setup lang="ts">
import { useFocusTrap } from '@vueuse/integrations/useFocusTrap'
import { useTemplateRef } from 'vue'

const target = useTemplateRef('target')
const { hasFocus, activate, deactivate } = useFocusTrap(target)
</script>

<template>
  <div>
    <button @click="activate()">
      激活
    </button>
    <div ref="target">
      <span>Has Focus: {{ hasFocus }}</span>
      <input type="text">
      <button @click="deactivate()">
        停用
      </button>
    </div>
  </div>
</template>
```

**多个 Ref**

```vue
<script setup lang="ts">
import { useFocusTrap } from '@vueuse/integrations/useFocusTrap'
import { useTemplateRef } from 'vue'

const targetOne = useTemplateRef('targetOne')
const targetTwo = useTemplateRef('targetTwo')
const { hasFocus, activate, deactivate } = useFocusTrap([targetOne, targetTwo])
</script>

<template>
  <div>
    <button @click="activate()">
      激活
    </button>
    <div ref="targetOne">
      <span>Has Focus: {{ hasFocus }}</span>
      <input type="text">
    </div>
    ...
    <div ref="targetTwo">
      <p>Another target here</p>
      <input type="text">
      <button @click="deactivate()">
        停用
      </button>
    </div>
  </div>
</template>
```

**动态焦点目标**

```vue
<script setup lang="ts">
import { useFocusTrap } from '@vueuse/integrations/useFocusTrap'
import { computed, shallowRef, useTemplateRef } from 'vue'

const left = useTemplateRef('left')
const right = useTemplateRef('right')
const currentRef = shallowRef<'left' | 'right'>('left')

const target = computed(() =>
  currentRef.value === 'left'
    ? left
    : currentRef.value === 'right'
      ? right
      : null,
)

const { activate } = useFocusTrap(target)
</script>

<template>
  <div>
    <div ref="left" class="left">
      ...
    </div>
    <div ref="right" class="right">
      ...
    </div>
  </div>
</template>
```

**自动聚焦**

```vue
<script setup lang="ts">
import { useFocusTrap } from '@vueuse/integrations/useFocusTrap'
import { useTemplateRef } from 'vue'

const target = useTemplateRef('target')
const { hasFocus, activate, deactivate } = useFocusTrap(target, { immediate: true })
</script>

<template>
  <div>
    <div ref="target">
      ...
    </div>
  </div>
</template>
```

**条件渲染**

此函数无法在使用 `v-if` 进行条件渲染的元素上正确激活焦点。这是因为它们在焦点激活时不存在于 DOM 中。要解决此问题，您需要在下一个刻度上激活。

```vue
<script setup lang="ts">
import { useFocusTrap } from '@vueuse/integrations/useFocusTrap'
import { nextTick, useTemplateRef } from 'vue'

const target = useTemplateRef('target')
const { activate, deactivate } = useFocusTrap(target, { immediate: true })

const show = ref(false)

async function reveal() {
  show.value = true

  await nextTick()
  activate()
}
</script>

<template>
  <div>
    <div v-if="show" ref="target">
      ...
    </div>

    <button @click="reveal">
      Reveal and Focus
    </button>
  </div>
</template>
```

## 使用组件

使用 `UseFocusTrap` 组件，焦点陷阱将在挂载此组件时自动激活，并在卸载时停用。

```vue
<script setup lang="ts">
import { UseFocusTrap } from '@vueuse/integrations/useFocusTrap/component'
import { shallowRef } from 'vue'

const show = shallowRef(false)
</script>

<template>
  <UseFocusTrap v-if="show" :options="{ immediate: true }">
    <div class="modal">
      ...
    </div>
  </UseFocusTrap>
</template>
```

## 类型声明

```ts
export interface UseFocusTrapOptions extends Options {
  /**
   * 立即激活陷阱
   */
  immediate?: boolean
}
export interface UseFocusTrapReturn {
  /**
   * 指示焦点陷阱当前是否活动
   */
  hasFocus: ShallowRef<boolean>
  /**
   * 指示焦点陷阱当前是否暂停
   */
  isPaused: ShallowRef<boolean>
  /**
   * 激活焦点陷阱
   *
   * @see https://github.com/focus-trap/focus-trap#trapactivateactivateoptions
   * @param opts 激活焦点陷阱选项
   */
  activate: (opts?: ActivateOptions) => void
  /**
   * 停用焦点陷阱
   *
   * @see https://github.com/focus-trap/focus-trap#trapdeactivatedeactivateoptions
   * @param opts 停用焦点陷阱选项
   */
  deactivate: (opts?: DeactivateOptions) => void
  /**
   * 暂停焦点陷阱
   *
   * @see https://github.com/focus-trap/focus-trap#trappause
   */
  pause: Fn
  /**
   * 取消暂停焦点陷阱
   *
   * @see https://github.com/focus-trap/focus-trap#trapunpause
   */
  unpause: Fn
}
/**
 * 响应式焦点陷阱
 *
 * @see https://vueuse.org/useFocusTrap
 */
export declare function useFocusTrap(
  target: MaybeRefOrGetter<
    Arrayable<MaybeRefOrGetter<string> | MaybeComputedElementRef>
  >,
  options?: UseFocusTrapOptions,
): UseFocusTrapReturn
```
