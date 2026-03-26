# 时间轴

显示带有日期、标题、图标或头像的事件序列的组件。

## 关键属性

- `items`: 作为具有以下属性的对象数组：

- `date?: string`{lang="ts-type"}
- `title?: string`{lang="ts-type"}
- `description?: AvatarProps`{lang="ts-type"}
- `icon?: string`{lang="ts-type"}
- `avatar?: AvatarProps`{lang="ts-type"}
- `value?: string | number`{lang="ts-type"}
- [`slot?: string`{lang="ts-type"}](#with-custom-slot)
- `class?: any`{lang="ts-type"}
- `ui?: { item?: ClassNameValue, container?: ClassNameValue, indicator?: ClassNameValue, separator?: ClassNameValue, wrapper?: ClassNameValue, date?: ClassNameValue, title?: ClassNameValue, description?: ClassNameValue }`{lang="ts-type"}

## ::component-code

ignore:

- items
- class
- defaultValue
  external:
- items
  externalTypes:
- TimelineItem[]
  props:
  defaultValue: 2
  items: - date: 'Mar 15, 2025'
  title: 'Project Kickoff'
  description: 'Kicked off project with team alignment.
- `color`: 更改时间轴中活动项目的颜色。
- `size`: 更改时间轴的大小。
- `orientation`: 更改时间轴的方向。
- `ui`: 创建具有交替布局的时间轴。
- `slot`:

## 事件

- `@select`: 选择项目时发出（v4.4+）

## 用法

```vue
<UTimeline
  :items="items"
  @select="handleSelect"
/>
```
