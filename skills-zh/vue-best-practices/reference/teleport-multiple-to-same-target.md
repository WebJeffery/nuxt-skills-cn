---
title: 多个传送传送到同一目标按顺序追加
impact: LOW
impactDescription: 当多个 Teleport 组件针对同一元素时，它们按声明顺序追加
type: capability
tags: [vue3, teleport, modal, order]
---

# 多个传送传送到同一目标按顺序追加

**影响: LOW** - 多个 `<Teleport>` 组件可以将内容挂载到同一目标元素。内容按声明顺序追加 - 后面的传送在 DOM 中出现在前面的传送之后。

这对于通知堆栈、模态框分层和工具提示系统很有用。

## 任务清单

- [ ] 对相关 UI 使用一致的传送目标（例如，所有模态框到 `#modals`）
- [ ] 利用自然堆叠顺序或使用 z-index 进行层控制
- [ ] 考虑通知/模态框管理系统用于复杂情况

**基本示例:**
```vue
<template>
  <Teleport to="#notifications">
    <div class="notification">第一个通知</div>
  </Teleport>

  <Teleport to="#notifications">
    <div class="notification">第二个通知</div>
  </Teleport>
</template>
```

**生成的 DOM:**
```html
<div id="notifications">
  <div class="notification">第一个通知</div>
  <div class="notification">第二个通知</div>
</div>
```

## 实际示例: 通知堆栈

```vue
<!-- NotificationSystem.vue -->
<template>
  <Teleport to="#notifications">
    <TransitionGroup name="notification" tag="div" class="notification-stack">
      <div
        v-for="notification in notifications"
        :key="notification.id"
        class="notification"
        :class="notification.type"
      >
        {{ notification.message }}
        <button @click="dismiss(notification.id)">×</button>
      </div>
    </TransitionGroup>
  </Teleport>
</template>

<script setup>
import { ref } from 'vue'

const notifications = ref([])

function notify(message, type = 'info') {
  const id = Date.now()
  notifications.value.push({ id, message, type })
  setTimeout(() => dismiss(id), 5000)
}

function dismiss(id) {
  notifications.value = notifications.value.filter(n => n.id !== id)
}

defineExpose({ notify })
</script>
```

## 模态框分层

当模态框打开其他模态框时，它们自然堆叠:

```vue
<!-- ParentModal.vue -->
<template>
  <Teleport to="#modals">
    <div v-if="visible" class="modal">
      <p>父模态框</p>
      <button @click="showChild = true">打开子模态框</button>
    </div>
  </Teleport>

  <!-- 子模态框在父模态框之后传送 -->
  <Teleport to="#modals">
    <div v-if="showChild" class="modal child-modal">
      <p>子模态框（出现在顶部）</p>
      <button @click="showChild = false">关闭</button>
    </div>
  </Teleport>
</template>

<style>
.modal {
  position: fixed;
  /* 后面的模态框自然具有更高的堆叠顺序 */
}
</style>
```

## 使用 z-index 控制顺序

对于堆叠的显式控制:

```vue
<template>
  <Teleport to="#modals">
    <div v-if="showBase" class="modal" style="z-index: 100;">
      基础模态框
    </div>
  </Teleport>

  <Teleport to="#modals">
    <div v-if="showConfirm" class="modal" style="z-index: 200;">
      确认对话框（始终在顶部）
    </div>
  </Teleport>
</template>
```

## 跨组件传送

来自应用程序不同部分的组件可以传送到同一目标:

```vue
<!-- Header.vue -->
<template>
  <Teleport to="#global-alerts">
    <div v-if="headerAlert" class="alert">头部警告</div>
  </Teleport>
</template>

<!-- Footer.vue -->
<template>
  <Teleport to="#global-alerts">
    <div v-if="footerAlert" class="alert">页脚警告</div>
  </Teleport>
</template>

<!-- 两者都出现在 #global-alerts 中，按挂载时间排序 -->
```

## 参考
- [Vue.js Teleport - 同一目标上的多个传送](https://vuejs.org/guide/built-ins/teleport.html#multiple-teleports-on-the-same-target)
