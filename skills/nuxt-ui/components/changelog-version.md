# 更新日志版本

可自定义的文章，用于在更新日志中显示。

## 关键属性

- `title`: 显示更新日志版本的标题。
- `description`: 显示更新日志版本的描述。
- `date`: 显示更新日志版本的日期。
- `badge`: 在更新日志版本上显示 [Badge](/docs/components/badge)。
- `image`: 在博客文章中显示图像。
- `authors`: 在更新日志版本中显示 [User](/docs/components/user) 列表，作为具有以下属性的对象数组：

- `name?: string`{lang="ts-type"}
- `description?: string`{lang="ts-type"}
- `avatar?: Omit<AvatarProps, 'size'>`{lang="ts-type"}
- `chip?: boolean | Omit<ChipProps, 'size' | 'inset'>`{lang="ts-type"}
- `size?: UserProps['size']`{lang="ts-type"}
- `orientation?: UserProps['orientation']`{lang="ts-type"}

您可以传递 [Link](/docs/components/link#props) 组件的任何属性，例如 `to`、`target` 等。

- `indicator`: 隐藏左侧的指示器点。

## 用法

```vue
<UChangelogVersion
  <!-- props here -->
/>
```
