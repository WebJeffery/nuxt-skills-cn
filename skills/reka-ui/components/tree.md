# 树

分层树视图

**部分：** `TreeRoot`、`TreeItem`、`TreeVirtualizer`

## TreeRoot

### 属性

| 属性                | 类型                                                 | 默认值        |
| ------------------- | ---------------------------------------------------- | -------------- |
| `as`                | `AsTag \| Component`                                 | `"ul"`         |
| `asChild`           | `boolean`                                            | -              |
| `defaultExpanded`   | `string[]`                                           | -              |
| `defaultValue`      | `Record<string, any> \| Record<string, any>[]`       | -              |
| `dir`               | `"ltr" \| "rtl"`                                     | -              |
| `disabled`          | `boolean`                                            | -              |
| `expanded`          | `string[]`                                           | -              |
| `getChildren`       | `((val: Record<string, any>) => Record<string, a...` | `val.children` |
| `getKey`\*          | `(val: Record<string, any>) => string`               | -              |
| `items`             | `Record<string, any>[]`                              | -              |
| `modelValue`        | `Record<string, any> \| Record<string, any>[]`       | -              |
| `multiple`          | `boolean`                                            | -              |
| `propagateSelect`   | `boolean`                                            | -              |
| `selectionBehavior` | `"toggle" \| "replace"`                              | `"toggle"`     |

### 事件

| 事件               | 载荷                      |
| ------------------- | ---------------------------- |
| `update:expanded`   | `[val: string[]]`            |
| `update:modelValue` | `[val: Record<string, any>]` |

### 插槽

| 插槽           | 类型                                           |
| -------------- | ---------------------------------------------- |
| `flattenItems` | `FlattenedItem<Record<string, any>>[]`         |
| `modelValue`   | `Record<string, any> \| Record<string, any>[]` |
| `expanded`     | `string[]`                                     |

## TreeItem

### 属性

| 属性      | 类型                  | 默认值 |
| --------- | --------------------- | ------- |
| `as`      | `AsTag \| Component`  | `"li"`  |
| `asChild` | `boolean`             | -       |
| `level`\* | `number`              | -       |
| `value`\* | `Record<string, any>` | -       |

### 事件

| 事件    | 载荷                                     |
| -------- | ------------------------------------------- |
| `select` | `[event: SelectEvent<Record<string, any>>]` |
| `toggle` | `[event: ToggleEvent<Record<string, any>>]` |

### 插槽

| 插槽              | 类型                   |
| ----------------- | ---------------------- |
| `isExpanded`      | `boolean`              |
| `isSelected`      | `boolean`              |
| `isIndeterminate` | `boolean \| undefined` |
| `handleToggle`    | ``                     |
| `handleSelect`    | ``                     |

## TreeVirtualizer

### 属性

| 属性           | 类型                                      | 默认值 |
| -------------- | ----------------------------------------- | ------- |
| `estimateSize` | `number`                                  | -       |
| `textContent`  | `((item: Record<string, any>) => string)` | -       |

### 插槽

| 插槽          | 类型                                      |
| ------------- | ----------------------------------------- |
| `item`        | `FlattenedItem<Record<string, any>>`      |
| `virtualizer` | `Virtualizer<Element \| Window, Element>` |
| `virtualItem` | `VirtualItem<Element>`                    |
