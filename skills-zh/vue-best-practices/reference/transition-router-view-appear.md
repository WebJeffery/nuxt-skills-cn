---
title: RouterView 过渡始终应用，尽管缺少 appear 属性
impact: LOW
impactDescription: 由于异步导航，初始页面加载时 RouterView 触发过渡动画，即使没有 appear 属性
type: gotcha
tags: [vue3, transition, vue-router, appear, initial-load, navigation]
---

# RouterView 过渡始终应用，尽管缺少 appear 属性

**影响：低** - 将 `<Transition>` 与 Vue Router 的 `<RouterView>` 一起使用时，即使您没有添加 `appear` 属性，进入过渡动画也会在初始页面加载时运行。这与正常的过渡行为不同，后者需要 `appear` 才能进行初始渲染动画。这是因为 Vue Router 的导航是异步的，导致组件在初始渲染之后挂载。

## 任务清单

- [ ] 注意 RouterView 过渡始终在初始加载时动画
- [ ] 如果您希望在初始加载时不动画，需要显式处理
- [ ] 不要添加 `appear` 属性期望它改变行为 - 它实际上已经启用
- [ ] 考虑初始动画是否适合您的 UX

**预期行为（正常过渡）：**
```vue
<template>
  <!-- 没有 appear：初始渲染时不动画 -->
  <Transition name="fade">
    <div v-if="show">内容</div>
  </Transition>

  <!-- 有 appear：初始渲染时动画 -->
  <Transition name="fade" appear>
    <div v-if="show">内容</div>
  </Transition>
</template>
```

**RouterView 行为（不同！）：**
```vue
<template>
  <!-- RouterView 过渡始终在初始加载时动画 -->
  <!-- appear 属性在这里没有效果 -->
  <RouterView v-slot="{ Component }">
    <Transition name="fade">
      <component :is="Component" />
    </Transition>
  </RouterView>
</template>

<!--
在初始页面加载时：
1. Vue 渲染应用程序
2. Router 解析路由（异步）
3. 组件在初始渲染之后挂载
4. 进入过渡触发（就像从 v-if="false" 切换到 v-if="true"）
-->
```

## 为什么会发生这种情况

Vue Router 导航是异步的。顺序是：

1. Vue 应用程序使用空的 RouterView 挂载
2. Router 解析初始路由
3. 路由组件被插入到 RouterView
4. 此插入触发进入过渡

由于组件在初始渲染中不存在并且随后被"插入"，Vue 将其视为正常的进入过渡，而不是初始渲染。

## 如果您想禁用初始动画

```vue
<template>
  <RouterView v-slot="{ Component }">
    <Transition :name="isInitialLoad ? '' : 'fade'" mode="out-in">
      <component :is="Component" />
    </Transition>
  </RouterView>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'

const isInitialLoad = ref(true)
const router = useRouter()

// 第一次导航完成后，启用过渡
router.isReady().then(() => {
  // 小延迟以确保初始渲染完成
  setTimeout(() => {
    isInitialLoad.value = false
  }, 0)
})
</script>
```

## 替代方案：使用 CSS 跳过第一次动画

```vue
<template>
  <RouterView v-slot="{ Component }">
    <Transition name="fade" mode="out-in">
      <component :is="Component" :class="{ 'skip-initial': isInitialLoad }" />
    </Transition>
  </RouterView>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const isInitialLoad = ref(true)
const router = useRouter()

router.isReady().then(() => {
  isInitialLoad.value = false
})
</script>

<style>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

/* 初始加载时跳过动画 */
.skip-initial.fade-enter-active {
  transition: none;
}
</style>
```

## 标准 RouterView 过渡模式

如果您对初始动画没问题（通常需要），使用标准模式：

```vue
<template>
  <RouterView v-slot="{ Component, route }">
    <Transition :name="route.meta.transition || 'fade'" mode="out-in">
      <component :is="Component" :key="route.path" />
    </Transition>
  </RouterView>
</template>

<style>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

/* 通过 meta 的路由特定过渡 */
.slide-enter-active,
.slide-leave-active {
  transition: transform 0.3s ease;
}

.slide-enter-from {
  transform: translateX(100%);
}

.slide-leave-to {
  transform: translateX(-100%);
}
</style>
```

```javascript
// router.js
const routes = [
  {
    path: '/',
    component: Home
  },
  {
    path: '/about',
    component: About,
    meta: { transition: 'slide' }  // 此路由的自定义过渡
  }
]
```

## 参考
- [Vue Router Transitions](https://router.vuejs.org/guide/advanced/transitions.html)
- [Vue.js Transition appear](https://vuejs.org/guide/built-ins/transition.html#transition-on-appear)
