# 分页

页面导航

**部分：** `PaginationRoot`、`PaginationList`、`PaginationListItem`、`PaginationFirst`、`PaginationPrev`、`PaginationNext`、`PaginationLast`、`PaginationEllipsis`

## PaginationRoot

### 属性

| 属性           | 类型                 | 默认值 |
| -------------- | -------------------- | ------- |
| `as`           | `AsTag \| Component` | `"nav"` |
| `asChild`      | `boolean`            | -       |
| `defaultPage`  | `number`             | `1`     |
| `disabled`     | `boolean`            | -       |
| `itemsPerPage` | `number`             | `10`    |
| `page`         | `number`             | -       |
| `showEdges`    | `boolean`            | `false` |
| `siblingCount` | `number`             | `2`     |
| `total`        | `number`             | `0`     |

### 事件

| 事件         | 载荷           |
| ------------- | ----------------- |
| `update:page` | `[value: number]` |

### 插槽

| 插槽        | 类型     |
| ----------- | -------- |
| `page`      | `number` |
| `pageCount` | `number` |

## PaginationList

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

### 插槽

| 插槽    | 类型                                                  |
| ------- | ----------------------------------------------------- |
| `items` | `{ type: "ellipsis"; } \| { type: "page"; value: ...` |

## PaginationListItem

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |
| `value`\* | `number`             | -          |

## PaginationFirst

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |

## PaginationPrev

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |

## PaginationNext

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |

## PaginationLast

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |

## PaginationEllipsis

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
