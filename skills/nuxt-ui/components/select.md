# 选择器

用于从选项列表中进行选择的元素。

> 基于 [Reka UI Select](https://reka-ui.com/docs/components/select)

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
- [`value?: string`{lang="ts-type"}](#value-key)
- [`type?: "label" | "separator" | "item"`{lang="ts-type"}](#with-items-type)
- [`icon?: string`{lang="ts-type"}](#with-icons-in-items)
- [`avatar?: AvatarProps`{lang="ts-type"}](#with-avatar-in-items)
- [`chip?: ChipProps`{lang="ts-type"}](#with-chip-in-items)
- `disabled?: boolean`{lang="ts-type"}
- `class?: any`{lang="ts-type"}
- `ui?: { label?: ClassNameValue, separator?: ClassNameValue, item?: ClassNameValue, itemLeadingIcon?: ClassNameValue, itemLeadingAvatarSize?: ClassNameValue, itemLeadingAvatar?: ClassNameValue, itemLeadingChipSize?: ClassNameValue, itemLeadingChip?: ClassNameValue, itemLabel?: ClassNameValue, itemTrailing?: ClassNameValue, itemTrailingIcon?: ClassNameValue }`{lang="ts-type"}

## ::component-code

ignore:

- modelValue
- items
- class
  external:
- items
- modelValue
  externalTypes:
- SelectItem[]
  props:
  modelValue: 'backlog'
  items: - label: 'Backlog'
  value: 'backlog' - label: 'Todo'
  value: 'todo' - label: 'In Progress'
  value: 'in_progress' - label: 'Done'
  value: 'done'
  class: 'w-48'

---

::

::caution
使用对象时，您需要在 `v-model` 指令或 `default-value` 属性中引用对象的 `value` 属性。

- `multiple`: 允许多选，所选项目将在触发器中用逗号分隔。
- `placeholder`: 设置占位符文本。
- `content`: 控制选择器内容的渲染方式，例如其 `align` 或 `side`。
- `arrow`: 在选择器上显示箭头。
- `color`: 更改选择器聚焦时的环颜色。
- `variant`: 更改选择器的变体。
- `size`: 更改选择器的大小。
- `icon`: 在选择器内显示 [Icon](/docs/components/icon)。
- `avatar`: 在选择器内显示 [Avatar](/docs/components/avatar)。

## 用法

```vue
<USelect
  <!-- props here -->
/>
```

## 插槽

- `#leading`
