# 日期范围字段

日期范围输入（alpha）

**部分：** `DateRangeFieldRoot`、`DateRangeFieldInput`

## DateRangeFieldRoot

### 属性

| 属性                 | 类型                                      | 默认值 |
| -------------------- | ----------------------------------------- | ------- |
| `as`                 | `AsTag \| Component`                      | `"div"` |
| `asChild`            | `boolean`                                 | -       |
| `defaultPlaceholder` | `DateValue`                               | -       |
| `defaultValue`       | `DateRange`                               | -       |
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
| `modelValue`         | `DateRange`                               | -       |
| `name`               | `string`                                  | -       |
| `placeholder`        | `DateValue`                               | -       |
| `readonly`           | `boolean`                                 | `false` |
| `required`           | `boolean`                                 | -       |

### 事件

| 事件                | 载荷             |
| -------------------- | ------------------- |
| `update:modelValue`  | `[DateRange]`       |
| `update:placeholder` | `[date: DateValue]` |

### 插槽

| 插槽         | 类型                                                 |
| ------------ | ---------------------------------------------------- |
| `modelValue` | `DateRange`                                          |
| `segments`   | `{ start: { part: SegmentPart; value: string; }[...` |

## DateRangeFieldInput

### 属性

| 属性      | 类型                                                      | 默认值 |
| --------- | --------------------------------------------------------- | ------- |
| `as`      | `AsTag \| Component`                                      | `"div"` |
| `asChild` | `boolean`                                                 | -       |
| `part`\*  | `"day" \| "month" \| "year" \| "hour" \| "minute" \| ...` | -       |
| `type`\*  | `"start" \| "end"`                                        | -       |
