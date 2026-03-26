# 编辑器工具栏

用于编辑器操作的可自定义工具栏，可以显示为固定、气泡或浮动菜单。

## 关键属性

- `items`: 作为具有以下属性的对象数组：

- `label?: string`{lang="ts-type"}
- `icon?: string`{lang="ts-type"}
- `color?: "error" | "primary" | "secondary" | "success" | "info" | "warning" | "neutral"`{lang="ts-type"}
- `activeColor?: "error" | "primary" | "secondary" | "success" | "info" | "warning" | "neutral"`{lang="ts-type"}
- `variant?: "solid" | "outline" | "soft" | "ghost" | "link" | "subtle"`{lang="ts-type"}
- `activeVariant?: "solid" | "outline" | "soft" | "ghost" | "link" | "subtle"`{lang="ts-type"}
- `size?: "xs" | "sm" | "md" | "lg" | "xl"`{lang="ts-type"}
- [`kind?: "mark" | "textAlign" | "heading" | "link" | "image" | "blockquote" | "bulletList" | "orderedList" | "codeBlock" | "horizontalRule" | "paragraph" | "undo" | "redo" | "clearFormatting" | "duplicate" | "delete" | "moveUp" | "moveDown" | "suggestion" | "mention" | "emoji"`{lang="ts-type"}](/docs/components/editor#handlers)
- `disabled?: boolean`{lang="ts-type"}
- `loading?: boolean`{lang="ts-type"}
- `active?: boolean`{lang="ts-type"}
- `tooltip?: TooltipProps`{lang="ts-type"}
- [`slot?: string`{lang="ts-type"}](#with-link-popover)
- `onClick?: (e: MouseEvent) => void`{lang="ts-type"}
- `items?: EditorToolbarItem[] | EditorToolbarItem[][]`{lang="ts-type"}
- `class?: any`{lang="ts-type"}

您可以传递 [Button](/docs/components/button#props) 组件的任何属性，例如 `color`、`variant`、`size` 等。

- `layout`: 更改工具栏的显示方式。

## 用法

```vue
<UEditorToolbar
  <!-- props here -->
/>
```
