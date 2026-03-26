# 列表框

可访问的列表选择

**部分：** `ListboxRoot`、`ListboxContent`、`ListboxFilter`、`ListboxItem`、`ListboxItemIndicator`、`ListboxGroup`、`ListboxGroupLabel`、`ListboxVirtualizer`

## ListboxRoot

### 属性

| 属性                | 类型                                                  | 默认值      |
| ------------------- | ----------------------------------------------------- | ------------ |
| `as`                | `AsTag \| Component`                                  | `"div"`      |
| `asChild`           | `boolean`                                             | -            |
| `by`                | `string \| ((a: AcceptableValue, b: AcceptableVal...` | -            |
| `defaultValue`      | `AcceptableValue \| AcceptableValue[]`                | -            |
| `dir`               | `"ltr" \| "rtl"`                                      | -            |
| `disabled`          | `boolean`                                             | -            |
| `highlightOnHover`  | `boolean`                                             | -            |
| `modelValue`        | `AcceptableValue \| AcceptableValue[]`                | -            |
| `multiple`          | `boolean`                                             | -            |
| `name`              | `string`                                              | -            |
| `orientation`       | `"vertical" \| "horizontal"`                          | `"vertical"` |
| `selectionBehavior` | `"toggle" \| "replace"`                               | `"toggle"`   |

### 事件

| 事件               | 载荷                                              |
| ------------------- | ---------------------------------------------------- |
| `entryFocus`        | `[event: CustomEvent<any>]`                          |
| `highlight`         | `[payload: { ref: HTMLElement; value: Acceptable...` |
| `leave`             | `[event: Event]`                                     |
| `update:modelValue` | `[value: AcceptableValue]`                           |

### 插槽

| 插槽         | 类型                                                |
| ------------ | --------------------------------------------------- |
| `modelValue` | `AcceptableValue \| AcceptableValue[] \| undefined` |

## ListboxContent

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## ListboxFilter

### 属性

| 属性         | 类型                 | 默认值   |
| ------------ | -------------------- | --------- |
| `as`         | `AsTag \| Component` | `"input"` |
| `asChild`    | `boolean`            | -         |
| `autoFocus`  | `boolean`            | -         |
| `modelValue` | `string`             | -         |

### 事件

| 事件               | 载荷    |
| ------------------- | ---------- |
| `update:modelValue` | `[string]` |

### 插槽

| 插槽         | 类型                  |
| ------------ | --------------------- |
| `modelValue` | `string \| undefined` |

## ListboxItem

### 属性

| 属性       | 类型                 | 默认值 |
| ---------- | -------------------- | ------- |
| `as`       | `AsTag \| Component` | `"div"` |
| `asChild`  | `boolean`            | -       |
| `disabled` | `boolean`            | -       |
| `value`\*  | `AcceptableValue`    | -       |

### 事件

| 事件    | 载荷                                 |
| -------- | --------------------------------------- |
| `select` | `[event: SelectEvent<AcceptableValue>]` |

## ListboxItemIndicator

### 属性

| 属性      | 类型                 | 默认值  |
| --------- | -------------------- | -------- |
| `as`      | `AsTag \| Component` | `"span"` |
| `asChild` | `boolean`            | -        |

## ListboxGroup

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## ListboxGroupLabel

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
| `for`     | `string`             | -       |

## ListboxVirtualizer

### 属性

| 属性           | 类型                                    | 默认值 |
| -------------- | --------------------------------------- | ------- |
| `estimateSize` | `number`                                | -       |
| `options`\*    | `AcceptableValue[]`                     | -       |
| `textContent`  | `((option: AcceptableValue) => string)` | -       |

### 插槽

| 插槽          | 类型                                                     |
| ------------- | -------------------------------------------------------- |
| `option`      | `string \| number \| false \| true \| Record<string,...` |
| `virtualizer` | `Virtualizer<Element \| Window, Element>`                |
| `virtualItem` | `VirtualItem<Element>`                                   |
