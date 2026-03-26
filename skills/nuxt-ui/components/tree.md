# 树

树视图组件，用于显示和交互层次数据结构。

> 基于 [Reka UI Tree](https://reka-ui.com/docs/components/tree)

## 关键属性

- `items`: 作为具有以下属性的对象数组：

- `icon?: string`{lang="ts-type"}
- `label?: string`{lang="ts-type"}
- `trailingIcon?: string`{lang="ts-type"}
- `defaultExpanded?: boolean`{lang="ts-type"}
- `disabled?: boolean`{lang="ts-type"}
- `slot?: string`{lang="ts-type"}
- `children?: TreeItem[]`{lang="ts-type"}
- `onToggle?: (e: TreeItemToggleEvent<TreeItem>) => void`{lang="ts-type"}
- `onSelect?: (e: TreeItemSelectEvent<TreeItem>) => void`{lang="ts-type"}
- `class?: any`{lang="ts-type"}
- `ui?: { item?: ClassNameValue, itemWithChildren?: ClassNameValue, link?: ClassNameValue, linkLeadingIcon?: ClassNameValue, linkLabel?: ClassNameValue, linkTrailing?: ClassNameValue, linkTrailingIcon?: ClassNameValue, listWithChildren?: ClassNameValue }`{lang="ts-type"}

::note
每个项目都需要唯一标识符。

- `multiple`: 允许多个项目选择。
- `nested`: 控制树是否以嵌套结构或平面列表形式呈现。
- `color`: 更改树的颜色。
- `size`: 更改树的大小。
- `disabled`: 防止任何与树的用户交互。
- `virtualize`: 为大型列表启用虚拟化，作为布尔值或带有选项的对象，如 `{ estimateSize: 32, overscan: 12 }`。
- `slot`:

## 用法

```vue
<UTree
  <!-- props here -->
/>
```
