# 可编辑

带预览/编辑模式的内联文本编辑

**部分：** `EditableRoot`、`EditableArea`、`EditableInput`、`EditablePreview`、`EditableSubmitTrigger`、`EditableCancelTrigger`、`EditableEditTrigger`

## EditableRoot

### 属性

| 属性                | 类型                                           | 默认值           |
| ------------------- | ---------------------------------------------- | ----------------- |
| `activationMode`    | `"dblclick" \| "focus" \| "none"`              | `"focus"`         |
| `as`                | `AsTag \| Component`                           | `"div"`           |
| `asChild`           | `boolean`                                      | -                 |
| `autoResize`        | `boolean`                                      | `false`           |
| `defaultValue`      | `string`                                       | -                 |
| `dir`               | `"ltr" \| "rtl"`                               | -                 |
| `disabled`          | `boolean`                                      | `false`           |
| `id`                | `string`                                       | -                 |
| `maxLength`         | `number`                                       | -                 |
| `modelValue`        | `string`                                       | -                 |
| `name`              | `string`                                       | -                 |
| `placeholder`       | `string \| { edit: string; preview: string; }` | `"Enter text..."` |
| `readonly`          | `boolean`                                      | -                 |
| `required`          | `boolean`                                      | `false`           |
| `selectOnFocus`     | `boolean`                                      | `false`           |
| `startWithEditMode` | `boolean`                                      | -                 |
| `submitMode`        | `"blur" \| "none" \| "enter" \| "both"`        | `"blur"`          |

### 事件

| 事件               | 载荷                                   |
| ------------------- | ----------------------------------------- |
| `submit`            | `[value: string]`                         |
| `update:modelValue` | `[value: string]`                         |
| `update:state`      | `[state: "cancel" \| "submit" \| "edit"]` |

### 插槽

| 插槽         | 类型                  |
| ------------ | --------------------- |
| `isEditing`  | `boolean`             |
| `modelValue` | `string \| undefined` |
| `isEmpty`    | `boolean`             |
| `submit`     | ``                    |
| `cancel`     | ``                    |
| `edit`       | ``                    |

## EditableArea

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## EditableInput

### 属性

| 属性      | 类型                 | 默认值   |
| --------- | -------------------- | --------- |
| `as`      | `AsTag \| Component` | `"input"` |
| `asChild` | `boolean`            | -         |

## EditablePreview

### 属性

| 属性      | 类型                 | 默认值  |
| --------- | -------------------- | -------- |
| `as`      | `AsTag \| Component` | `"span"` |
| `asChild` | `boolean`            | -        |

## EditableSubmitTrigger

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |

## EditableCancelTrigger

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |

## EditableEditTrigger

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |
