---
category: Component
outline: deep
---

# createReusableTemplate

在组件作用域内定义和重用模板。

## 动机

通常需要重用模板的某些部分。例如:

```vue
<template>
  <dialog v-if="showInDialog">
    <!-- 复杂的内容 -->
  </dialog>
  <div v-else>
    <!-- 复杂的内容 -->
  </div>
</template>
```

我们希望尽可能重用代码。因此通常我们需要将这些重复的部分提取到组件中。但是,在分离的组件中,您将失去访问本地绑定的能力。为它们定义 props 和 emits 有时会很繁琐。

因此,这个函数提供了一种在组件作用域内定义和重用模板的方法。

## 用法

在之前的示例中,我们可以将其重构为:

```vue
<script setup lang="ts">
import { createReusableTemplate } from '@vueuse/core'

const [DefineTemplate, ReuseTemplate] = createReusableTemplate()
</script>

<template>
  <DefineTemplate>
    <!-- 复杂的内容 -->
  </DefineTemplate>

  <dialog v-if="showInDialog">
    <ReuseTemplate />
  </dialog>
  <div v-else>
    <ReuseTemplate />
  </div>
</template>
```

- `<DefineTemplate>` 将注册模板并且不渲染任何内容。
- `<ReuseTemplate>` 将渲染由 `<DefineTemplate>` 提供的模板。
- `<DefineTemplate>` 必须在 `<ReuseTemplate>` 之前使用。

> **注意**: 建议尽可能提取为单独的组件。滥用此函数可能会导致代码库的不良实践。

### Options API

