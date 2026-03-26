# 复选框组

一组复选框按钮，用于从列表中选择多个选项。

> 基于 [Reka UI CheckboxGroup](https://reka-ui.com/docs/components/checkbox#group-root)

## 关键属性

- `items`: 作为字符串或数字的数组：

## ::component-code

prettier: true
ignore:

- modelValue
- items
  external:
- items
- modelValue
  props:
  modelValue: - 'System'
  items: - 'System' - 'Light' - 'Dark'

---

::

您还可以传递具有以下属性的对象数组：

- `label?: string`{lang="ts-type"}
- `description?: string`{lang="ts-type"}
- [`value?: string`{lang="ts-type"}](#value-key)
- `disabled?: boolean`{lang="ts-type"}
- `class?: any`{lang="ts-type"}
- `ui?: { item?: ClassNameValue, container?: ClassNameValue, base?: ClassNameValue, 'indicator'?: ClassNameValue, icon?: ClassNameValue, wrapper?: ClassNameValue, label?: ClassNameValue, description?: ClassNameValue }`{lang="ts-type"}

## ::component-code

ignore:

- modelValue
- items
  external:
- items
- modelValue
  externalTypes:
- CheckboxGroupItem[]
  props:
  modelValue: - 'system'
  items: - label: 'System'
  description: 'This is first option.
- `legend`: 设置复选框组的图例。
- `color`: 更改复选框组的颜色。
- `variant`: 更改复选框组的变体。
- `size`: 更改复选框组的大小。
- `orientation`: 更改复选框组的方向。
- `indicator`: 更改位置或隐藏指示器。
- `disabled`: 禁用复选框组。

## 用法

```vue
<UCheckboxGroup
  <!-- props here -->
/>
```
