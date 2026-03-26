---
name: preset-attributify
description: 在 HTML 属性中组合工具类而不是 class
---

# 预设 Attributify

在 HTML 属性中组合工具类以提高可读性。

## 安装

```ts
import { defineConfig, presetAttributify, presetWind3 } from 'unocss'

export default defineConfig({
  presets: [
    presetWind3(),
    presetAttributify(),
  ],
})
```

## 基本用法

代替长的 class 字符串：

```html
<button class="bg-blue-400 hover:bg-blue-500 text-sm text-white font-mono font-light py-2 px-4 rounded border-2 border-blue-200">
  Button
</button>
```

在属性中按前缀分组：

```html
<button
  bg="blue-400 hover:blue-500"
  text="sm white"
  font="mono light"
  p="y-2 x-4"
  border="2 rounded blue-200"
>
  Button
</button>
```

## 前缀自引用

对于匹配其前缀的工具类（`flex`、`grid`、`border`），使用 `~`：

```html
<!-- 之前 -->
<button class="border border-red">Button</button>

<!-- 之后 -->
<button border="~ red">Button</button>
```

## 无值的 Attributify

将工具类用作布尔属性：

```html
<div m-2 rounded text-teal-400 />
```

## 处理属性冲突

当属性名称与 HTML 属性冲突时：

```html
<!-- 使用 un- 前缀 -->
<a un-text="red">Text color to red</a>
```

### 强制前缀

```ts
presetAttributify({
  prefix: 'un-',
  prefixedOnly: true,
})
```

## 选项

```ts
presetAttributify({
  strict: false,           // 仅生成 attributify 的 CSS
  prefix: 'un-',           // 属性前缀
  prefixedOnly: false,     // 要求所有都使用前缀
  nonValuedAttribute: true, // 支持无值属性
  ignoreAttributes: [],    // 要忽略的属性
  trueToNonValued: false,  // 将 value="true" 视为无值
})
```

## TypeScript 支持

### Vue 3

```ts
// html.d.ts
declare module '@vue/runtime-dom' {
  interface HTMLAttributes { [key: string]: any }
}
declare module '@vue/runtime-core' {
  interface AllowedComponentProps { [key: string]: any }
}
export {}
```

### React

```ts
import type { AttributifyAttributes } from '@unocss/preset-attributify'

declare module 'react' {
  interface HTMLAttributes<T> extends AttributifyAttributes {}
}
```

## JSX 支持

对于 JSX 中 `<div foo>` 变为 `<div foo={true}>` 的情况：

```ts
import { transformerAttributifyJsx } from 'unocss'

export default defineConfig({
  transformers: [
    transformerAttributifyJsx(),
  ],
})
```

**重要：** 仅当 `uno.config.*` 显示启用了 `presetAttributify()` 时才使用 attributify。

<!--
源参考：
- https://unocss.dev/presets/attributify
-->
