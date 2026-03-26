# 数字字段

带有增加/减少功能的数字输入

**部分：** `NumberFieldRoot`、`NumberFieldInput`、`NumberFieldIncrement`、`NumberFieldDecrement`

## NumberFieldRoot

### 属性

| 属性            | 类型                  | 默认值   |
| --------------- | --------------------- | --------- |
| `as`            | `AsTag \| Component`  | `"div"`   |
| `asChild`       | `boolean`             | -         |
| `defaultValue`  | `number`              | -         |
| `disabled`      | `boolean`             | -         |
| `formatOptions` | `NumberFormatOptions` | -         |
| `id`            | `string`              | -         |
| `locale`        | `string`              | `"en-US"` |
| `max`           | `number`              | -         |
| `min`           | `number`              | -         |
| `modelValue`    | `number`              | -         |
| `name`          | `string`              | -         |
| `required`      | `boolean`             | -         |
| `step`          | `number`              | `1`       |

### 事件

| 事件               | 载荷         |
| ------------------- | --------------- |
| `update:modelValue` | `[val: number]` |

### 插槽

| 插槽         | 类型     |
| ------------ | -------- |
| `modelValue` | `number` |
| `textValue`  | `string` |

## NumberFieldInput

### 属性

| 属性      | 类型                 | 默认值   |
| --------- | -------------------- | --------- |
| `as`      | `AsTag \| Component` | `"input"` |
| `asChild` | `boolean`            | -         |

## NumberFieldIncrement

### 属性

| 属性       | 类型                 | 默认值    |
| ---------- | -------------------- | ---------- |
| `as`       | `AsTag \| Component` | `"button"` |
| `asChild`  | `boolean`            | -          |
| `disabled` | `boolean`            | -          |

## NumberFieldDecrement

### 属性

| 属性       | 类型                 | 默认值    |
| ---------- | -------------------- | ---------- |
| `as`       | `AsTag \| Component` | `"button"` |
| `asChild`  | `boolean`            | -          |
| `disabled` | `boolean`            | -          |
