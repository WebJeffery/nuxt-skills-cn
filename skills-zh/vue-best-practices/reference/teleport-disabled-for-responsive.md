---
title: 使用 Teleport disabled 属性进行响应式设计
impact: LOW
impactDescription: 有条件地切换传送以实现响应式布局
type: best-practice
tags: [vue3, teleport, responsive, mobile]
---

# 使用 Teleport disabled 属性进行响应式设计

**影响: LOW** - `<Teleport>` 上的 `disabled` 属性允许有条件传送。这对于响应式设计很有用，其中内容应该在桌面上作为覆盖层渲染，但在移动设备上内联渲染。

## 任务清单

- [ ] 使用 `:disabled` 绑定进行动态传送控制
- [ ] 结合 CSS 媒体查询以实现完整的响应式行为
- [ ] 考虑位置更改的可访问性影响

**基本用法:**
```vue
<template>
  <Teleport to="body" :disabled="isMobile">
    <div class="sidebar">
      <!-- 在移动设备上: 内联渲染 -->
      <!-- 在桌面上: 作为覆盖层传送到 body -->
      <nav>导航内容</nav>
    </div>
  </Teleport>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const isMobile = ref(false)

function checkMobile() {
  isMobile.value = window.innerWidth < 768
}

onMounted(() => {
  checkMobile()
  window.addEventListener('resize', checkMobile)
})

onUnmounted(() => {
  window.removeEventListener('resize', checkMobile)
})
</script>
```

## 响应式模态框模式

```vue
<template>
  <button @click="open = true">打开菜单</button>

  <!-- 桌面传送，移动设备内联表单 -->
  <Teleport to="body" :disabled="isMobile">
    <Transition :name="isMobile ? 'slide-up' : 'fade'">
      <div v-if="open" :class="isMobile ? 'bottom-sheet' : 'modal-overlay'">
        <div :class="isMobile ? 'sheet-content' : 'modal-content'">
          <slot />
          <button @click="open = false">关闭</button>
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<script setup>
import { ref } from 'vue'
import { useMediaQuery } from '@vueuse/core'

const open = ref(false)
const isMobile = useMediaQuery('(max-width: 768px)')
</script>

<style scoped>
/* 桌面: 居中模态框覆盖层 */
.modal-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal-content {
  background: white;
  border-radius: 8px;
  padding: 24px;
  max-width: 500px;
}

/* 移动设备: 底部表单 */
.bottom-sheet {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  background: rgba(0, 0, 0, 0.5);
}

.sheet-content {
  background: white;
  border-radius: 16px 16px 0 0;
  padding: 24px;
}

/* 过渡 */
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.3s;
}
.fade-enter-from, .fade-leave-to {
  opacity: 0;
}

.slide-up-enter-active, .slide-up-leave-active {
  transition: transform 0.3s;
}
.slide-up-enter-from, .slide-up-leave-to {
  transform: translateY(100%);
}
</style>
```

## 侧边栏导航示例

```vue
<!-- AppLayout.vue -->
<template>
  <div class="layout">
    <header>
      <button @click="sidebarOpen = !sidebarOpen" class="menu-toggle">
        菜单
      </button>
    </header>

    <main>
      <slot />
    </main>

    <!-- 桌面: 侧边栏在正常流中 -->
    <!-- 移动设备: 作为覆盖层传送到 body -->
    <Teleport to="body" :disabled="!isMobile">
      <aside v-if="sidebarOpen || !isMobile" class="sidebar">
        <nav>
          <a href="/">首页</a>
          <a href="/about">关于</a>
          <a href="/contact">联系</a>
        </nav>
      </aside>
    </Teleport>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useMediaQuery } from '@vueuse/core'

const sidebarOpen = ref(false)
const isMobile = useMediaQuery('(max-width: 1024px)')
</script>

<style>
.layout {
  display: flex;
}

/* 桌面: 侧边栏在 flex 布局中 */
@media (min-width: 1025px) {
  .sidebar {
    width: 250px;
    height: 100vh;
    position: sticky;
    top: 0;
  }
}

/* 移动设备: 覆盖层侧边栏 */
@media (max-width: 1024px) {
  .sidebar {
    position: fixed;
    inset: 0;
    background: white;
    z-index: 1000;
  }
}
</style>
```

## VueUse 集成

使用 `@vueuse/core` 进行响应式媒体查询:

```vue
<script setup>
import { useMediaQuery, useBreakpoints } from '@vueuse/core'

// 简单媒体查询
const isMobile = useMediaQuery('(max-width: 768px)')

// 或使用断点
const breakpoints = useBreakpoints({
  mobile: 0,
  tablet: 768,
  desktop: 1024,
})

const isMobileOrTablet = breakpoints.smaller('desktop')
</script>

<template>
  <Teleport to="body" :disabled="isMobileOrTablet">
    <ModalContent />
  </Teleport>
</template>
```

## 参考
- [Vue.js Teleport - 禁用传送](https://vuejs.org/guide/built-ins/teleport.html#disabling-teleport)
- [VueUse - useMediaQuery](https://vueuse.org/core/useMediaQuery/)
