# 编辑器提及菜单

提及菜单，在编辑器中输入 @ 字符时显示用户建议。

## 关键属性

- `items`: 作为具有以下属性的对象数组：

- `label: string`{lang="ts-type"}
- `avatar?: AvatarProps`{lang="ts-type"}
- `icon?: string`{lang="ts-type"}
- `description?: string`{lang="ts-type"}
- `disabled?: boolean`{lang="ts-type"}

## ::component-example

elevated: true
collapse: true
name: 'editor-mention-menu-items-example'
class: 'p-8'

---

::

::note
您还可以将数组数组传递给 `items` 属性以创建分隔的项目组。

- `char`: 更改触发字符。
- `options`: 使用 [Floating UI 选项](https://floating-ui. 自定义定位行为。

## 用法

```vue
<UEditorMentionMenu
  <!-- props here -->
/>
```
