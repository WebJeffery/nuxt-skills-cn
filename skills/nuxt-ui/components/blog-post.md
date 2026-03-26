# 博客文章

可自定义的文章，用于在博客页面中显示。

## 关键属性

- `title`: 显示博客文章的标题。
- `description`: 显示博客文章的描述。
- `date`: 显示博客文章的日期。
- `badge`: 在博客文章中显示 [Badge](/docs/components/badge)。
- `image`: 在博客文章中显示图像。
- `authors`: 在博客文章中显示 [User](/docs/components/user) 列表，作为具有以下属性的对象数组：

- `name?: string`{lang="ts-type"}
- `description?: string`{lang="ts-type"}
- `avatar?: Omit<AvatarProps, 'size'>`{lang="ts-type"}
- `chip?: boolean | Omit<ChipProps, 'size' | 'inset'>`{lang="ts-type"}
- `size?: UserProps['size']`{lang="ts-type"}
- `orientation?: UserProps['orientation']`{lang="ts-type"}

您可以传递 [Link](/docs/components/link#props) 组件的任何属性，例如 `to`、`target` 等。

- `variant`: 更改博客文章的样式。
- `orientation`: 更改博客文章的方向。

## 用法

```vue
<UBlogPost
  <!-- props here -->
/>
```
