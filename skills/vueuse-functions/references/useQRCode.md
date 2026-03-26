---
category: '@Integrations'
---

# useQRCode

[`qrcode`](https://github.com/soldair/node-qrcode) 的包装器。

## 安装

```bash
npm i qrcode@^1
```

## 用法

```ts
import { useQRCode } from '@vueuse/integrations/useQRCode'

// `qrcode` 将是数据 URL 的 ref
const qrcode = useQRCode('text-to-encode')
```

或向其传递一个 `ref`，返回的数据 URL ref 将随源 ref 的更改而更改。

```ts
import { useQRCode } from '@vueuse/integrations/useQRCode'
import { shallowRef } from 'vue'

const text = shallowRef('text-to-encode')
const qrcode = useQRCode(text)
```

```html
<input v-model="text" type="text" />
<img :src="qrcode" alt="QR Code" />
```

## Type Declarations

```ts
/**
 * Wrapper for qrcode.
 *
 * @see https://vueuse.org/useQRCode
 * @param text
 * @param options
 */
export declare function useQRCode(
  text: MaybeRefOrGetter<string>,
  options?: QRCode.QRCodeToDataURLOptions,
): ShallowRef<string, string>
```
