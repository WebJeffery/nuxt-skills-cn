# 标签页

一次显示一组标签面板。

> 基于 [Reka UI Tabs](https://reka-ui.com/docs/components/tabs)

## 关键属性

- `items`: 作为具有以下属性的对象数组：

- `label?: string`{lang="ts-type"}
- `icon?: string`{lang="ts-type"}
- `avatar?: AvatarProps`{lang="ts-type"}
- `badge?: string | number | BadgeProps`{lang="ts-type"}
- `content?: string`{lang="ts-type"}
- `value?: string | number`{lang="ts-type"}
- `disabled?: boolean`{lang="ts-type"}
- [`slot?: string`{lang="ts-type"}](#with-custom-slot)
- `class?: any`{lang="ts-type"}
- `ui?: { trigger?: ClassNameValue, leadingIcon?: ClassNameValue, leadingAvatar?: ClassNameValue, leadingAvatarSize?: ClassNameValue, label?: ClassNameValue, trailingBadge?: ClassNameValue, trailingBadgeSize?: ClassNameValue, content?: ClassNameValue }`{lang="ts-type"}

## ::component-code

ignore:

- items
- class
  external:
- items
  externalTypes:
- TabsItem[]
  props:
  items: - label: Account
  icon: 'i-lucide-user'
  content: 'This is account content.
- `color`: 更改标签页的颜色。
- `variant`: 更改标签页的变体。
- `size`: 更改标签页的大小。
- `orientation`: 更改标签页的方向。
- `slot`:

## 用法

```vue
<UTabs
  <!-- props here -->
/>
```

## 插槽

- `#content`