当与 [Options API](https://vuejs.org/guide/introduction.html#api-styles) 一起使用时,您需要在组件设置之外定义 `createReusableTemplate` 并传递给 `components` 选项,以便在模板中使用它们。

```vue
<script>
import { createReusableTemplate } from '@vueuse/core'
import { defineComponent } from 'vue'

const [DefineTemplate, ReuseTemplate] = createReusableTemplate()

export default defineComponent({
  components: {
    DefineTemplate,
    ReuseTemplate,
  },
  setup() {
    // ...
  },
})
</script>

<template>
  <DefineTemplate v-slot="{ data, msg, anything }">
    <div>{{ data }} passed from usage</div>
  </DefineTemplate>

  <ReuseTemplate :data="data" msg="The first usage" />
</template>
```

### 传递数据

您还可以使用插槽将数据传递给模板:

- 使用 `v-slot="..."` 在 `<DefineTemplate>` 上访问数据
- 直接在 `<ReuseTemplate>` 上绑定数据以将它们传递给模板

```vue
<script setup lang="ts">
import { createReusableTemplate } from '@vueuse/core'

const [DefineTemplate, ReuseTemplate] = createReusableTemplate()
</script>

<template>
  <DefineTemplate v-slot="{ data, msg, anything }">
    <div>{{ data }} passed from usage</div>
  </DefineTemplate>

  <ReuseTemplate :data="data" msg="The first usage" />
  <ReuseTemplate :data="anotherData" msg="The second usage" />
  <ReuseTemplate v-bind="{ data: something, msg: 'The third' }" />
</template>
```

### TypeScript 支持

`createReusableTemplate` 接受泛型类型,为传递给模板的数据提供类型支持:

```vue
<script setup lang="ts">
import { createReusableTemplate } from '@vueuse/core'

// 返回 `DefineTemplate` 和 `ReuseTemplate` 对
const [DefineFoo, ReuseFoo] = createReusableTemplate<{ msg: string }>()

// 您可以创建多个可重用模板
const [DefineBar, ReuseBar] = createReusableTemplate<{ items: string[] }>()
</script>

<template>
  <DefineFoo v-slot="{ msg }">
    <!-- `msg` 被类型化为 `string` -->
    <div>Hello {{ msg.toUpperCase() }}</div>
  </DefineFoo>

  <ReuseFoo msg="World" />

  <!-- @ts-expect-error 类型错误! -->
  <ReuseFoo :msg="1" />
</template>
```

或者,如果您不是数组解构的粉丝,以下用法也是合法的:

```vue
<script setup lang="ts">
import { createReusableTemplate } from '@vueuse/core'

const { define: DefineFoo, reuse: ReuseFoo } = createReusableTemplate<{
  msg: string
}>()
</script>

<template>
  <DefineFoo v-slot="{ msg }">
    <div>Hello {{ msg.toUpperCase() }}</div>
  </DefineFoo>

  <ReuseFoo msg="World" />
</template>
```

```vue
<script setup lang="ts">
import { createReusableTemplate } from '@vueuse/core'

const TemplateFoo = createReusableTemplate<{ msg: string }>()
</script>

<template>
  <TemplateFoo.define v-slot="{ msg }">
    <div>Hello {{ msg.toUpperCase() }}</div>
  </TemplateFoo.define>

  <TemplateFoo.reuse msg="World" />
</template>
```

::: warning
在没有 `v-bind` 的情况下传递 boolean props 不受支持。有关更多详细信息,请参见 [注意事项](#boolean-props) 部分。
:::

### Props 和属性

默认情况下,传递给 `<ReuseTemplate>` 的所有 props 和属性都将传递给模板。如果您不希望某些 props 传递给 DOM,您需要定义运行时 props:

```ts
import { createReusableTemplate } from '@vueuse/core'

const [DefineTemplate, ReuseTemplate] = createReusableTemplate({
  props: {
    msg: String,
    enable: Boolean,
  }
})
```

如果您不想将任何 props 传递给模板,可以传递 `inheritAttrs` 选项:

```ts
import { createReusableTemplate } from '@vueuse/core'

const [DefineTemplate, ReuseTemplate] = createReusableTemplate({
  inheritAttrs: false,
})
```

### 传递插槽

还可以从 `<ReuseTemplate>` 传递插槽。您可以从 `$slots` 在 `<DefineTemplate>` 上访问插槽:

```vue
<script setup lang="ts">
import { createReusableTemplate } from '@vueuse/core'

const [DefineTemplate, ReuseTemplate] = createReusableTemplate()
</script>

<template>
  <DefineTemplate v-slot="{ $slots, otherProp }">
    <div some-layout>
      <!-- 渲染插槽 -->
      <component :is="$slots.default" />
    </div>
  </DefineTemplate>

  <ReuseTemplate>
    <div>Some content</div>
  </ReuseTemplate>
  <ReuseTemplate>
    <div>Another content</div>
  </ReuseTemplate>
</template>
```

## 注意事项

### Boolean props

与 Vue 的行为相反,定义为 `boolean` 的 props 在没有 `v-bind` 或不存在的情况下将分别解析为空字符串或 `undefined`:

```vue
<script setup lang="ts">
import { createReusableTemplate } from '@vueuse/core'

const [DefineTemplate, ReuseTemplate] = createReusableTemplate<{
  value?: boolean
}>()
</script>

<template>
  <DefineTemplate v-slot="{ value }">
    {{ typeof value }}: {{ value }}
  </DefineTemplate>

  <ReuseTemplate :value="true" />
  <!-- boolean: true -->

  <ReuseTemplate :value="false" />
  <!-- boolean: false -->

  <ReuseTemplate value />
  <!-- string: -->

  <ReuseTemplate />
  <!-- undefined: -->
</template>
```

## 参考

此函数从 [vue-reuse-template](https://github.com/antfu/vue-reuse-template) 迁移。

关于重用模板的现有 Vue 讨论/问题:

- [关于重用模板的讨论](https://github.com/vuejs/core/discussions/6898)

替代方法:

- [Vue Macros - `namedTemplate`](https://vue-macros.sxzz.moe/features/named-template.html)
- [`unplugin-vue-reuse-template`](https://github.com/liulinboyi/unplugin-vue-reuse-template)

## 类型声明

```ts
type ObjectLiteralWithPotentialObjectLiterals = Record<
  string,
  Record<string, any> | undefined
>
type GenerateSlotsFromSlotMap<
  T extends ObjectLiteralWithPotentialObjectLiterals,
> = {
  [K in keyof T]: Slot<T[K]>
}
export type DefineTemplateComponent<
  Bindings extends Record<string, any>,
  MapSlotNameToSlotProps extends ObjectLiteralWithPotentialObjectLiterals,
> = DefineComponent & {
  new (): {
    $slots: {
      default: (
        _: Bindings & {
          $slots: GenerateSlotsFromSlotMap<MapSlotNameToSlotProps>
        },
      ) => any
    }
  }
}
export type ReuseTemplateComponent<
  Bindings extends Record<string, any>,
  MapSlotNameToSlotProps extends ObjectLiteralWithPotentialObjectLiterals,
> = DefineComponent<Bindings> & {
  new (): {
    $slots: GenerateSlotsFromSlotMap<MapSlotNameToSlotProps>
  }
}
export type ReusableTemplatePair<
  Bindings extends Record<string, any>,
  MapSlotNameToSlotProps extends ObjectLiteralWithPotentialObjectLiterals,
> = [
  DefineTemplateComponent<Bindings, MapSlotNameToSlotProps>,
  ReuseTemplateComponent<Bindings, MapSlotNameToSlotProps>,
] & {
  define: DefineTemplateComponent<Bindings, MapSlotNameToSlotProps>
  reuse: ReuseTemplateComponent<Bindings, MapSlotNameToSlotProps>
}
export interface CreateReusableTemplateOptions<
  Props extends Record<string, any>,
> {
  /**
   * 从重用组件继承属性。
   *
   * @default true
   */
  inheritAttrs?: boolean
  /**
   * 重用组件的 props 定义。
   */
  props?: ComponentObjectPropsOptions<Props>
}
/**
 * 此函数成对创建 `define` 和 `reuse` 组件,
 * 它还允许传递泛型以与类型绑定。
 *
 * @see https://vueuse.org/createReusableTemplate
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function createReusableTemplate<
  Bindings extends Record<string, any>,
  MapSlotNameToSlotProps extends ObjectLiteralWithPotentialObjectLiterals =
    Record<"default", undefined>,
>(
  options?: CreateReusableTemplateOptions<Bindings>,
): ReusableTemplatePair<Bindings, MapSlotNameToSlotProps>
```
