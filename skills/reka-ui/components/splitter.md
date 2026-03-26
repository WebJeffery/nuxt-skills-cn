# 分割器

可调整大小的分割面板

**部分：** `SplitterGroup`、`SplitterPanel`、`SplitterResizeHandle`

## SplitterGroup

### 属性

| 属性               | 类型                         | 默认值          |
| ------------------ | ---------------------------- | ---------------- |
| `as`               | `AsTag \| Component`         | `"div"`          |
| `asChild`          | `boolean`                    | -                |
| `autoSaveId`       | `string \| null`             | `null`           |
| `direction`\*      | `"vertical" \| "horizontal"` | -                |
| `id`               | `string \| null`             | -                |
| `keyboardResizeBy` | `number \| null`             | `10`             |
| `storage`          | `PanelGroupStorage`          | `defaultStorage` |

### 事件

| 事件    | 载荷           |
| -------- | ----------------- |
| `layout` | `[val: number[]]` |

### 插槽

| 插槽     | 类型       |
| -------- | ---------- |
| `layout` | `number[]` |

## SplitterPanel

### 属性

| 属性            | 类型                 | 默认值 |
| --------------- | -------------------- | ------- |
| `as`            | `AsTag \| Component` | `"div"` |
| `asChild`       | `boolean`            | -       |
| `collapsedSize` | `number`             | -       |
| `collapsible`   | `boolean`            | -       |
| `defaultSize`   | `number`             | -       |
| `id`            | `string`             | -       |
| `maxSize`       | `number`             | -       |
| `minSize`       | `number`             | -       |
| `order`         | `number`             | -       |

### 事件

| 事件      | 载荷                            |
| ---------- | ---------------------------------- |
| `collapse` | `[]`                               |
| `expand`   | `[]`                               |
| `resize`   | `[size: number, prevSize: number]` |

### 插槽

| 插槽          | 类型      |
| ------------- | --------- |
| `isCollapsed` | `boolean` |
| `isExpanded`  | `boolean` |

## SplitterResizeHandle

### 属性

| 属性             | 类型                    | 默认值 |
| ---------------- | ----------------------- | ------- |
| `as`             | `AsTag \| Component`    | `"div"` |
| `asChild`        | `boolean`               | -       |
| `disabled`       | `boolean`               | -       |
| `hitAreaMargins` | `PointerHitAreaMargins` | -       |
| `id`             | `string`                | -       |
| `tabindex`       | `number`                | `0`     |

### 事件

| 事件      | 载荷                 |
| ---------- | ----------------------- |
| `dragging` | `[isDragging: boolean]` |
