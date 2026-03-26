# 日历

日期选择网格（alpha）

**部分：** `CalendarRoot`、`CalendarHeader`、`CalendarHeading`、`CalendarGrid`、`CalendarGridHead`、`CalendarGridBody`、`CalendarGridRow`、`CalendarCell`、`CalendarCellTrigger`、`CalendarHeadCell`、`CalendarNext`、`CalendarPrev`

## CalendarRoot

### 属性

| 属性                 | 类型                                      | 默认值    |
| -------------------- | ----------------------------------------- | ---------- |
| `as`                 | `AsTag \| Component`                      | `"div"`    |
| `asChild`            | `boolean`                                 | -          |
| `calendarLabel`      | `string`                                  | -          |
| `defaultPlaceholder` | `DateValue`                               | -          |
| `defaultValue`       | `DateValue`                               | -          |
| `dir`                | `"ltr" \| "rtl"`                          | -          |
| `disabled`           | `boolean`                                 | `false`    |
| `fixedWeeks`         | `boolean`                                 | `false`    |
| `initialFocus`       | `boolean`                                 | `false`    |
| `isDateDisabled`     | `Matcher`                                 | -          |
| `isDateUnavailable`  | `Matcher`                                 | -          |
| `locale`             | `string`                                  | `"en"`     |
| `maxValue`           | `DateValue`                               | -          |
| `minValue`           | `DateValue`                               | -          |
| `modelValue`         | `DateValue \| DateValue[]`                | -          |
| `multiple`           | `boolean`                                 | `false`    |
| `nextPage`           | `((placeholder: DateValue) => DateValue)` | -          |
| `numberOfMonths`     | `number`                                  | `1`        |
| `pagedNavigation`    | `boolean`                                 | `false`    |
| `placeholder`        | `DateValue`                               | -          |
| `preventDeselect`    | `boolean`                                 | `false`    |
| `prevPage`           | `((placeholder: DateValue) => DateValue)` | -          |
| `readonly`           | `boolean`                                 | `false`    |
| `weekdayFormat`      | `"narrow" \| "short" \| "long"`           | `"narrow"` |
| `weekStartsOn`       | `0 \| 1 \| 2 \| 3 \| 4 \| 5 \| 6`         | `0`        |

### 事件

| 事件                | 载荷             |
| -------------------- | ------------------- |
| `update:modelValue`  | `[date: DateValue]` |
| `update:placeholder` | `[date: DateValue]` |

### 插槽

| 插槽           | 类型                              |
| -------------- | --------------------------------- |
| `date`         | `DateValue`                       |
| `grid`         | `Grid<DateValue>[]`               |
| `weekDays`     | `string[]`                        |
| `weekStartsOn` | `0 \| 1 \| 2 \| 3 \| 4 \| 5 \| 6` |
| `locale`       | `string`                          |
| `fixedWeeks`   | `boolean`                         |

## CalendarHeader

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## CalendarHeading

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

### 插槽

| 插槽           | 类型     |
| -------------- | -------- |
| `headingValue` | `string` |

## CalendarGrid

### 属性

| 属性      | 类型                 | 默认值   |
| --------- | -------------------- | --------- |
| `as`      | `AsTag \| Component` | `"table"` |
| `asChild` | `boolean`            | -         |

## CalendarGridHead

### 属性

| 属性      | 类型                 | 默认值   |
| --------- | -------------------- | --------- |
| `as`      | `AsTag \| Component` | `"thead"` |
| `asChild` | `boolean`            | -         |

## CalendarGridBody

### 属性

| 属性      | 类型                 | 默认值   |
| --------- | -------------------- | --------- |
| `as`      | `AsTag \| Component` | `"tbody"` |
| `asChild` | `boolean`            | -         |

## CalendarGridRow

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"tr"`  |
| `asChild` | `boolean`            | -       |

## CalendarCell

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"td"`  |
| `asChild` | `boolean`            | -       |
| `date`\*  | `DateValue`          | -       |

## CalendarCellTrigger

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

## CalendarHeadCell

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"th"`  |
| `asChild` | `boolean`            | -       |

## CalendarNext

### 属性

| 属性       | 类型                                      | 默认值    |
| ---------- | ----------------------------------------- | ---------- |
| `as`       | `AsTag \| Component`                      | `"button"` |
| `asChild`  | `boolean`                                 | -          |
| `nextPage` | `((placeholder: DateValue) => DateValue)` | -          |
| `step`     | `"month" \| "year"`                       | `"month"`  |

## CalendarPrev

### 属性

| 属性       | 类型                                      | 默认值    |
| ---------- | ----------------------------------------- | ---------- |
| `as`       | `AsTag \| Component`                      | `"button"` |
| `asChild`  | `boolean`                                 | -          |
| `prevPage` | `((placeholder: DateValue) => DateValue)` | -          |
| `step`     | `"month" \| "year"`                       | `"month"`  |
