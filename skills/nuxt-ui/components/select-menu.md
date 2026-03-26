# 选择菜单

高级可搜索选择元素。

> 基于 [Reka UI SelectMenu](https://reka-ui.com/docs/components/combobox)

## 关键属性

- `items`: 作为字符串、数字或布尔值的数组：

## ::component-code

prettier: true
ignore:

- modelValue
- items
- class
  external:
- items
- modelValue
  props:
  modelValue: 'Backlog'
  items: - Backlog - Todo - In Progress - Done
  class: 'w-48'

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
- `ui?: { label?: ClassNameValue, separator?: ClassNameValue, item?: ClassNameValue, itemLeadingIcon?: ClassNameValue, itemLeadingAvatarSize?: ClassNameValue, itemLeadingAvatar?: ClassNameValue, itemLeadingChipSize?: ClassNameValue, itemLeadingChip?: ClassNameValue, itemLabel?: ClassNameValue, itemTrailing?: ClassNameValue, itemTrailingIcon?: ClassNameValue }`{lang="ts-type"}

## ::component-code

ignore:

- modelValue.
- `multiple`: 允许多选，所选项目将在触发器中用逗号分隔。
- `placeholder`: 设置占位符文本。
- `content`: 控制选择菜单内容的渲染方式，例如其 `align` 或 `side`。
- `arrow`: 在选择菜单上显示箭头。
- `color`: 更改选择菜单聚焦时的环颜色。
- `variant`: 更改选择菜单的变体。
- `size`: 更改选择菜单的大小。
- `icon`: 在选择菜单内显示 [Icon](/docs/components/icon)。
- `avatar`: 在选择菜单内显示 [Avatar](/docs/components/avatar)。

## 用法

```vue
<USelectMenu
  <!-- props here -->
/>
```

## 插槽

- `#leading`
