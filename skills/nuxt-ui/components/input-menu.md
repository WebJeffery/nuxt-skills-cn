# 菜单输入框

具有实时建议的自动完成输入。

> 基于 [Reka UI InputMenu](https://reka-ui.com/docs/components/combobox)

## 关键属性

- `items`: 作为字符串、数字或布尔值的数组：

## ::component-code

prettier: true
ignore:

- modelValue
- items
  external:
- items
- modelValue
  props:
  modelValue: 'Backlog'
  items: - Backlog - Todo - In Progress - Done

---

::

您还可以传递具有以下属性的对象数组：

- `label?: string`{lang="ts-type"}
- [`type?: "label" | "separator" | "item"`{lang="ts-type"}](#with-items-type)
- [`icon?: string`{lang="ts-type"}](#with-icons-in-items)
- [`avatar?: AvatarProps`{lang="ts-type"}](#with-avatar-in-items)
- [`chip?: ChipProps`{lang="ts-type"}](#with-chip-in-items)
- `disabled?: boolean`{lang="ts-type"}
- `onSelect?: (e: Event) => void`{lang="ts-type"}
- `class?: any`{lang="ts-type"}
- `ui?: { tagsItem?: ClassNameValue, tagsItemText?: ClassNameValue, tagsItemDelete?: ClassNameValue, tagsItemDeleteIcon?: ClassNameValue, label?: ClassNameValue, separator?: ClassNameValue, item?: ClassNameValue, itemLeadingIcon?: ClassNameValue, itemLeadingAvatarSize?: ClassNameValue, itemLeadingAvatar?: ClassNameValue, itemLeadingChip?: ClassNameValue, itemLeadingChipSize?: ClassNameValue, itemLabel?: ClassNameValue, itemTrailing?: ClassNameValue, itemTrailingIcon?: ClassNameValue }`{lang="ts-type"}

## ::component-code

ignore:

- modelValue.
- `multiple`: 允许多选，所选项目将显示为标签。
- `placeholder`: 设置占位符文本。
- `content`: 控制菜单输入框内容的渲染方式，例如其 `align` 或 `side`。
- `arrow`: 在菜单输入框上显示箭头。
- `color`: 更改菜单输入框聚焦时的环颜色。
- `variant`: 更改菜单输入框的变体。
- `size`: 更改菜单输入框的大小。
- `icon`: 在菜单输入框内显示 [Icon](/docs/components/icon)。
- `avatar`: 在菜单输入框内显示 [Avatar](/docs/components/avatar)。

## 用法

```vue
<UInputMenu
  <!-- props here -->
/>
```

## 插槽

- `#leading`
