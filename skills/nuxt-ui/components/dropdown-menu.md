# 下拉菜单

在单击元素时显示操作的菜单。

> 基于 [Reka UI DropdownMenu](https://reka-ui.com/docs/components/dropdown-menu)

## 关键属性

- `items`: 作为具有以下属性的对象数组：

- `label?: string`{lang="ts-type"}
- `icon?: string`{lang="ts-type"}
- `avatar?: AvatarProps`{lang="ts-type"}
- `kbds?: string[] | KbdProps[]`{lang="ts-type"}
- [`type?: "link" | "label" | "separator" | "checkbox"`{lang="ts-type"}](#with-checkbox-items)
- [`color?: "error" | "primary" | "secondary" | "success" | "info" | "warning" | "neutral"`{lang="ts-type"}](#with-color-items)
- [`checked?: boolean`{lang="ts-type"}](#with-checkbox-items)
- `disabled?: boolean`{lang="ts-type"}
- [`slot?: string`{lang="ts-type"}](#with-custom-slot)
- `onSelect?: (e: Event) => void`{lang="ts-type"}
- [`onUpdateChecked?: (checked: boolean) => void`{lang="ts-type"}](#with-checkbox-items)
- `children?: DropdownMenuItem[] | DropdownMenuItem[][]`{lang="ts-type"}
- `class?: any`{lang="ts-type"}
- `ui?: { item?: ClassNameValue, label?: ClassNameValue, separator?: ClassNameValue, itemLeadingIcon?: ClassNameValue, itemLeadingAvatarSize?: ClassNameValue, itemLeadingAvatar?: ClassNameValue, itemLabel?: ClassNameValue, itemLabelExternalIcon?: ClassNameValue, itemTrailing?: ClassNameValue, itemTrailingIcon?: ClassNameValue, itemTrailingKbds?: ClassNameValue, itemTrailingKbdsSize?: ClassNameValue }`{lang="ts-type"}

您可以传递 [Link](/docs/components/link#props) 组件的任何属性，例如 `to`、`target` 等。

- `content`: 控制下拉菜单内容的渲染方式，例如其 `align` 或 `side`。
- `arrow`: 在下拉菜单上显示箭头。
- `size`: 控制下拉菜单的大小。
- `modal`: 控制下拉菜单是否阻止与外部内容的交互。
- `disabled`: 禁用下拉菜单。
- `slot`:

## 用法

```vue
<UDropdownMenu
  <!-- props here -->
/>
```

## 插槽

- `#item`
