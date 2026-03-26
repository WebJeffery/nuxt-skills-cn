# 页面链接

要在页面中显示的链接列表。

## 关键属性

- `links`: 作为具有以下属性的对象数组：

- `label: string`{lang="ts-type"}
- `icon?: string`{lang="ts-type"}
- `class?: any`{lang="ts-type"}
- `ui?: { item?: ClassNameValue, link?: ClassNameValue, linkLabel?: ClassNameValue, linkLabelExternalIcon?: ClassNameValue, linkLeadingIcon?: ClassNameValue }`{lang="ts-type"}

您可以传递 [Link](/docs/components/link#props) 组件的任何属性，例如 `to`、`target` 等。

- `title`: 在链接上方显示标题。

## 用法

```vue
<UPageLinks
  <!-- props here -->
/>
```
