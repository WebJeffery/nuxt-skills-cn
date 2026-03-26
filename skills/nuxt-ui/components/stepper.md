# 步骤条

一组用于指示多步骤过程进度的步骤。

> 基于 [Reka UI Stepper](https://reka-ui.com/docs/components/stepper)

## 关键属性

- `items`: 作为具有以下属性的对象数组：

- `title?: string`{lang="ts-type"}
- `description?: AvatarProps`{lang="ts-type"}
- `content?: string`{lang="ts-type"}
- `icon?: string`{lang="ts-type"}
- `value?: string | number`{lang="ts-type"}
- `disabled?: boolean`{lang="ts-type"}
- [`slot?: string`{lang="ts-type"}](#with-custom-slot)
- `class?: any`{lang="ts-type"}
- `ui?: { item?: ClassNameValue, container?: ClassNameValue, trigger?: ClassNameValue, indicator?: ClassNameValue, icon?: ClassNameValue, separator?: ClassNameValue, wrapper?: ClassNameValue, title?: ClassNameValue, description?: ClassNameValue }`{lang="ts-type"}

## ::component-code

ignore:

- items
- class
  external:
- items
  externalTypes:
- StepperItem[]
  props:
  items: - title: 'Address'
  description: 'Add your address here'
  icon: 'i-lucide-house' - title: 'Shipping'
  description: 'Set your preferred shipping method'
  icon: 'i-lucide-truck' - title: 'Checkout'
  description: 'Confirm your order'
  class: 'w-full'

---

::

::note
单击项目以在步骤之间导航。

- `color`: 更改步骤条的颜色。
- `size`: 更改步骤条的大小。
- `orientation`: 更改步骤条的方向。
- `disabled`: 禁用步骤之间的导航。
- `slot`:

## 用法

```vue
<UStepper
  <!-- props here -->
/>
```

## 插槽

- `#content`
