---
title: 使用 Key 属性强制重新渲染动画
impact: MEDIUM
impactDescription: 没有 key 属性，Vue 重用 DOM 元素，AutoAnimate 等动画库无法检测更改以进行动画
type: gotcha
tags: [vue3, animation, key, autoanimate, rerender, dom]
---

# 使用 Key 属性强制重新渲染动画

**影响: 中等** - Vue 通过尽可能重用 DOM 元素来优化性能。然而，这种优化可能会阻止动画库（如 AutoAnimate）检测更改，因为元素是在原位更新而不是重新创建。添加 `:key` 属性强制 Vue 将更改的元素视为新的，触发适当的动画。

## 任务清单

- [ ] 对内容更改时应动画的元素添加 `:key`
- [ ] 对 key 使用唯一的、更改的值（而不是索引）
- [ ] 对于路由过渡，将 `:key="$route.fullPath"` 添加到 `<router-view>`
- [ ] 将 `v-auto-animate` 应用于 keyed 子元素的父元素

**有问题的代码:**
```vue
<template>
  <!-- 坏: 文本更改但没有动画发生 -->
  <div v-auto-animate>
    <p>{{ message }}</p>  <!-- 没有 key - 元素被重用 -->
  </div>

  <!-- 坏: 图像源更改但没有动画 -->
  <div v-auto-animate>
    <img :src="imageUrl" />  <!-- 没有 key - 元素被重用 -->
  </div>

  <!-- 坏: 路由更改不动画 -->
  <router-view v-auto-animate />  <!-- 没有 key -->
</template>

<script setup>
import { ref } from 'vue'

const message = ref('Hello')
const imageUrl = ref('/images/photo1.jpg')

// 更改这些不会触发动画，因为
// Vue 更新现有元素而不是替换它们
</script>
```

**正确的代码:**
```vue
<template>
  <!-- 好: Key 强制重新渲染，触发动画 -->
  <div v-auto-animate>
    <p :key="message">{{ message }}</p>
  </div>

  <!-- 好: 图像在源更改时动画 -->
  <div v-auto-animate>
    <img :key="imageUrl" :src="imageUrl" />
  </div>

  <!-- 好: 路由更改正确动画 -->
  <router-view :key="$route.fullPath" v-auto-animate />
</template>

<script setup>
import { ref } from 'vue'

const message = ref('Hello')
const imageUrl = ref('/images/photo1.jpg')

// 现在更改这些将触发动画
function updateMessage() {
  message.value = 'World'  // 为新的 <p> 触发进入动画
}
</script>
```

## 为什么这有效

当 Vue 看到 `:key` 更改时：
1. 它将旧元素和新元素视为不同
2. 旧元素被移除（触发离开动画）
3. 创建新元素（触发进入动画）

没有 `:key`:
1. Vue 在相同位置看到相同类型的元素
2. 它在原位更新元素的属性
3. 没有发生 DOM 添加/移除，所以没有动画触发

## 常见用例

### 动画文本内容更改

```vue
<template>
  <div v-auto-animate>
    <h1 :key="title">{{ title }}</h1>
    <p :key="description">{{ description }}</p>
  </div>
</template>
```

### 动画动态组件

```vue
<template>
  <div v-auto-animate>
    <component :is="currentComponent" :key="currentComponent" />
  </div>
</template>
```

### 动画路由过渡

```vue
<template>
  <router-view v-slot="{ Component, route }">
    <div v-auto-animate>
      <component :is="Component" :key="route.fullPath" />
    </div>
  </router-view>
</template>
```

## 与 Vue 的内置 Transition

同样的原则适用于 Vue 的 `<Transition>` 组件：

```vue
<template>
  <!-- 好: Key 在内容更改时触发过渡 -->
  <Transition name="fade" mode="out-in">
    <p :key="message">{{ message }}</p>
  </Transition>

  <!-- 好: 条件内容的不同键 -->
  <Transition name="fade" mode="out-in">
    <div v-if="isLoading" key="loading">加载中...</div>
    <div v-else key="content">{{ content }}</div>
  </Transition>
</template>
```

## 注意: 性能影响

使用 `:key` 强制完整的组件重新创建。对于频繁更改的数据：
- keyed 元素下的整个组件树被销毁并重新创建
- 任何组件状态都会丢失
- 考虑动画是否值得性能成本

```vue
<!-- 对复杂组件要谨慎 -->
<ComplexDashboard :key="refreshTrigger" />
<!-- 这会销毁并重新创建整个仪表板！ -->
```

## 参考
- [Vue.js 动画技术](https://vuejs.org/guide/extras/animation.html)
- [AutoAnimate 与 Vue](https://auto-animate.formkit.com/#usage-vue)
- [Vue.js v-for 与 key](https://vuejs.org/guide/essentials/list.html#maintaining-state-with-key)
