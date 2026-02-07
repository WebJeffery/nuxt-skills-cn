---
title: KeepAlive 与 Transition 内存泄漏
impact: MEDIUM
impactDescription: 组合使用 KeepAlive 和 Transition 可能导致某些 Vue 版本中的内存泄漏
type: gotcha
tags: [vue3, keepalive, transition, memory-leak, animation]
---

# KeepAlive 与 Transition 内存泄漏

**影响：中** - 使用 `<Transition>` 和 `<KeepAlive>` 组合时存在已知的内存泄漏。组件实例可能无法从内存中正确释放。

## 任务清单

- [ ] 同时使用 KeepAlive + Transition 时测试内存行为
- [ ] 考虑缓存组件是否需要过渡动画
- [ ] 使用浏览器 DevTools 内存选项卡验证没有泄漏
- [ ] 保持 Vue 更新以获取最新的错误修复

## 问题

```vue
<template>
  <!-- 某些 Vue 版本中的已知内存泄漏组合 -->
  <Transition name="fade">
    <KeepAlive>
      <component :is="currentView" />
    </KeepAlive>
  </Transition>
</template>
```

在组件之间重复切换时：
- 组件实例在内存中累积
- 引用阻止垃圾回收
- 每次切换内存使用量都会增长

## 诊断

使用 Chrome DevTools 检测泄漏：

1. 打开 DevTools > 内存选项卡
2. 拍摄堆快照
3. 在组件之间切换 10+ 次
4. 拍摄另一个堆快照
5. 比较：查找增长的 VueComponent 计数

## 变通方法

### 选项 1：如果不需要则移除 Transition

```vue
<template>
  <!-- 没有 Transition 则没有内存泄漏 -->
  <KeepAlive :max="5">
    <component :is="currentView" />
  </KeepAlive>
</template>
```

### 选项 2：使用 CSS 动画代替

```vue
<template>
  <KeepAlive :max="5">
    <component
      :is="currentView"
      :class="{ 'fade-enter': isTransitioning }"
    />
  </KeepAlive>
</template>

<style>
.fade-enter {
  animation: fadeIn 0.3s ease-in;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}
</style>
```

### 选项 3：使用严格缓存限制

如果你必须同时使用两者，用严格限制最小化影响：

```vue
<template>
  <Transition name="fade" mode="out-in">
    <KeepAlive :max="3">
      <component :is="currentView" />
    </KeepAlive>
  </Transition>
</template>
```

### 选项 4：基于键的缓存失效

在需要时强制新实例：

```vue
<script setup>
import { ref, computed } from 'vue'

const currentView = ref('Dashboard')
const cacheKey = ref(0)

function switchViewFresh(view) {
  currentView.value = view
  cacheKey.value++ // 强制新实例
}
</script>

<template>
  <Transition name="fade" mode="out-in">
    <KeepAlive :max="3">
      <component :is="currentView" :key="cacheKey" />
    </KeepAlive>
  </Transition>
</template>
```

## 保持 Vue 更新

这是 Vue 的 GitHub 存储库中跟踪的已知问题。内存泄漏修复会定期发布，因此确保你使用最新的 Vue 版本：

```bash
npm update vue
```

## 关键点

1. **已知问题** - KeepAlive + Transition 的内存泄漏已记录
2. **在 DevTools 中测试** - 使用内存选项卡验证你的特定用法
3. **考虑替代方案** - CSS 动画可能在没有泄漏的情况下工作
4. **设置严格的 `max`** - 限制缓存大小以限制内存影响
5. **保持 Vue 更新** - 错误修复会定期发布

## 参考
- [GitHub Issue #9842：transition 和 keep-alive 的内存泄漏](https://github.com/vuejs/vue/issues/9842)
- [GitHub Issue #9840：transition 和 keep-alive 的内存泄漏](https://github.com/vuejs/vue/issues/9840)
- [Vue.js KeepAlive 文档](https://vuejs.org/guide/built-ins/keep-alive.html)
