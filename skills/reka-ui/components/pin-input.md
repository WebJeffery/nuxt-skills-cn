# PIN 输入

多字符代码输入（OTP）

**部分：** `PinInputRoot`、`PinInputInput`

## PinInputRoot

### 属性

| 属性           | 类型                 | 默认值  |
| -------------- | -------------------- | -------- |
| `as`           | `AsTag \| Component` | `"div"`  |
| `asChild`      | `boolean`            | -        |
| `defaultValue` | `string[]`           | -        |
| `dir`          | `"ltr" \| "rtl"`     | -        |
| `disabled`     | `boolean`            | -        |
| `id`           | `string`             | -        |
| `mask`         | `boolean`            | -        |
| `modelValue`   | `string[]`           | -        |
| `name`         | `string`             | -        |
| `otp`          | `boolean`            | -        |
| `placeholder`  | `string`             | `""`     |
| `required`     | `boolean`            | -        |
| `type`         | `"number" \| "text"` | `"text"` |

### 事件

| 事件               | 载荷             |
| ------------------- | ------------------- |
| `complete`          | `[value: string[]]` |
| `update:modelValue` | `[value: string[]]` |

### 插槽

| 插槽         | 类型       |
| ------------ | ---------- |
| `modelValue` | `string[]` |

## PinInputInput

### 属性

| 属性       | 类型                 | 默认值   |
| ---------- | -------------------- | --------- |
| `as`       | `AsTag \| Component` | `"input"` |
| `asChild`  | `boolean`            | -         |
| `disabled` | `boolean`            | -         |
| `index`\*  | `number`             | -         |
