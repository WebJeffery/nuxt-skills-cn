---
category: Component
outline: deep
---

# createTemplatePromise

模板即 Promise。用于构建自定义对话框、模态框、Toast 等。

## 用法

```vue
<script setup lang="ts">
import { createTemplatePromise } from '@vueuse/core'

const TemplatePromise = createTemplatePromise<ReturnType>()

async function open() {
  const result = await TemplatePromise.start()
  // 按钮被点击,result 为 'ok'
}
</script>

<template>
  <TemplatePromise v-slot="{ promise, resolve, reject, args }">
    <!-- 您的 UI -->
    <button @click="resolve('ok')">
      OK
    </button>
  </TemplatePromise>
</template>
```

## 特性

- **程序化** - 将 UI 作为 promise 调用
- **模板** - 使用 Vue 模板渲染,而不是新的 DSL
- **TypeScript** - 通过泛型类型实现完整的类型安全
- **无渲染** - 您完全控制 UI
- **过渡** - 使用支持 Vue 过渡

此函数从 [vue-template-promise](https://github.com/antfu/vue-template-promise) 迁移

## 用法

`createTemplatePromise` 返回一个 **Vue 组件**,您可以直接在模板中使用它与 `<script setup>`

```ts twoslash include main
import { createTemplatePromise } from '@vueuse/core'

const TemplatePromise = createTemplatePromise()
const MyPromise = createTemplatePromise<boolean>() // 带泛型类型
```

在模板中,使用 `v-slot` 访问 promise 和 resolve 函数。

```vue
<template>
  <TemplatePromise v-slot="{ promise, resolve, reject, args }">
    <!-- 您可以有任何内容 -->
    <button @click="resolve('ok')">
      OK
    </button>
  </TemplatePromise>
  <MyPromise v-slot="{ promise, resolve, reject, args }">
    <!-- 另一个 -->
  </MyPromise>
</template>
```

插槽最初不会渲染(类似于 `v-if="false"`),直到您从组件调用 `start` 方法。

```ts
// @include: main
// ---cut---
const result = await TemplatePromise.start()
```

一旦在模板中调用了 `resolve` 或 `reject`,promise 将被解析或拒绝,返回您传入的值。解析后,插槽将自动删除。

### 传递参数

您可以使用参数将参数传递给 `start`。

```ts twoslash include passing-arguments
import { createTemplatePromise } from '@vueuse/core'

const TemplatePromise = createTemplatePromise<boolean, [string, number]>()
```

```ts
// @include: passing-arguments
// ---cut---
const result = await TemplatePromise.start('hello', 123) // Pr
```

在模板插槽中,您可以通过 `args` 属性访问参数。

```vue
<template>
  <TemplatePromise v-slot="{ args, resolve }">
    <div>{{ args[0] }}</div>
    <!-- hello -->
    <div>{{ args[1] }}</div>
    <!-- 123 -->
    <button @click="resolve(true)">
      OK
    </button>
  </TemplatePromise>
</template>
```

### 单例模式

使用 `singleton` 选项确保一次只能有一个 promise 实例处于活动状态。如果在 promise 已经活动时调用 `start`,它将返回现有的 promise,而不是创建新的 promise。

```ts
import { createTemplatePromise } from '@vueuse/core'

const TemplatePromise = createTemplatePromise<boolean>({
  singleton: true,
})

// 如果快速连续调用,这些将返回相同的 promise
const result1 = TemplatePromise.start()
const result2 = TemplatePromise.start() // 返回与 result1 相同的 promise
```

### 过渡

您可以使用过渡来动画化插槽。

```vue
<script setup lang="ts">
const TemplatePromise = createTemplatePromise<ReturnType>({
  transition: {
    name: 'fade',
    appear: true,
  },
})
</script>

<template>
  <TemplatePromise v-slot="{ resolve }">
    <!-- 您的 UI -->
    <button @click="resolve('ok')">
      OK
    </button>
  </TemplatePromise>
</template>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>
```

了解更多关于 [Vue 过渡](https://vuejs.org/guide/built-ins/transition.html)。

### 插槽 Props

插槽提供以下 props:

| Prop          | Type                                     | Description                                               |
| ------------- | ---------------------------------------- | --------------------------------------------------------- |
| `promise`     | `Promise<Return> \| undefined`           | 当前的 promise 实例                              |
| `resolve`     | `(v: Return \| Promise<Return>) => void` | 使用值解析 promise                          |
| `reject`      | `(v: any) => void`                       | 拒绝 promise                                        |
| `args`        | `Args`                                   | 传递给 `start()` 的参数                             |
| `isResolving` | `boolean`                                | 当解析传递给 `resolve` 的另一个 promise 时为 `true` |
| `key`         | `number`                                 | 列表渲染的唯一键                             |

```vue
<template>
  <TemplatePromise v-slot="{ promise, resolve, reject, args, isResolving }">
    <div v-if="isResolving">
      Loading...
    </div>
    <div v-else>
      <button @click="resolve('ok')">
        OK
      </button>
      <button @click="reject('cancelled')">
        Cancel
      </button>
    </div>
  </TemplatePromise>
</template>
```

## 动机

以编程方式调用对话框或模态框的常见方法是这样的:

```ts
const dialog = useDialog()
const result = await dialog.open({
  title: 'Hello',
  content: 'World',
})
```

这可以通过将这些信息发送到顶层组件并让它渲染对话框来工作。但是,它限制了您可以在 UI 中表达的灵活性。例如,您可能希望标题是红色的,或者有额外的按钮等。您最终会得到很多选项,比如:

```ts
const result = await dialog.open({
  title: 'Hello',
  titleClass: 'text-red',
  content: 'World',
  contentClass: 'text-blue text-sm',
  buttons: [
    { text: 'OK', class: 'bg-red', onClick: () => {} },
    { text: 'Cancel', class: 'bg-blue', onClick: () => {} },
  ],
  // ...
})
```

即使这还不够灵活。如果您想要更多,您最终可能会使用手动渲染函数。

```ts
const result = await dialog.open({
  title: 'Hello',
  contentSlot: () => h(MyComponent, { content }),
})
```

这就像在脚本中重新发明一个新的 DSL 来表达 UI 模板。

因此,这个函数允许**在模板而不是脚本中表达 UI**,这是它应该在的地方,同时仍然可以以编程方式进行操作。

## 类型声明

```ts
export interface TemplatePromiseProps<Return, Args extends any[] = []> {
  /**
   * Promise 实例。
   */
  promise: Promise<Return> | undefined
  /**
   * 解析 promise。
   */
  resolve: (v: Return | Promise<Return>) => void
  /**
   * 拒绝 promise。
   */
  reject: (v: any) => void
  /**
   * 传递给 TemplatePromise.start() 的参数
   */
  args: Args
  /**
   * 指示 promise 是否正在解析。
   * 当将另一个 promise 传递给 `resolve` 时,这将设置为 `true`,直到 promise 被解析。
   */
  isResolving: boolean
  /**
   * 传递给 createTemplatePromise() 的选项
   */
  options: TemplatePromiseOptions
  /**
   * 列表渲染的唯一键。
   */
  key: number
}
export interface TemplatePromiseOptions {
  /**
   * 确定 promise 是否一次只能调用一次。
   *
   * @default false
   */
  singleton?: boolean
  /**
   * Promise 的过渡 props。
   */
  transition?: TransitionGroupProps
}
export type TemplatePromise<
  Return,
  Args extends any[] = [],
> = DefineComponent<object> & {
  new (): {
    $slots: {
      default: (_: TemplatePromiseProps<Return, Args>) => any
    }
  }
} & {
  start: (...args: Args) => Promise<Return>
}
/**
 * 创建模板 promise 组件。
 *
 * @see https://vueuse.org/createTemplatePromise
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function createTemplatePromise<Return, Args extends any[] = []>(
  options?: TemplatePromiseOptions,
): TemplatePromise<Return, Args>
```
