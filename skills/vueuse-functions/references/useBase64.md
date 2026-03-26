---
category: Utilities
---

# useBase64

响应式 base64 转换。支持纯文本、缓冲区、文件、画布、对象、映射、集合和图像。

## 用法

```ts
import { useBase64 } from '@vueuse/core'
import { shallowRef } from 'vue'

const text = shallowRef('')

const { base64, promise, execute } = useBase64(text)
```

### 支持的输入类型

- `string` - 纯文本
- `Blob` - 文件或 blob 数据
- `ArrayBuffer` - 二进制数据
- `HTMLCanvasElement` - 画布元素
- `HTMLImageElement` - 图像元素
- `Object` / `Array` / `Map` / `Set` - 序列化为 JSON

### 返回值

| 属性  | 描述                               |
| --------- | ----------------------------------------- |
| `base64`  | 结果 base64 字符串               |
| `promise` | 当前转换的 promise |
| `execute` | 手动触发转换       |

### 数据 URL 格式

默认情况下,输出为数据 URL 格式(例如,`data:text/plain;base64,...`)。设置 `dataUrl: false` 以获取原始 base64。

```ts
const { base64 } = useBase64(text, { dataUrl: false })
// 返回没有数据 URL 前缀的原始 base64
```

### 画布和图像选项

转换画布或图像元素时,您可以指定 MIME 类型和质量。

```ts
const canvas = document.querySelector('canvas')

const { base64 } = useBase64(canvas, {
  type: 'image/jpeg', // MIME 类型
  quality: 0.8, // 图像质量(0-1,用于 jpeg/webp)
})
```

### 自定义序列化器

对于对象、数组、映射和集合,您可以提供自定义序列化器。否则,数据将使用 `JSON.stringify` 序列化(映射转换为对象,集合转换为数组)。

```ts
const data = shallowRef({ foo: 'bar' })

const { base64 } = useBase64(data, {
  serializer: v => JSON.stringify(v, null, 2),
})
```

## 类型声明

```ts
export interface UseBase64Options {
  /**
   * 输出为数据 URL 格式
   *
   * @default true
   */
  dataUrl?: boolean
}
export interface ToDataURLOptions extends UseBase64Options {
  /**
   * MIME 类型
   */
  type?: string | undefined
  /**
   * jpeg 或 webp 的图像质量
   */
  quality?: any
}
export interface UseBase64ObjectOptions<T> extends UseBase64Options {
  serializer?: (v: T) => string
}
export interface UseBase64Return {
  base64: ShallowRef<string>
  promise: ShallowRef<Promise<string>>
  execute: () => Promise<string>
}
export declare function useBase64(
  target: MaybeRefOrGetter<string | undefined>,
  options?: UseBase64Options,
): UseBase64Return
export declare function useBase64(
  target: MaybeRefOrGetter<Blob | undefined>,
  options?: UseBase64Options,
): UseBase64Return
export declare function useBase64(
  target: MaybeRefOrGetter<ArrayBuffer | undefined>,
  options?: UseBase64Options,
): UseBase64Return
export declare function useBase64(
  target: MaybeRefOrGetter<HTMLCanvasElement | undefined>,
  options?: ToDataURLOptions,
): UseBase64Return
export declare function useBase64(
  target: MaybeRefOrGetter<HTMLImageElement | undefined>,
  options?: ToDataURLOptions,
): UseBase64Return
export declare function useBase64<T extends Record<string, unknown>>(
  target: MaybeRefOrGetter<T>,
  options?: UseBase64ObjectOptions<T>,
): UseBase64Return
export declare function useBase64<T extends Map<string, unknown>>(
  target: MaybeRefOrGetter<T>,
  options?: UseBase64ObjectOptions<T>,
): UseBase64Return
export declare function useBase64<T extends Set<unknown>>(
  target: MaybeRefOrGetter<T>,
  options?: UseBase64ObjectOptions<T>,
): UseBase64Return
export declare function useBase64<T>(
  target: MaybeRefOrGetter<T[]>,
  options?: UseBase64ObjectOptions<T[]>,
): UseBase64Return
```
