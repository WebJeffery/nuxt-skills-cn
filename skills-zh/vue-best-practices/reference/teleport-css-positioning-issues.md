---
title: 使用 Teleport 避免 CSS 定位问题
impact: MEDIUM
impactDescription: 当父元素具有 transform、perspective 或 filter CSS 属性时，模态框和覆盖层可能会中断
type: best-practice
tags: [vue3, teleport, modal, css, z-index, positioning]
---

# 使用 Teleport 避免 CSS 定位问题

**影响: MEDIUM** - CSS `position: fixed` 仅在没有祖先具有 `transform`、`perspective` 或 `filter` 属性时才相对于视口定位。没有 Teleport，当父元素使用这些 CSS 属性时，深度嵌套在 DOM 中的模态框可能会中断。

这是在父元素上使用 CSS 动画或当模态框深度嵌套在组件层次结构中时的常见问题。

## 任务清单

- [ ] 对模态框、工具提示和其他固定定位覆盖层使用 `<Teleport>`
- [ ] 传送到 Vue 应用层次结构之外的容器（例如，`body` 或 `#modals`）
- [ ] 避免仅依赖 `z-index` 进行堆叠 - 传送确保正确的分层

**问题 - 没有 Teleport:**
```vue
<template>
  <div class="animated-container">
    <!-- 模态框嵌套在动画父元素内 -->
    <button @click="showModal = true">打开模态框</button>

    <!-- 损坏: position: fixed 相对于 .animated-container，而不是视口 -->
    <div v-if="showModal" class="modal">
      <p>这个模态框将定位错误!</p>
    </div>
  </div>
</template>

<style>
.animated-container {
  /* 这破坏了后代中的 position: fixed */
  transform: translateX(0);
  /* 或其中任何一个: */
  /* perspective: 1000px; */
  /* filter: blur(0); */
}

.modal {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 9999;  /* z-index 受父元素堆叠上下文约束 */
}
</style>
```

**解决方案 - 使用 Teleport:**
```vue
<template>
  <div class="animated-container">
    <button @click="showModal = true">打开模态框</button>

    <!-- Teleport 将模态框移出有问题的父元素 -->
    <Teleport to="body">
      <div v-if="showModal" class="modal">
        <p>模态框现在相对于视口正确定位!</p>
        <button @click="showModal = false">关闭</button>
      </div>
    </Teleport>
  </div>
</template>

<style>
.animated-container {
  transform: translateX(0);  /* 不再影响模态框 */
}

.modal {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 9999;  /* z-index 现在按预期工作 */
}
</style>
```

## Z-Index 堆叠上下文问题

即使没有 transform/filter，深度嵌套的元素也会创建约束 z-index 的堆叠上下文:

```vue
<template>
  <div class="sidebar" style="z-index: 100; position: relative;">
    <div class="nested-component">
      <!-- 没有 Teleport: z-index: 9999 在侧边栏内约束 -->
      <div v-if="showDropdown" class="dropdown" style="z-index: 9999;">
        此下拉菜单可能被其他元素覆盖
      </div>
    </div>
  </div>

  <div class="content" style="z-index: 200; position: relative;">
    <!-- 这将覆盖下拉菜单，因为父元素 z-index 更高 -->
  </div>
</template>
```

**解决方案:**
```vue
<template>
  <div class="sidebar">
    <div class="nested-component">
      <Teleport to="body">
        <div v-if="showDropdown" class="dropdown">
          下拉菜单现在在 body 级别渲染 - 没有 z-index 约束
        </div>
      </Teleport>
    </div>
  </div>
</template>
```

## 何时使用 Teleport

| UI 元素 | 应该传送? | 原因 |
|------------|-----------------|--------|
| 全屏模态框 | 是 | 固定定位，需要转义堆叠上下文 |
| 工具提示 | 经常 | 可能需要转义 overflow: hidden 容器 |
| 下拉菜单 | 有时 | 取决于容器 overflow/定位 |
| 通知/提示 | 是 | 应该出现在所有内容之上 |
| 内联弹出窗口 | 通常不 | 相对于触发元素定位 |

## 参考
- [Vue.js Teleport - 基本用法](https://vuejs.org/guide/built-ins/teleport.html#basic-usage)
- [MDN - 堆叠上下文](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_positioned_layout/Understanding_z-index/Stacking_context)
