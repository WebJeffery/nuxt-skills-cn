# 轮播图

使用 Embla 构建的具有动画和滑动的轮播图。

## 关键属性

- `items`: 作为数组，使用默认插槽渲染每个项目：

## ::component-example

name: 'carousel-items-example'
class: 'p-8'

---

::

您还可以传递具有以下属性的对象数组：

- `class?: any`{lang="ts-type"}
- `ui?: { item?: ClassNameValue }`{lang="ts-type"}

您可以使用 [`basis`](https://tailwindcss. 控制可见的项目数量。

- `orientation`: 更改进度的方向。
- `arrows`: 显示上一个和下一个按钮。
- `dots`: 显示点列表以滚动到特定幻灯片。
- `autoplay`: 作为布尔值或对象来配置 [Autoplay 插件](https://www.
- `fade`: 作为布尔值或对象来配置 [Fade 插件](https://www.

## 用法

```vue
<UCarousel
  <!-- props here -->
/>
```
