---
category: Utilities
---

# useConfirmDialog

创建事件挂钩以支持模态框和确认对话框链。

函数可以在模板上使用,而挂钩是模态框对话框或其他需要用户确认的操作的业务逻辑的方便骨架。

## 函数和挂钩

- `reveal()` - 触发 `onReveal` 挂钩并将 `revealed.value` 设置为 `true`。返回由 `confirm()` 或 `cancel()` 解析的 promise。
- `confirm()` - 将 `isRevealed.value` 设置为 `false` 并触发 `onConfirm` 挂钩。
- `cancel()` - 将 `isRevealed.value` 设置为 `false` 并触发 `onCancel` 挂钩。

## 基本用法

### 使用挂钩

```vue
<script setup lang="ts">
import { useConfirmDialog } from '@vueuse/core'

const { isRevealed, reveal, confirm, cancel, onReveal, onConfirm, onCancel }
  = useConfirmDialog()
</script>

<template>
  <button @click="reveal">
    显示模态框
  </button>

  <teleport to="body">
    <div v-if="isRevealed" class="modal-bg">
      <div class="modal">
        <h2>确认?</h2>
        <button @click="confirm">
          是
        </button>
        <button @click="cancel">
          取消
        </button>
      </div>
    </div>
  </teleport>
</template>
```

### Promise

如果您更喜欢使用 promise:

```vue
<script setup lang="ts">
import { useConfirmDialog } from '@vueuse/core'

const {
  isRevealed,
  reveal,
  confirm,
  cancel,
} = useConfirmDialog()

async function openDialog() {
  const { data, isCanceled } = await reveal()
  if (!isCanceled)
    console.log(data)
}
</script>

<template>
  <button @click="openDialog">
    显示模态框
  </button>

  <teleport to="body">
    <div v-if="isRevealed" class="modal-layout">
      <div class="modal">
        <h2>确认?</h2>
        <button @click="confirm(true)">
          是
        </button>
        <button @click="confirm(false)">
          否
        </button>
      </div>
    </div>
  </teleport>
</template>
```

## 类型声明

```ts
export type UseConfirmDialogRevealResult<C, D> =
  | {
      data?: C
      isCanceled: false
    }
  | {
      data?: D
      isCanceled: true
    }
export interface UseConfirmDialogReturn<RevealData, ConfirmData, CancelData> {
  /**
   * 显示状态
   */
  isRevealed: ComputedRef<boolean>
  /**
   * 打开对话框。
   * 创建 promise 并返回它。触发 `onReveal` 挂钩。
   */
  reveal: (
    data?: RevealData,
  ) => Promise<UseConfirmDialogRevealResult<ConfirmData, CancelData>>
  /**
   * 确认并关闭对话框。触发 `onConfirm` 挂钩内的回调。
   * 使用 `data` 和 `isCanceled` ref 的 `false` 值解析来自 `reveal()` 的 promise。
   * 可以接受任何数据并将其传递给 `onConfirm` 挂钩。
   */
  confirm: (data?: ConfirmData) => void
  /**
   * 取消并关闭对话框。触发 `onCancel` 挂钩内的回调。
   * 使用 `data` 和 `isCanceled` ref 的 `true` 值解析来自 `reveal()` 的 promise。
   * 可以接受任何数据并将其传递给 `onCancel` 挂钩。
   */
  cancel: (data?: CancelData) => void
  /**
   * 在对话框创建之前立即触发的事件挂钩。
   */
  onReveal: EventHookOn<RevealData>
  /**
   * 在 `confirm()` 上调用的事件挂钩。
   * 从 `confirm` 函数获取数据对象。
   */
  onConfirm: EventHookOn<ConfirmData>
  /**
   * 在 `cancel()` 上调用的事件挂钩。
   * 从 `cancel` 函数获取数据对象。
   */
  onCancel: EventHookOn<CancelData>
}
/**
 * 用于创建确认对话框的挂钩。对于模态窗口、弹出窗口和登录很有用。
 *
 * @see https://vueuse.org/useConfirmDialog/
 * @param revealed 处理模态窗口的 `boolean` `ref`
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useConfirmDialog<
  RevealData = any,
  ConfirmData = any,
  CancelData = any,
>(
  revealed?: ShallowRef<boolean>,
): UseConfirmDialogReturn<RevealData, ConfirmData, CancelData>
```
