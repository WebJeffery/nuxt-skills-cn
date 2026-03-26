# 页面部分

用于您的页面的响应式部分。

## 关键属性

- `title`: 设置部分的标题。
- `description`: 设置部分的描述。
- `headline`: 设置部分的标题行。
- `icon`: 设置部分的图标。
- `features`: 在描述下方显示 [PageFeature](/docs/components/page-feature) 列表，作为具有以下属性的对象数组：

- `title?: string`{lang="ts-type"}
- `description?: string`{lang="ts-type"}
- `icon?: string`{lang="ts-type"}
- `orientation?: 'horizontal' | 'vertical'`{lang="ts-type"}

您可以传递 [Link](/docs/components/link#props) 组件的任何属性，例如 `to`、`target` 等。

- `links`: 在描述下方显示 [Button](/docs/components/button) 列表。
- `orientation`: 更改默认插槽的方向。
- `reverse`: 反转默认插槽的方向。

## 用法

```vue
<UPageSection
  <!-- props here -->
/>
```
