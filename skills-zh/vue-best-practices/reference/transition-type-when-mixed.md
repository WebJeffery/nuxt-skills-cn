---
title: 混合 CSS 过渡和动画时指定过渡类型
impact: MEDIUM
impactDescription: 当同时应用 CSS 过渡和动画时，Vue 可能检测到错误的过渡结束事件，导致时序问题
type: gotcha
tags: [vue3, transition, animation, css, type, timing]
---

# 混合 CSS 过渡和动画时指定过渡类型

**影响：中等** - 当您对同一元素应用 CSS 过渡和 CSS 动画时（例如，Vue 触发的动画与悬停过渡效果结合），Vue 无法自动确定要监听哪个结束事件。您必须使用值为 `"animation"` 或 `"transition"` 的 `type` 属性显式告诉 Vue 优先考虑哪种类型。

## 任务清单

- [ ] 检查您的元素是否同时具有 `transition` 和 `animation` CSS 属性
- [ ] 确定哪个时序应该控制 Vue 何时认为过渡完成
- [ ] 向 `<Transition>` 组件添加 `type="animation"` 或 `type="transition"`
- [ ] 类型应该匹配哪个动画/过渡更长或更重要

**有问题的代码：**
```vue
<template>
  <!-- 错误：同时存在过渡和动画，Vue 可能选择错误的结束事件 -->
  <Transition name="bounce">
    <div v-if="show" class="box">
      悬停我以获得额外效果
    </div>
  </Transition>
</template>

<style>
/* Vue 触发的 CSS 动画 */
.bounce-enter-active {
  animation: bounce-in 0.5s;
}

.bounce-leave-active {
  animation: bounce-out 0.3s;
}

@keyframes bounce-in {
  0% { transform: scale(0); }
  50% { transform: scale(1.2); }
  100% { transform: scale(1); }
}

@keyframes bounce-out {
  0% { transform: scale(1); }
  100% { transform: scale(0); }
}

/* 同一元素上的额外悬停过渡 */
.box {
  transition: background-color 0.2s ease, box-shadow 0.2s ease;
}

.box:hover {
  background-color: #f0f0f0;
  box-shadow: 0 4px 8px rgba(0,0,0,0.1);
}
</style>
```

**正确的代码：**
```vue
<template>
  <!-- 好：显式指定动画控制时序 -->
  <Transition name="bounce" type="animation">
    <div v-if="show" class="box">
      悬停我以获得额外效果
    </div>
  </Transition>
</template>

<style>
/* Vue 触发的 CSS 动画 - 这是我们关心的 */
.bounce-enter-active {
  animation: bounce-in 0.5s;
}

.bounce-leave-active {
  animation: bounce-out 0.3s;
}

@keyframes bounce-in {
  0% { transform: scale(0); }
  50% { transform: scale(1.2); }
  100% { transform: scale(1); }
}

@keyframes bounce-out {
  0% { transform: scale(1); }
  100% { transform: scale(0); }
}

/* 额外的悬停过渡 - 与 Vue 过渡时序无关 */
.box {
  transition: background-color 0.2s ease, box-shadow 0.2s ease;
}

.box:hover {
  background-color: #f0f0f0;
  box-shadow: 0 4px 8px rgba(0,0,0,0.1);
}
</style>
```

## 何时使用每种类型

### 使用 `type="animation"` 当：
- 您的进入/离开效果使用 `@keyframes` 动画
- 动画比任何过渡都长
- 您想要精确控制多步动画

```vue
<Transition name="fancy" type="animation">
  <div v-if="show" class="animated-element" />
</Transition>

<style>
.fancy-enter-active {
  animation: fancy-entrance 1s ease-out;
}

.animated-element {
  /* 这个较短的过渡不应影响时序 */
  transition: color 0.2s;
}
</style>
```

### 使用 `type="transition"` 当：
- 您的进入/离开效果使用 CSS `transition` 属性
- 您有不影响时序的装饰性动画

```vue
<Transition name="slide" type="transition">
  <div v-if="show" class="sliding-element" />
</Transition>

<style>
.slide-enter-active,
.slide-leave-active {
  transition: transform 0.3s ease;
}

.slide-enter-from,
.slide-leave-to {
  transform: translateX(-100%);
}

/* 装饰性无限动画不应影响时序 */
.sliding-element {
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.8; }
}
</style>
```

## 不指定类型时的常见症状

1. 过渡结束太早（元素跳转到最终位置）
2. 过渡挂起或需要太长时间完成
3. 元素在动画完成前消失
4. 过渡完成后 CSS 类仍然应用

## 参考
- [Vue.js Transition Documentation](https://vuejs.org/guide/built-ins/transition.html#css-based-transitions)
