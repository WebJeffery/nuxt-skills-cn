---
title: 使用 KeepAlive 保留动态组件状态
impact: MEDIUM
impactDescription: 动态组件切换会销毁并重新创建组件，除非用 KeepAlive 包装，否则会丢失所有内部状态
type: best-practice
tags: [vue3, dynamic-components, keepalive, component-is, state-preservation, performance]
---

# 使用 KeepAlive 保留动态组件状态

**影响: 中等** - 当使用 `<component :is="...">` 在组件之间切换时，Vue 会销毁旧组件并创建新组件。所有内部状态（表单输入、滚动位置、获取的数据）都会丢失。将动态组件包装在 `<KeepAlive>` 中会缓存它们并保留其状态。

## 任务清单

- [ ] 需要状态保留时用 `<KeepAlive>` 包装 `<component :is>`
- [ ] 使用 `include` 和 `exclude` 控制哪些组件被缓存
- [ ] 使用 `max` 限制缓存大小并防止内存问题
- [ ] 为缓存感知逻辑实现 `onActivated`/`onDeactivated` 钩子
- [ ] 当需要新状态时考虑不使用 KeepAlive

## 问题：状态丢失

```vue
<script setup>
import { ref, shallowRef } from 'vue'
import TabA from './TabA.vue'
import TabB from './TabB.vue'

const currentTab = shallowRef(TabA)
</script>

<template>
  <button @click="currentTab = TabA">标签页 A</button>
  <button @click="currentTab = TabB">标签页 B</button>

  <!-- 切换标签页时状态会丢失！ -->
  <component :is="currentTab" />
</template>
```

如果 TabA 有一个带有用户输入的表单，切换到 TabB 并返回会重置所有输入。

## 解决方案：KeepAlive

```vue
<template>
  <button @click="currentTab = TabA">标签页 A</button>
  <button @click="currentTab = TabB">标签页 B</button>

  <!-- 切换时状态被保留 -->
  <KeepAlive>
    <component :is="currentTab" />
  </KeepAlive>
</template>
```

现在即使显示 TabB，TabA 的状态也会持久化。

## 控制缓存内容

### 按名称包含/排除

仅缓存特定组件：

```vue
<template>
  <!-- 仅缓存名为 'TabA' 或 'TabB' 的组件 -->
  <KeepAlive include="TabA,TabB">
    <component :is="currentTab" />
  </KeepAlive>

  <!-- 缓存除 'HeavyComponent' 外的所有组件 -->
  <KeepAlive exclude="HeavyComponent">
    <component :is="currentTab" />
  </KeepAlive>

  <!-- 使用正则表达式 -->
  <KeepAlive :include="/^Tab/">
    <component :is="currentTab" />
  </KeepAlive>

  <!-- 使用数组 -->
  <KeepAlive :include="['TabA', 'TabB', 'Settings']">
    <component :is="currentTab" />
  </KeepAlive>
</template>
```

**重要：** 组件必须具有 `name` 选项才能匹配：

```vue
<!-- TabA.vue -->
<script>
export default {
  name: 'TabA' // 包含/排除匹配所必需
}
</script>

<script setup>
// ...组合式 API 代码
</script>
```

或者在 Vue 3.3+ 中使用 `<script setup>`：

```vue
<script setup>
defineOptions({
  name: 'TabA'
})
</script>
```

### 限制缓存大小

防止许多缓存组件导致内存问题：

```vue
<template>
  <!-- 仅在缓存中保留最后 5 个组件 -->
  <KeepAlive :max="5">
    <component :is="currentTab" />
  </KeepAlive>
</template>
```

当缓存超过 `max` 时，最近最少访问的组件会被销毁。

## 生命周期钩子：onActivated 和 onDeactivated

缓存的组件需要特殊的生命周期钩子：

```vue
<!-- TabA.vue -->
<script setup>
import { onMounted, onUnmounted, onActivated, onDeactivated } from 'vue'

// 仅在首次挂载时调用，切换返回时不调用
onMounted(() => {
  console.log('TabA 已挂载（一次）')
})

// 仅在真正销毁时调用，切换离开时不调用
onUnmounted(() => {
  console.log('TabA 已卸载')
})

// 每次组件变为可见时调用
onActivated(() => {
  console.log('TabA 已激活')
  // 刷新数据、恢复计时器等
  fetchLatestData()
})

// 每次组件被隐藏时调用（但保留在缓存中）
onDeactivated(() => {
  console.log('TabA 已停用')
  // 暂停计时器、保存草稿等
  pauseAutoRefresh()
})
</script>
```

**激活钩子的常见用例：**
- 返回标签页时刷新过时数据
- 恢复/暂停视频或音频播放
- 重新连接/断开 WebSocket 连接
- 保存/恢复滚动位置
- 跟踪标签页视图的分析数据

## KeepAlive 与 Vue Router

对于基于路由的缓存：

```vue
<!-- App.vue -->
<template>
  <router-view v-slot="{ Component }">
    <KeepAlive include="Dashboard,Settings">
      <component :is="Component" />
    </KeepAlive>
  </router-view>
</template>
```

**带过渡效果：**

```vue
<template>
  <router-view v-slot="{ Component }">
    <Transition name="fade" mode="out-in">
      <KeepAlive>
        <component :is="Component" :key="$route.fullPath" />
      </KeepAlive>
    </Transition>
  </router-view>
</template>
```

## 何时不使用 KeepAlive

在以下情况下不要缓存：

```vue
<!-- 每次都需要新数据 -->
<template>
  <!-- 没有 KeepAlive - 每次访问都需要新的搜索结果 -->
  <component :is="currentView" />
</template>
```

- 表单应该在访问之间重置
- 数据必须是新鲜的（实时仪表板）
- 组件有显著的内存占用
- 敏感数据应该被清除

## 性能考虑

```vue
<script setup>
import { shallowRef } from 'vue'
import TabA from './TabA.vue'
import TabB from './TabB.vue'

// 对组件引用使用 shallowRef
// 常规 ref 会不必要地深度跟踪组件对象
const currentTab = shallowRef(TabA)
</script>
```

## 参考
- [Vue.js KeepAlive](https://vuejs.org/guide/built-ins/keep-alive.html)
- [Vue.js 动态组件](https://vuejs.org/guide/essentials/component-basics.html#dynamic-components)
