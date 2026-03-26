# 单选组

互斥选择

**部分：** `RadioGroupRoot`、`RadioGroupItem`、`RadioGroupIndicator`

## RadioGroupRoot

### 属性

| 属性           | 类型                         | 默认值 |
| -------------- | ---------------------------- | ------- |
| `as`           | `AsTag \| Component`         | `"div"` |
| `asChild`      | `boolean`                    | -       |
| `defaultValue` | `string`                     | -       |
| `dir`          | `"ltr" \| "rtl"`             | -       |
| `disabled`     | `boolean`                    | `false` |
| `loop`         | `boolean`                    | `true`  |
| `modelValue`   | `string`                     | -       |
| `name`         | `string`                     | -       |
| `orientation`  | `"vertical" \| "horizontal"` | -       |
| `required`     | `boolean`                    | `false` |

### 事件

| 事件               | 载荷             |
| ------------------- | ------------------- |
| `update:modelValue` | `[payload: string]` |

### 插槽

| 插槽         | 类型                  |
| ------------ | --------------------- |
| `modelValue` | `string \| undefined` |

## RadioGroupItem

### 属性

| 属性       | 类型                 | 默认值    |
| ---------- | -------------------- | ---------- |
| `as`       | `AsTag \| Component` | `"button"` |
| `asChild`  | `boolean`            | -          |
| `disabled` | `boolean`            | `false`    |
| `id`       | `string`             | -          |
| `name`     | `string`             | -          |
| `required` | `boolean`            | -          |
| `value`    | `string`             | -          |

## RadioGroupIndicator

### 属性

| 属性         | 类型                 | 默认值  |
| ------------ | -------------------- | -------- |
| `as`         | `AsTag \| Component` | `"span"` |
| `asChild`    | `boolean`            | -        |
| `forceMount` | `boolean`            | -        |
