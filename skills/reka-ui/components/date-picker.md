# 日期选择器

带日历的日期选择器（alpha）

**部分：** `DatePickerRoot`、`DatePickerField`、`DatePickerInput`、`DatePickerTrigger`、`DatePickerContent`、`DatePickerCalendar`、`DatePickerHeader`、`DatePickerHeading`、`DatePickerGrid`、`DatePickerCell`、`DatePickerCellTrigger`、`DatePickerNext`、`DatePickerPrev`、`DatePickerAnchor`、`DatePickerArrow`、`DatePickerClose`

## DatePickerRoot

### 属性

| 属性                 | 类型                                      | 默认值    |
| -------------------- | ----------------------------------------- | ---------- |
| `as`                 | `AsTag \| Component`                      | `"div"`    |
| `asChild`            | `boolean`                                 | -          |
| `defaultOpen`        | `boolean`                                 | `false`    |
| `defaultPlaceholder` | `DateValue`                               | -          |
| `defaultValue`       | `DateValue`                               | -          |
| `dir`                | `"ltr" \| "rtl"`                          | -          |
| `disabled`           | `boolean`                                 | `false`    |
| `fixedWeeks`         | `boolean`                                 | `false`    |
| `granularity`        | `"day" \| "hour" \| "minute" \| "second"` | -          |
| `hideTimeZone`       | `boolean`                                 | -          |
| `hourCycle`          | `12 \| 24`                                | -          |
| `id`                 | `string`                                  | -          |
| `isDateDisabled`     | `Matcher`                                 | -          |
| `isDateUnavailable`  | `Matcher`                                 | -          |
| `locale`             | `string`                                  | `"en"`     |
| `maxValue`           | `DateValue`                               | -          |
| `minValue`           | `DateValue`                               | -          |
| `modal`              | `boolean`                                 | `false`    |
| `modelValue`         | `DateValue`                               | -          |
| `name`               | `string`                                  | -          |
| `numberOfMonths`     | `number`                                  | `1`        |
| `open`               | `boolean`                                 | -          |
| `pagedNavigation`    | `boolean`                                 | `false`    |
| `placeholder`        | `DateValue`                               | -          |
| `preventDeselect`    | `boolean`                                 | `false`    |
| `readonly`           | `boolean`                                 | `false`    |
| `required`           | `boolean`                                 | -          |
| `weekdayFormat`      | `"narrow" \| "short" \| "long"`           | `"narrow"` |
| `weekStartsOn`       | `0 \| 1 \| 2 \| 3 \| 4 \| 5 \| 6`         | `0`        |

### 事件

| 事件                | 载荷             |
| -------------------- | ------------------- |
| `update:modelValue`  | `[date: DateValue]` |
| `update:open`        | `[value: boolean]`  |
| `update:placeholder` | `[date: DateValue]` |

## DatePickerField

### 插槽

| 插槽         | 类型                                      |
| ------------ | ----------------------------------------- |
| `segments`   | `{ part: SegmentPart; value: string; }[]` |
| `modelValue` | `DateValue \| undefined`                  |

## DatePickerInput

### 属性

| 属性      | 类型                                                      | 默认值 |
| --------- | --------------------------------------------------------- | ------- |
| `as`      | `AsTag \| Component`                                      | `"div"` |
| `asChild` | `boolean`                                                 | -       |
| `part`\*  | `"day" \| "month" \| "year" \| "hour" \| "minute" \| ...` | -       |

## DatePickerTrigger

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## DatePickerContent

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

## DatePickerCalendar

### 插槽

| 插槽           | 类型                              |
| -------------- | --------------------------------- |
| `date`         | `DateValue`                       |
| `grid`         | `Grid<DateValue>[]`               |
| `weekDays`     | `string[]`                        |
| `weekStartsOn` | `0 \| 1 \| 2 \| 3 \| 4 \| 5 \| 6` |
| `locale`       | `string`                          |
| `fixedWeeks`   | `boolean`                         |

## DatePickerHeader

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## DatePickerHeading

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

### 插槽

| 插槽           | 类型     |
| -------------- | -------- |
| `headingValue` | `string` |

## DatePickerGrid

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## DatePickerCell

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
| `date`\*  | `DateValue`          | -       |

## DatePickerCellTrigger

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
| `day`\*   | `DateValue`          | -       |
| `month`\* | `DateValue`          | -       |

## DatePickerNext

### 属性

| 属性       | 类型                                      | 默认值 |
| ---------- | ----------------------------------------- | ------- |
| `as`       | `AsTag \| Component`                      | `"div"` |
| `asChild`  | `boolean`                                 | -       |
| `nextPage` | `((placeholder: DateValue) => DateValue)` | -       |
| `step`     | `"month" \| "year"`                       | -       |

## DatePickerPrev

### 属性

| 属性       | 类型                                      | 默认值 |
| ---------- | ----------------------------------------- | ------- |
| `as`       | `AsTag \| Component`                      | `"div"` |
| `asChild`  | `boolean`                                 | -       |
| `prevPage` | `((placeholder: DateValue) => DateValue)` | -       |
| `step`     | `"month" \| "year"`                       | -       |

## DatePickerAnchor

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
| `element` | `Measurable`         | -       |

## DatePickerArrow

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
| `height`  | `number`             | -       |
| `width`   | `number`             | -       |

## DatePickerClose

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
