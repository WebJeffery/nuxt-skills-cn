# 滑块

范围输入控件

**部分：** `SliderRoot`、`SliderTrack`、`SliderRange`、`SliderThumb`

## SliderRoot

### 属性

| 属性                    | 类型                         | 默认值        |
| ----------------------- | ---------------------------- | -------------- |
| `as`                    | `AsTag \| Component`         | `"div"`        |
| `asChild`               | `boolean`                    | -              |
| `defaultValue`          | `number[]`                   | `[0]`          |
| `dir`                   | `"ltr" \| "rtl"`             | -              |
| `disabled`              | `boolean`                    | `false`        |
| `inverted`              | `boolean`                    | `false`        |
| `max`                   | `number`                     | `100`          |
| `min`                   | `number`                     | `0`            |
| `minStepsBetweenThumbs` | `number`                     | `0`            |
| `modelValue`            | `number[]`                   | -              |
| `name`                  | `string`                     | -              |
| `orientation`           | `"vertical" \| "horizontal"` | `"horizontal"` |
| `step`                  | `number`                     | `1`            |

### 事件

| 事件               | 载荷               |
| ------------------- | --------------------- |
| `update:modelValue` | `[payload: number[]]` |
| `valueCommit`       | `[payload: number[]]` |

### 插槽

| 插槽         | 类型       |
| ------------ | ---------- |
| `modelValue` | `number[]` |

## SliderTrack

### 属性

| 属性      | 类型                 | 默认值  |
| --------- | -------------------- | -------- |
| `as`      | `AsTag \| Component` | `"span"` |
| `asChild` | `boolean`            | -        |

## SliderRange

### 属性

| 属性      | 类型                 | 默认值  |
| --------- | -------------------- | -------- |
| `as`      | `AsTag \| Component` | `"span"` |
| `asChild` | `boolean`            | -        |

## SliderThumb

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
