---
title: 使用 Key 属性强制相同元素类型的过渡
impact: HIGH
impactDescription: 没有 key，Vue 重用相同的 DOM 元素，当只有内容更改时不会发生过渡
type: gotcha
tags: [vue3, transition, key, animation, dom-reuse]
---

# 使用 Key 属性强制相同元素类型的过渡

**影响：高** - 在相同类型的元素之间转换时（例如，两个 `<span>` 元素或同一组件的两个实例），Vue 的 DOM diffing 算法将重用现有元素并仅更新其内容。这意味着不会发生过渡，因为元素从未实际插入或删除。您必须使用 `key` 属性强制 Vue 将它们视为不同的元素。

这是"我的过渡不工作"问题的最常见原因之一。

## 任务清单

- [ ] 在相同元素类型之间转换时，始终添加唯一的 `key`
- [ ] 对于同一元素中的动态内容，将 `key` 绑定到更改的值
- [ ] 对于具有相同元素类型的 `v-if`/`v-else`，为每个添加不同的键
- [ ] 记住：没有 `key`，只有文本内容更新，没有动画

**有问题的代码：**
```vue
<template>
  <!-- 错误：相同的元素类型，没有 key - 不发生过渡！ -->
  <Transition name="fade">
    <span>{{ count }}</span>
  </Transition>

  <button @click="count++">增加</button>
</template>

<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>

<!-- span 只是更新其文本内容，从不触发进入/离开 -->
```

```vue
<template>
  <!-- 错误：v-if/v-else 中没有键的相同元素类型 -->
  <Transition name="fade">
    <p v-if="isActive">活动状态</p>
    <p v-else>非活动状态</p>
  </Transition>
</template>

<!-- Vue 可能重用 <p> 元素，导致不一致的过渡 -->
```

**正确的代码：**
```vue
<template>
  <!-- 好：键绑定到更改的值强制重新渲染 -->
  <Transition name="fade" mode="out-in">
    <span :key="count">{{ count }}</span>
  </Transition>

  <button @click="count++">增加</button>
</template>

<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>

<!-- 现在每个数字更改都会触发完整的进入/离开过渡 -->
```

```vue
<template>
  <!-- 好：相同元素类型上的显式键 -->
  <Transition name="fade" mode="out-in">
    <p v-if="isActive" key="active">活动状态</p>
    <p v-else key="inactive">非活动状态</p>
  </Transition>
</template>
```

```vue
<template>
  <!-- 好：动态组件上的键 -->
  <Transition name="slide" mode="out-in">
    <component :is="currentComponent" :key="currentComponent.name" />
  </Transition>
</template>
```

## 动画计数器示例

```vue
<template>
  <div class="counter">
    <button @click="count--">-</button>

    <Transition name="slide-up" mode="out-in">
      <span :key="count" class="count-display">{{ count }}</span>
    </Transition>

    <button @click="count++">+</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>

<style>
.slide-up-enter-active,
.slide-up-leave-active {
  transition: all 0.2s ease;
}

.slide-up-enter-from {
  opacity: 0;
  transform: translateY(10px);
}

.slide-up-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}

.count-display {
  display: inline-block;
  min-width: 2em;
  text-align: center;
}
</style>
```

## 使用相同组件的选项卡切换

```vue
<template>
  <div class="tabs">
    <button
      v-for="tab in tabs"
      :key="tab.id"
      :class="{ active: currentTab === tab.id }"
      @click="currentTab = tab.id"
    >
      {{ tab.label }}
    </button>
  </div>

  <!-- 键在这里是必不可少的，因为所有选项卡都使用相同的组件 -->
  <Transition name="fade" mode="out-in">
    <TabContent :key="currentTab" :tab-id="currentTab" />
  </Transition>
</template>

<script setup>
import { ref } from 'vue'
import TabContent from './TabContent.vue'

const tabs = [
  { id: 'home', label: '首页' },
  { id: 'profile', label: '个人资料' },
  { id: 'settings', label: '设置' }
]
const currentTab = ref('home')
</script>
```

## 何时不需要键

在**不同**元素类型之间转换时不需要键：

```vue
<template>
  <!-- 不需要键 - 不同的元素类型 -->
  <Transition name="fade">
    <button v-if="isEditing" @click="save">保存</button>
    <span v-else>查看模式</span>
  </Transition>
</template>
```

## 没有键时的常见症状

1. 内容更改时没有动画
2. 文本立即更新而不是淡入淡出
3. 从不应用进入/离开 CSS 类
4. DevTools 显示元素内容更改但没有过渡状态

## 参考
- [Vue.js Transition - Between Elements](https://vuejs.org/guide/built-ins/transition.html#transition-between-elements)
