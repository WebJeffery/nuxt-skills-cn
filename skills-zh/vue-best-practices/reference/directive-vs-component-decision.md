---
title: 了解何时使用指令 vs 组件
impact: MEDIUM
impactDescription: 当组件更合适时使用指令会导致更难维护和测试
type: best-practice
tags: [vue3, directives, components, architecture, best-practices]
---

# 了解何时使用指令 vs 组件

**影响: 中等** - 从自定义指令内部访问组件实例通常是指令应该成为组件本身的迹象。指令设计用于低级 DOM 操作，而组件更适合封装涉及状态、响应式或复杂逻辑的行为。

选择错误的抽象会导致代码更难维护、测试和重用。

## 任务清单

- [ ] 对简单的、无状态的 DOM 操作使用指令
- [ ] 当需要封装状态或复杂逻辑时使用组件
- [ ] 如果频繁访问 `binding.instance`，考虑使用组件代替
- [ ] 如果行为需要自己的模板，使用组件
- [ ] 对于不需要模板的有状态逻辑，考虑使用 composables

## 决策矩阵

| 需求 | 使用指令 | 使用组件 | 使用 Composable |
|-------------|--------------|---------------|----------------|
| 仅 DOM 操作 | 是 | - | - |
| 需要自己的模板 | - | 是 | - |
| 封装状态 | - | 是 | 可能 |
| 可重用行为 | 是 | 是 | 是 |
| 访问父实例 | 避免 | - | 是 |
| 需要 SSR 支持 | 避免 | 是 | 是 |
| 第三方库集成 | 是 | - | 可能 |
| 复杂的响应式逻辑 | - | 是 | 是 |

## 适合指令的用例

```javascript
// 好: 简单的 DOM 操作
const vFocus = {
  mounted: (el) => el.focus()
}

// 好: 第三方库集成
const vTippy = {
  mounted(el, binding) {
    el._tippy = tippy(el, binding.value)
  },
  updated(el, binding) {
    el._tippy?.setProps(binding.value)
  },
  unmounted(el) {
    el._tippy?.destroy()
  }
}

// 好: Vue 不提供的事件处理
const vClickOutside = {
  mounted(el, binding) {
    el._handler = (e) => {
      if (!el.contains(e.target)) binding.value(e)
    }
    document.addEventListener('click', el._handler)
  },
  unmounted(el) {
    document.removeEventListener('click', el._handler)
  }
}

// 好: Intersection Observer
const vLazyLoad = {
  mounted(el, binding) {
    const observer = new IntersectionObserver(([entry]) => {
      if (entry.isIntersecting) {
        el.src = binding.value
        observer.disconnect()
      }
    })
    observer.observe(el)
    el._observer = observer
  },
  unmounted(el) {
    el._observer?.disconnect()
  }
}
```

## 适合组件的用例

```vue
<!-- 好: 带有模板和状态的组件 -->
<!-- Tooltip.vue -->
<template>
  <div class="tooltip-wrapper" @mouseenter="show" @mouseleave="hide">
    <slot></slot>
    <Transition name="fade">
      <div v-if="isVisible" class="tooltip-content">
        {{ content }}
      </div>
    </Transition>
  </div>
</template>

<script setup>
import { ref } from 'vue'

defineProps({
  content: String
})

const isVisible = ref(false)
const show = () => isVisible.value = true
const hide = () => isVisible.value = false
</script>
```

```vue
<!-- 好: 带有复杂逻辑的组件 -->
<!-- InfiniteScroll.vue -->
<template>
  <div ref="container">
    <slot></slot>
    <div v-if="loading" class="loading-indicator">
      <slot name="loading">Loading...</slot>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const props = defineProps({
  threshold: { type: Number, default: 100 }
})

const emit = defineEmits(['load-more'])
const container = ref(null)
const loading = ref(false)

// 带有状态管理的复杂滚动逻辑
const handleScroll = () => {
  if (loading.value) return
  const { scrollHeight, scrollTop, clientHeight } = container.value
  if (scrollHeight - scrollTop - clientHeight < props.threshold) {
    loading.value = true
    emit('load-more', () => { loading.value = false })
  }
}

onMounted(() => container.value?.addEventListener('scroll', handleScroll))
onUnmounted(() => container.value?.removeEventListener('scroll', handleScroll))
</script>
```

## 适合 Composable 的用例

```javascript
// 好: 可重用的有状态逻辑，没有模板
// useClickOutside.js
import { onMounted, onUnmounted, ref } from 'vue'

export function useClickOutside(elementRef, callback) {
  const isClickedOutside = ref(false)

  const handler = (e) => {
    if (elementRef.value && !elementRef.value.contains(e.target)) {
      isClickedOutside.value = true
      callback?.(e)
    }
  }

  onMounted(() => document.addEventListener('click', handler))
  onUnmounted(() => document.removeEventListener('click', handler))

  return { isClickedOutside }
}

// 在组件中使用
const dropdownRef = ref(null)
const { isClickedOutside } = useClickOutside(dropdownRef, () => {
  isOpen.value = false
})
```

## 反模式: 指令过多访问实例

```javascript
// 反模式: 指令过度依赖组件实例
const vBadPattern = {
  mounted(el, binding) {
    // 过多访问实例 = 应该是组件
    const instance = binding.instance
    instance.someMethod()
    instance.someProperty = 'value'
    instance.$watch('someProp', (val) => {
      el.textContent = val
    })
  }
}

// 更好: 使用组件或 composable
// 组件版本
<template>
  <div>{{ someProp }}</div>
</template>

<script setup>
const props = defineProps(['someProp'])
</script>
```

## 何时实例访问是可接受的

```javascript
// 可以: 最小化实例访问以满足特定需求
const vPermission = {
  mounted(el, binding) {
    // 检查全局权限 - 可接受
    const userPermissions = binding.instance.$store?.state.user.permissions
    if (!userPermissions?.includes(binding.value)) {
      el.style.display = 'none'
    }
  }
}
```

## 参考
- [Vue.js 自定义指令](https://vuejs.org/guide/reusability/custom-directives)
- [Vue.js Composables](https://vuejs.org/guide/reusability/composables.html)
- [Vue.js 组件基础](https://vuejs.org/guide/essentials/component-basics.html)
