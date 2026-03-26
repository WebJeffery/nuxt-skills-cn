# 日期字段

日期输入字段（alpha）

**部分：** `DateFieldRoot`、`DateFieldInput`

## DateFieldRoot

### 属性

| 属性                 | 类型                                      | 默认值 |
| -------------------- | ----------------------------------------- | ------- |
| `as`                 | `AsTag \| Component`                      | `"div"` |
| `asChild`            | `boolean`                                 | -       |
| `defaultPlaceholder` | `DateValue`                               | -       |
| `defaultValue`       | `DateValue`                               | -       |
| `dir`                | `"ltr" \| "rtl"`                          | -       |
| `disabled`           | `boolean`                                 | `false` |
| `granularity`        | `"day" \| "hour" \| "minute" \| "second"` | -       |
| `hideTimeZone`       | `boolean`                                 | -       |
| `hourCycle`          | `12 \| 24`                                | -       |
| `id`                 | `string`                                  | -       |
| `isDateUnavailable`  | `Matcher`                                 | -       |
| `locale`             | `string`                                  | `"en"`  |
| `maxValue`           | `DateValue`                               | -       |
| `minValue`           | `DateValue`                               | -       |
| `modelValue`         | `DateValue`                               | -       |
| `name`               | `string`                                  | -       |
| `placeholder`        | `DateValue`                               | -       |
| `readonly`           | `boolean`                                 | `false` |
| `required`           | `boolean`                                 | -       |

### 事件

| 事件                | 载荷             |
| -------------------- | ------------------- |
| `update:modelValue`  | `[date: DateValue]` |
| `update:placeholder` | `[date: DateValue]` |

### 插槽

| 插槽         | 类型                                      |
| ------------ | ----------------------------------------- |
| `modelValue` | `DateValue \| undefined`                  |
| `segments`   | `{ part: SegmentPart; value: string; }[]` |
| `isInvalid`  | `boolean`                                 |

## DateFieldInput

### 属性

| 属性      | 类型                                                      | 默认值 |
| --------- | --------------------------------------------------------- | ------- |
| `as`      | `AsTag \| Component`                                      | `"div"` |
| `asChild` | `boolean`                                                 | -       |
| `part`\*  | `"day" \| "month" \| "year" \| "hour" \| "minute" \| ...` | -       |
