---
title: JavaScript 过渡钩子需要带有 css="false" 的 done() 回调
impact: HIGH
impactDescription: 不调用 done()，仅 JavaScript 的过渡立即完成，完全跳过动画
type: gotcha
tags: [vue3, transition, javascript, animation, hooks, gsap, done-callback]
---

# JavaScript 过渡钩子需要带有 css="false" 的 done() 回调

**影响：高** - 使用仅 JavaScript 过渡（带有 `:css="false"`）时，`@enter` 和 `@leave` 钩子**必须**调用 `done()` 回调以在动画完成时发出信号。如果不调用 `done()`，Vue 认为过渡立即完成，导致元素出现/消失而没有动画。

当使用 GSAP、Anime.js 或 Web Animations API 等动画库时，这尤其重要。

## 任务清单

- [ ] 使用 `:css="false"` 时，始终在 `@enter` 和 `@leave` 钩子中调用 `done()`
- [ ] 在 JavaScript 动画完成时调用 `done()`（在 `onComplete` 回调中）
- [ ] 记住：当 CSS 处理过渡时，`done()` 是可选的，但使用 `:css="false"` 时**必需**
- [ ] 使用 `:css="false"` 防止 CSS 规则干扰 JS 动画

**有问题的代码：**
```vue
<template>
  <!-- 错误：没有 done() 回调 - 动画被跳过！ -->
  <Transition :css="false" @enter="onEnter" @leave="onLeave">
    <div v-if="show" class="box">内容</div>
  </Transition>
</template>

<script setup>
import gsap from 'gsap'

function onEnter(el) {
  // 动画开始但 Vue 不等待它！
  gsap.from(el, {
    opacity: 0,
    y: 50,
    duration: 0.5
  })
  // 缺少 done() 调用 - 元素没有动画出现
}

function onLeave(el) {
  gsap.to(el, {
    opacity: 0,
    y: -50,
    duration: 0.5
  })
  // 缺少 done() 调用 - 元素立即删除！
}
</script>
```

**正确的代码：**
```vue
<template>
  <!-- 好：done() 回调发出动画完成信号 -->
  <Transition :css="false" @enter="onEnter" @leave="onLeave">
    <div v-if="show" class="box">内容</div>
  </Transition>
</template>

<script setup>
import gsap from 'gsap'

function onEnter(el, done) {
  gsap.from(el, {
    opacity: 0,
    y: 50,
    duration: 0.5,
    onComplete: done  // 告诉 Vue 动画完成
  })
}

function onLeave(el, done) {
  gsap.to(el, {
    opacity: 0,
    y: -50,
    duration: 0.5,
    onComplete: done  // 动画后删除元素
  })
}
</script>
```

## 为什么使用 `:css="false"`？

1. **防止 CSS 干扰**：Vue 不会添加可能与 JS 动画冲突的过渡类
2. **轻微的性能优势**：跳过 CSS 过渡检测
3. **更清晰的意图**：明确表示 JS 控制动画

```vue
<template>
  <!-- 没有 :css="false"，Vue 添加 v-enter-active 等类 -->
  <!-- 这些可能会干扰您的 JS 动画时序 -->
  <Transition @enter="onEnter" @leave="onLeave">
    <div v-if="show">可能有 CSS 冲突</div>
  </Transition>

  <!-- 使用 :css="false"，不添加类 - 完全 JS 控制 -->
  <Transition :css="false" @enter="onEnter" @leave="onLeave">
    <div v-if="show">纯 JS 动画</div>
  </Transition>
</template>
```

## 完整的 JavaScript 过渡示例

```vue
<template>
  <Transition
    :css="false"
    @before-enter="onBeforeEnter"
    @enter="onEnter"
    @after-enter="onAfterEnter"
    @enter-cancelled="onEnterCancelled"
    @before-leave="onBeforeLeave"
    @leave="onLeave"
    @after-leave="onAfterLeave"
    @leave-cancelled="onLeaveCancelled"
  >
    <div v-if="show" class="animated-box">内容</div>
  </Transition>
</template>

<script setup>
import gsap from 'gsap'
import { ref } from 'vue'

const show = ref(false)
let enterAnimation = null
let leaveAnimation = null

function onBeforeEnter(el) {
  // 动画前设置初始状态
  el.style.opacity = 0
  el.style.transform = 'translateY(50px)'
}

function onEnter(el, done) {
  // 存储动画引用以供潜在的取消
  enterAnimation = gsap.to(el, {
    opacity: 1,
    y: 0,
    duration: 0.5,
    ease: 'power2.out',
    onComplete: done  // 使用 :css="false" 时必需
  })
}

function onAfterEnter(el) {
  // 进入完成后清理
  enterAnimation = null
}

function onEnterCancelled() {
  // 处理中断（例如，用户快速切换）
  if (enterAnimation) {
    enterAnimation.kill()
    enterAnimation = null
  }
}

function onBeforeLeave(el) {
  // 离开前设置状态
}

function onLeave(el, done) {
  leaveAnimation = gsap.to(el, {
    opacity: 0,
    y: -50,
    duration: 0.5,
    ease: 'power2.in',
    onComplete: done  // 使用 :css="false" 时必需
  })
}

function onAfterLeave(el) {
  leaveAnimation = null
}

function onLeaveCancelled() {
  if (leaveAnimation) {
    leaveAnimation.kill()
    leaveAnimation = null
  }
}
</script>
```

## 使用 Web Animations API

```vue
<script setup>
function onEnter(el, done) {
  const animation = el.animate([
    { opacity: 0, transform: 'scale(0.9)' },
    { opacity: 1, transform: 'scale(1)' }
  ], {
    duration: 300,
    easing: 'ease-out'
  })

  animation.onfinish = done  // 动画结束时调用 done
}

function onLeave(el, done) {
  const animation = el.animate([
    { opacity: 1, transform: 'scale(1)' },
    { opacity: 0, transform: 'scale(0.9)' }
  ], {
    duration: 300,
    easing: 'ease-in'
  })

  animation.onfinish = done
}
</script>
```

## 常见错误

```javascript
// 错误：立即调用 done() 而不是动画后
function onEnter(el, done) {
  gsap.from(el, { opacity: 0, duration: 0.5 })
  done() // 立即调用 - 动画跳过！
}

// 错误：忘记 done() 参数
function onEnter(el) {  // 没有 'done' 参数
  gsap.from(el, {
    opacity: 0,
    onComplete: done  // 错误：done 未定义！
  })
}

// 正确：将 done 传递给动画回调
function onEnter(el, done) {
  gsap.from(el, {
    opacity: 0,
    duration: 0.5,
    onComplete: done  // 0.5s 后调用
  })
}
```

## 参考
- [Vue.js Transition - JavaScript Hooks](https://vuejs.org/guide/built-ins/transition.html#javascript-hooks)
- [GSAP with Vue](https://gsap.com/resources/vue/)
