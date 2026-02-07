---
title: 为插槽提供后备内容
impact: LOW
impactDescription: 没有插槽后备的组件在未提供内容时可能渲染为空或损坏
type: best-practice
tags: [vue3, slots, fallback-content, component-design, reusability]
---

# 为插槽提供后备内容

**影响：低** - 在 `<slot>` 元素内提供合理的默认内容使组件更具弹性和用户友好性。当父组件未提供插槽内容时，后备内容会渲染。

## 任务清单

- [ ] 为可选插槽在插槽元素内添加后备内容
- [ ] 使用有意义的默认值，使其独立存在时也合理
- [ ] 考虑在国际化应用中为文本后备内容使用 i18n
- [ ] 记录哪些插槽有后备内容以及它们显示什么

**没有后备（脆弱）：**
```vue
<!-- SubmitButton.vue -->
<template>
  <button type="submit" class="btn-primary">
    <!-- 如果未提供内容则为空 -->
    <slot></slot>
  </button>
</template>
```

```vue
<!-- 没有内容的使用 - 渲染空按钮 -->
<SubmitButton />
<!-- 结果: <button type="submit" class="btn-primary"></button> -->
```

**有后备（弹性）：**
```vue
<!-- SubmitButton.vue -->
<template>
  <button type="submit" class="btn-primary">
    <slot>提交</slot>
  </button>
</template>
```

```vue
<!-- 没有内容的使用 - 显示默认值 -->
<SubmitButton />
<!-- 结果: <button type="submit" class="btn-primary">提交</button> -->

<!-- 有内容的使用 - 覆盖默认值 -->
<SubmitButton>保存更改</SubmitButton>
<!-- 结果: <button type="submit" class="btn-primary">保存更改</button> -->
```

## 带后备的命名插槽

```vue
<!-- Card.vue -->
<template>
  <div class="card">
    <header class="card-header">
      <slot name="header">
        <h3>卡片标题</h3>
      </slot>
    </header>

    <main class="card-body">
      <slot>
        <p>未提供内容。</p>
      </slot>
    </main>

    <footer class="card-footer">
      <slot name="footer">
        <button>关闭</button>
      </slot>
    </footer>
  </div>
</template>
```

## 复杂的后备内容

后备内容可以包含多个元素和逻辑：

```vue
<!-- UserAvatar.vue -->
<template>
  <div class="avatar-container">
    <slot name="image">
      <!-- 带条件逻辑的复杂后备 -->
      <img
        v-if="user?.avatar"
        :src="user.avatar"
        :alt="user.name"
      />
      <div v-else class="avatar-placeholder">
        {{ initials }}
      </div>
    </slot>
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  user: Object
})

const initials = computed(() => {
  if (!props.user?.name) return '?'
  return props.user.name
    .split(' ')
    .map(n => n[0])
    .join('')
    .toUpperCase()
})
</script>
```

## 何时使用后备

| 插槽用途 | 推荐使用后备？ | 示例 |
|--------------|----------------------|---------|
| 按钮文本 | 是 | "提交"、"点击这里" |
| 图标插槽 | 是 | 默认图标组件 |
| 模态框标题 | 是 | "对话框" |
| 必需内容区域 | 否 | 主内容插槽 |
| 仅用于自定义 | 可选 | 操作按钮插槽 |

## 参考
- [Vue.js 插槽 - 后备内容](https://vuejs.org/guide/components/slots.html#fallback-content)
