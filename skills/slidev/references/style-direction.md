---
name: direction
description: 基于导航方向的样式
---

# 导航方向变体

根据导航方向（前进/后退）应用不同的样式。

## CSS 类

```css
/* 仅在前进导航时延迟 */
.slidev-nav-go-forward .slidev-vclick-target {
  transition-delay: 500ms;
}
.slidev-nav-go-backward .slidev-vclick-target {
  transition-delay: 0;
}
```

## UnoCSS 变体

使用 `forward:` 或 `backward:` 前缀：

```html
<div v-click class="transition forward:delay-300">元素</div>
```

动画仅在前进导航时延迟，后退时不会。

## 使用场景

创建不对称动画，使进入幻灯片的感觉与离开时不同。
