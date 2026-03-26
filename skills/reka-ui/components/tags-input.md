# 标签输入

多标签输入和管理

**部分：** `TagsInputRoot`、`TagsInputInput`、`TagsInputItem`、`TagsInputItemText`、`TagsInputItemDelete`、`TagsInputClear`

## TagsInputRoot

### 属性

| 属性           | 类型                                        | 默认值            |
| -------------- | ------------------------------------------- | ------------------ |
| `addOnBlur`    | `boolean`                                   | -                  |
| `addOnPaste`   | `boolean`                                   | -                  |
| `addOnTab`     | `boolean`                                   | -                  |
| `as`           | `AsTag \| Component`                        | `"div"`            |
| `asChild`      | `boolean`                                   | -                  |
| `convertValue` | `((value: string) => AcceptableInputValue)` | -                  |
| `defaultValue` | `AcceptableInputValue[]`                    | `[]`               |
| `delimiter`    | `string`                                    | `","`              |
| `dir`          | `"ltr" \| "rtl"`                            | -                  |
| `disabled`     | `boolean`                                   | -                  |
| `displayValue` | `((value: AcceptableInputValue) => string)` | `value.toString()` |
| `duplicate`    | `boolean`                                   | -                  |
| `id`           | `string`                                    | -                  |
| `max`          | `number`                                    | `0`                |
| `modelValue`   | `AcceptableInputValue[]`                    | -                  |
| `name`         | `string`                                    | -                  |
| `required`     | `boolean`                                   | -                  |

### 事件

| 事件               | 载荷                             |
| ------------------- | ----------------------------------- |
| `invalid`           | `[payload: AcceptableInputValue]`   |
| `update:modelValue` | `[payload: AcceptableInputValue[]]` |

### 插槽

| 插槽         | 类型                            |
| ------------ | ------------------------------- |
| `modelValue` | `string \| Record<string, any>` |

## TagsInputInput

### 属性

| 属性          | 类型                 | 默认值   |
| ------------- | -------------------- | --------- |
| `as`          | `AsTag \| Component` | `"input"` |
| `asChild`     | `boolean`            | -         |
| `autoFocus`   | `boolean`            | -         |
| `maxLength`   | `number`             | -         |
| `placeholder` | `string`             | -         |

## TagsInputItem

### 属性

| 属性       | 类型                            | 默认值 |
| ---------- | ------------------------------- | ------- |
| `as`       | `AsTag \| Component`            | `"div"` |
| `asChild`  | `boolean`                       | -       |
| `disabled` | `boolean`                       | -       |
| `value`\*  | `string \| Record<string, any>` | -       |

## TagsInputItemText

### 属性

| 属性      | 类型                 | 默认值  |
| --------- | -------------------- | -------- |
| `as`      | `AsTag \| Component` | `"span"` |
| `asChild` | `boolean`            | -        |

## TagsInputItemDelete

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |

## TagsInputClear

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |
