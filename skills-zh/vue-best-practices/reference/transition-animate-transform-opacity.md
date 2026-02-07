---
title: 为性能优先动画转换和不透明度
impact: MEDIUM
impactDescription: 动画触发布局的属性（如 height 或 margin）会导致昂贵的重排和卡顿的动画
type: best-practice
tags: [vue3, transition, animation, performance, css, transform, opacity]
---

# 为性能优先动画转换和不透明度

**影响: MEDIUM** - 创建 Vue 过渡时，你应该优先动画 `transform` 和 `opacity` 属性。这些是 GPU 加速的，不会触发昂贵的 CSS 布局重新计算。动画 `height`、`width`、`margin`、`padding` 或 `top`/`left` 等属性会导致浏览器在每一帧重新计算布局，导致卡顿、低性能的动画。

## 任务清单

- [ ] 使用 `transform` 进行位置和大小动画（translate、scale、rotate）
- [ ] 使用 `opacity` 进行淡入淡出效果
- [ ] 避免动画 `height`、`width`、`margin`、`padding`、`top`、`left`、`right`、`bottom`
- [ ] 如果必须动画高度，考虑使用带有慷慨值的 `max-height` 或基于 JavaScript 的解决方案
- [ ] 谨慎使用 `will-change`，仅在需要时使用

**有问题的代码:**
```css
/* 不好: 动画高度每帧触发布局重新计算 */
.slide-enter-active,
.slide-leave-active {
  transition: height 0.3s ease;
}

.slide-enter-from,
.slide-leave-to {
  height: 0;
}

.slide-enter-to,
.slide-leave-from {
  height: 200px;
}
```

```css
/* 不好: 动画 margin 导致布局抖动 */
.slide-enter-active,
.slide-leave-active {
  transition: margin-top 0.3s ease;
}

.slide-enter-from,
.slide-leave-to {
  margin-top: -100%;
}
```

**正确的代码:**
```css
/* 好: 使用 transform 和 opacity - GPU 加速 */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
```

```css
/* 好: 使用 transform 进行滑动动画 */
.slide-enter-active,
.slide-leave-active {
  transition: transform 0.3s ease, opacity 0.3s ease;
}

.slide-enter-from {
  transform: translateX(-100%);
  opacity: 0;
}

.slide-leave-to {
  transform: translateX(100%);
  opacity: 0;
}
```

```css
/* 好: 使用 scale 而不是 width/height */
.scale-enter-active,
.scale-leave-active {
  transition: transform 0.3s ease, opacity 0.3s ease;
}

.scale-enter-from,
.scale-leave-to {
  transform: scale(0);
  opacity: 0;
}
```

## 当你必须动画高度时

如果你绝对需要动画高度（例如，手风琴），考虑这些替代方案:

```vue
<template>
  <!-- 选项 1: max-height 技巧（简单但不精确） -->
  <Transition name="expand">
    <div v-if="expanded" class="content">
      <slot />
    </div>
  </Transition>
</template>

<style>
.expand-enter-active,
.expand-leave-active {
  transition: max-height 0.3s ease, opacity 0.3s ease;
  overflow: hidden;
}

.expand-enter-from,
.expand-leave-to {
  max-height: 0;
  opacity: 0;
}

.expand-enter-to,
.expand-leave-from {
  max-height: 500px; /* 设置为预期的最大内容高度 */
  opacity: 1;
}
</style>
```

```vue
<script setup>
// 选项 2: JavaScript 钩子用于精确的高度动画
function onEnter(el, done) {
  el.style.height = '0'
  el.style.overflow = 'hidden'
  // 强制重排
  el.offsetHeight
  el.style.transition = 'height 0.3s ease'
  el.style.height = el.scrollHeight + 'px'

  el.addEventListener('transitionend', () => {
    el.style.height = ''
    el.style.overflow = ''
    done()
  }, { once: true })
}

function onLeave(el, done) {
  el.style.height = el.scrollHeight + 'px'
  el.style.overflow = 'hidden'
  el.offsetHeight
  el.style.transition = 'height 0.3s ease'
  el.style.height = '0'

  el.addEventListener('transitionend', done, { once: true })
}
</script>

<template>
  <Transition @enter="onEnter" @leave="onLeave">
    <div v-if="expanded">
      <slot />
    </div>
  </Transition>
</template>
```

## 性能比较

| 属性 | 布局 | 绘制 | 合成 | 性能 |
|----------|--------|-------|-----------|-------------|
| `transform` | 否 | 否 | 是 | 优秀 |
| `opacity` | 否 | 否 | 是 | 优秀 |
| `background-color` | 否 | 是 | 是 | 良好 |
| `width`/`height` | 是 | 是 | 是 | 差 |
| `margin`/`padding` | 是 | 是 | 是 | 差 |
| `top`/`left` | 是 | 是 | 是 | 差 |

## 参考
- [Vue.js 过渡文档](https://vuejs.org/guide/built-ins/transition.html)
- [CSS Triggers](https://csstriggers.com/) - 哪些属性触发布局/绘制的参考
- [高性能动画](https://web.dev/animations-guide/)
