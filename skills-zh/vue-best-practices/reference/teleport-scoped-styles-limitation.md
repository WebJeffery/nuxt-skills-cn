---
title: 作用域样式可能不适用于传送内容
impact: MEDIUM
impactDescription: 由于数据属性限制，作用域样式可能无法应用于传送元素
type: gotcha
tags: [vue3, teleport, scoped-styles, css]
---

# 作用域样式可能不适用于传送内容

**影响: MEDIUM** - 将作用域样式与 Teleport 一起使用时，样式可能无法正确应用于传送元素。这是一个已知的限制，与 Vue 的作用域样式属性如何与在组件 DOM 树外部渲染的元素工作有关。

## 任务清单

- [ ] 测试传送内容上的作用域样式
- [ ] 对传送内容使用 `:deep()` 选择器或非作用域样式
- [ ] 考虑 CSS 模块作为替代方案
- [ ] 将传送内容样式保留在单独的非作用域样式块中

**问题 - 作用域样式未应用:**
```vue
<template>
  <Teleport to="body">
    <div class="modal">
      <p class="modal-text">这段文本可能没有被样式化!</p>
    </div>
  </Teleport>
</template>

<style scoped>
/* 这些样式可能不适用于传送内容 */
.modal {
  background: white;
  padding: 20px;
}

.modal-text {
  color: blue;  /* 可能不起作用 */
}
</style>
```

**解决方案 1 - 对传送内容使用非作用域样式:**
```vue
<template>
  <Teleport to="body">
    <div class="my-modal">
      <p class="my-modal-text">这段文本将被样式化</p>
    </div>
  </Teleport>
</template>

<style scoped>
/* 组件特定的样式 */
.button { color: blue; }
</style>

<style>
/* 传送内容的非作用域样式 */
/* 使用特定的类名以避免冲突 */
.my-modal {
  background: white;
  padding: 20px;
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.my-modal-text {
  color: blue;
}
</style>
```

**解决方案 2 - 使用 :deep() 选择器:**
```vue
<template>
  <Teleport to="body">
    <div class="modal">
      <p class="modal-text">使用 :deep() 样式化</p>
    </div>
  </Teleport>
</template>

<style scoped>
:deep(.modal) {
  background: white;
  padding: 20px;
}

:deep(.modal-text) {
  color: blue;
}
</style>
```

**解决方案 3 - CSS 模块:**
```vue
<template>
  <Teleport to="body">
    <div :class="$style.modal">
      <p :class="$style.modalText">使用 CSS 模块样式化</p>
    </div>
  </Teleport>
</template>

<style module>
.modal {
  background: white;
  padding: 20px;
}

.modalText {
  color: blue;
}
</style>
```

## 带有 Teleport 的多根组件

将 Teleport 用作多个根节点之一会导致额外的问题:

```vue
<template>
  <!-- 多根组件 -->
  <button @click="open = true">打开</button>
  <Teleport to="body">
    <div class="modal">内容</div>
  </Teleport>
</template>

<!-- 警告: class/style 属性可能不会被继承 -->
```

显式传递类以避免继承问题:

```vue
<template>
  <button @click="open = true">打开</button>
  <Teleport to="body">
    <div :class="['modal', $attrs.class]" :style="$attrs.style">
      内容
    </div>
  </Teleport>
</template>
```

## 最佳实践: 专用模态框样式

为模态框/覆盖组件创建专用样式表:

```css
/* modal-styles.css */
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
  width: 90%;
}
```

```vue
<script setup>
import './modal-styles.css'
</script>

<template>
  <Teleport to="body">
    <div v-if="open" class="modal-overlay">
      <div class="modal-content">
        <slot />
      </div>
    </div>
  </Teleport>
</template>
```

## 参考
- [Vue.js SFC CSS 功能 - 作用域 CSS](https://vuejs.org/api/sfc-css-features.html#scoped-css)
- [GitHub Issue #2047 - 作用域样式和传送](https://github.com/vuejs/core/issues/2047)
