# 日期范围选择器

日期范围选择器（alpha）

**部分：** `DateRangePickerRoot`、`DateRangePickerField`、`DateRangePickerInput`、`DateRangePickerTrigger`、`DateRangePickerContent`、`DateRangePickerCalendar`、`DateRangePickerHeader`、`DateRangePickerHeading`、`DateRangePickerGrid`、`DateRangePickerCell`、`DateRangePickerCellTrigger`、`DateRangePickerNext`、`DateRangePickerPrev`

## DateRangePickerRoot

### 属性

| 属性                 | 类型                                      | 默认值                                |
| -------------------- | ----------------------------------------- | -------------------------------------- |
| `as`                 | `AsTag \| Component`                      | `"div"`                                |
| `asChild`            | `boolean`                                 | -                                      |
| `defaultOpen`        | `boolean`                                 | `false`                                |
| `defaultPlaceholder` | `DateValue`                               | -                                      |
| `defaultValue`       | `DateRange`                               | `{ start: undefined, end: undefined }` |
| `dir`                | `"ltr" \| "rtl"`                          | -                                      |
| `disabled`           | `boolean`                                 | `false`                                |
| `fixedWeeks`         | `boolean`                                 | `false`                                |
| `granularity`        | `"day" \| "hour" \| "minute" \| "second"` | -                                      |
| `hideTimeZone`       | `boolean`                                 | -                                      |
| `hourCycle`          | `12 \| 24`                                | -                                      |
| `id`                 | `string`                                  | -                                      |
| `isDateDisabled`     | `Matcher`                                 | -                                      |
| `isDateUnavailable`  | `Matcher`                                 | -                                      |
| `locale`             | `string`                                  | `"en"`                                 |
| `maxValue`           | `DateValue`                               | -                                      |
| `minValue`           | `DateValue`                               | -                                      |
| `modal`              | `boolean`                                 | `false`                                |
| `modelValue`         | `DateRange`                               | -                                      |
| `name`               | `string`                                  | -                                      |
| `numberOfMonths`     | `number`                                  | `1`                                    |
| `open`               | `boolean`                                 | -                                      |
| `pagedNavigation`    | `boolean`                                 | `false`                                |
| `placeholder`        | `DateValue`                               | -                                      |
| `preventDeselect`    | `boolean`                                 | `false`                                |
| `readonly`           | `boolean`                                 | `false`                                |
| `required`           | `boolean`                                 | -                                      |
| `weekdayFormat`      | `"narrow" \| "short" \| "long"`           | `"narrow"`                             |
| `weekStartsOn`       | `0 \| 1 \| 2 \| 3 \| 4 \| 5 \| 6`         | `0`                                    |

### 事件

| 事件                | 载荷             |
| -------------------- | ------------------- |
| `update:modelValue`  | `[date: DateRange]` |
| `update:open`        | `[value: boolean]`  |
| `update:placeholder` | `[date: DateValue]` |
| `update:startValue`  | `[date: DateValue]` |

## DateRangePickerField

### 插槽

| 插槽         | 类型                                                 |
| ------------ | ---------------------------------------------------- |
| `segments`   | `{ start: { part: SegmentPart; value: string; }[...` |
| `modelValue` | `DateRange`                                          |

## DateRangePickerInput

### 属性

| 属性      | 类型                                                      | 默认值 |
| --------- | --------------------------------------------------------- | ------- |
| `as`      | `AsTag \| Component`                                      | `"div"` |
| `asChild` | `boolean`                                                 | -       |
| `part`\*  | `"day" \| "month" \| "year" \| "hour" \| "minute" \| ...` | -       |
| `type`\*  | `"start" \| "end"`                                        | -       |

## DateRangePickerTrigger

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## DateRangePickerContent

### 属性

| 属性                          | 类型                                                    | 默认值 |
| ----------------------------- | ------------------------------------------------------- | ------- |
| `align`                       | `"start" \| "center" \| "end"`                          | -       |
| `alignOffset`                 | `number`                                                | -       |
| `arrowPadding`                | `number`                                                | -       |
| `as`                          | `AsTag \| Component`                                    | `"div"` |
| `asChild`                     | `boolean`                                               | -       |
| `avoidCollisions`             | `boolean`                                               | -       |
| `collisionBoundary`           | `Element \| (Element \| null)[] \| null`                | -       |
| `collisionPadding`            | `number \| Partial<Record<"top" \| "right" \| "bott...` | -       |
| `disableOutsidePointerEvents` | `boolean`                                               | -       |
| `forceMount`                  | `boolean`                                               | -       |
| `hideWhenDetached`            | `boolean`                                               | -       |
| `prioritizePosition`          | `boolean`                                               | -       |
| `side`                        | `"top" \| "right" \| "bottom" \| "left"`                | -       |
| `sideOffset`                  | `number`                                                | -       |
| `sticky`                      | `"partial" \| "always"`                                 | -       |
| `trapFocus`                   | `boolean`                                               | -       |
| `updatePositionStrategy`      | `"always" \| "optimized"`                               | -       |

### 事件

| 事件                | 载荷                                               |
| -------------------- | ----------------------------------------------------- |
| `closeAutoFocus`     | `[event: Event]`                                      |
| `escapeKeyDown`      | `[event: KeyboardEvent]`                              |
| `focusOutside`       | `[event: FocusOutsideEvent]`                          |
| `interactOutside`    | `[event: PointerDownOutsideEvent \| FocusOutsideE...` |
| `openAutoFocus`      | `[event: Event]`                                      |
| `pointerDownOutside` | `[event: PointerDownOutsideEvent]`                    |

## DateRangePickerCalendar

### 插槽

| 插槽           | 类型                              |
| -------------- | --------------------------------- |
| `date`         | `DateValue`                       |
| `grid`         | `Grid<DateValue>[]`               |
| `weekDays`     | `string[]`                        |
| `weekStartsOn` | `0 \| 1 \| 2 \| 3 \| 4 \| 5 \| 6` |
| `locale`       | `string`                          |
| `fixedWeeks`   | `boolean`                         |

## DateRangePickerHeader

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## DateRangePickerHeading

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

### 插槽

| 插槽           | 类型     |
| -------------- | -------- |
| `headingValue` | `string` |

## DateRangePickerGrid

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## DateRangePickerCell

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
| `date`\*  | `DateValue`          | -       |

## DateRangePickerCellTrigger

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
| `day`\*   | `DateValue`          | -       |
| `month`\* | `DateValue`          | -       |

## DateRangePickerNext

### 属性

| 属性       | 类型                                      | 默认值 |
| ---------- | ----------------------------------------- | ------- |
| `as`       | `AsTag \| Component`                      | `"div"` |
| `asChild`  | `boolean`                                 | -       |
| `nextPage` | `((placeholder: DateValue) => DateValue)` | -       |
| `step`     | `"month" \| "year"`                       | -       |

## DateRangePickerPrev

### 属性

| 属性       | 类型                                      | 默认值 |
| ---------- | ----------------------------------------- | ------- |
| `as`       | `AsTag \| Component`                      | `"div"` |
| `asChild`  | `boolean`                                 | -       |
| `prevPage` | `((placeholder: DateValue) => DateValue)` | -       |
| `step`     | `"month" \| "year"`                       | -       |
