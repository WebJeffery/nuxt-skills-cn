# 范围日历

日期范围日历（alpha）

**部分：** `RangeCalendarRoot`、`RangeCalendarHeader`、`RangeCalendarHeading`、`RangeCalendarGrid`、`RangeCalendarCell`、`RangeCalendarCellTrigger`、`RangeCalendarNext`、`RangeCalendarPrev`

## RangeCalendarRoot

### 属性

| 属性                 | 类型                                      | 默认值                                |
| -------------------- | ----------------------------------------- | -------------------------------------- |
| `as`                 | `AsTag \| Component`                      | `"div"`                                |
| `asChild`            | `boolean`                                 | -                                      |
| `calendarLabel`      | `string`                                  | -                                      |
| `defaultPlaceholder` | `DateValue`                               | -                                      |
| `defaultValue`       | `DateRange`                               | `{ start: undefined, end: undefined }` |
| `dir`                | `"ltr" \| "rtl"`                          | -                                      |
| `disabled`           | `boolean`                                 | `false`                                |
| `fixedWeeks`         | `boolean`                                 | `false`                                |
| `initialFocus`       | `boolean`                                 | `false`                                |
| `isDateDisabled`     | `Matcher`                                 | -                                      |
| `isDateUnavailable`  | `Matcher`                                 | -                                      |
| `locale`             | `string`                                  | `"en"`                                 |
| `maxValue`           | `DateValue`                               | -                                      |
| `minValue`           | `DateValue`                               | -                                      |
| `modelValue`         | `DateRange`                               | -                                      |
| `nextPage`           | `((placeholder: DateValue) => DateValue)` | -                                      |
| `numberOfMonths`     | `number`                                  | `1`                                    |
| `pagedNavigation`    | `boolean`                                 | `false`                                |
| `placeholder`        | `DateValue`                               | -                                      |
| `preventDeselect`    | `boolean`                                 | `false`                                |
| `prevPage`           | `((placeholder: DateValue) => DateValue)` | -                                      |
| `readonly`           | `boolean`                                 | `false`                                |
| `weekdayFormat`      | `"narrow" \| "short" \| "long"`           | `"narrow"`                             |
| `weekStartsOn`       | `0 \| 1 \| 2 \| 3 \| 4 \| 5 \| 6`         | `0`                                    |

### 事件

| 事件                | 载荷             |
| -------------------- | ------------------- |
| `update:modelValue`  | `[date: DateRange]` |
| `update:placeholder` | `[date: DateValue]` |
| `update:startValue`  | `[date: DateValue]` |

### 插槽

| 插槽           | 类型                              |
| -------------- | --------------------------------- |
| `date`         | `DateValue`                       |
| `grid`         | `Grid<DateValue>[]`               |
| `weekDays`     | `string[]`                        |
| `weekStartsOn` | `0 \| 1 \| 2 \| 3 \| 4 \| 5 \| 6` |
| `locale`       | `string`                          |
| `fixedWeeks`   | `boolean`                         |

## RangeCalendarHeader

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## RangeCalendarHeading

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

### 插槽

| 插槽           | 类型     |
| -------------- | -------- |
| `headingValue` | `string` |

## RangeCalendarGrid

### 属性

| 属性      | 类型                 | 默认值   |
| --------- | -------------------- | --------- |
| `as`      | `AsTag \| Component` | `"table"` |
| `asChild` | `boolean`            | -         |

## RangeCalendarCell

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"td"`  |
| `asChild` | `boolean`            | -       |
| `date`\*  | `DateValue`          | -       |

## RangeCalendarCellTrigger

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
| `day`\*   | `DateValue`          | -       |
| `month`\* | `DateValue`          | -       |

### 插槽

| 插槽       | 类型     |
| ---------- | -------- |
| `dayValue` | `string` |

## RangeCalendarNext

### 属性

| 属性       | 类型                                      | 默认值    |
| ---------- | ----------------------------------------- | ---------- |
| `as`       | `AsTag \| Component`                      | `"button"` |
| `asChild`  | `boolean`                                 | -          |
| `nextPage` | `((placeholder: DateValue) => DateValue)` | -          |
| `step`     | `"month" \| "year"`                       | -          |

## RangeCalendarPrev

### 属性

| 属性       | 类型                                      | 默认值    |
| ---------- | ----------------------------------------- | ---------- |
| `as`       | `AsTag \| Component`                      | `"button"` |
| `asChild`  | `boolean`                                 | -          |
| `prevPage` | `((placeholder: DateValue) => DateValue)` | -          |
| `step`     | `"month" \| "year"`                       | -          |
