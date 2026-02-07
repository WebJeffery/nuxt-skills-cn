---
title: KeepAlive Include/Exclude 需要组件名称
impact: MEDIUM
impactDescription: include 和 exclude 属性与组件的 name 选项匹配，必须显式声明
type: gotcha
tags: [vue3, keepalive, component-name, include, exclude, sfc]
---

# KeepAlive Include/Exclude 需要组件名称

**影响: 中等** - 在 KeepAlive 上使用 `include` 或 `exclude` 属性时，匹配是针对组件的 `name` 选项进行的。没有显式名称的组件将不会匹配，缓存行为将不符合预期。

## 任务清单

- [ ] 在使用 include/exclude 的组件上声明 `name` 选项
- [ ] 在 `<script setup>` 中使用 `defineOptions({ name: '...' })`
- [ ] 注意: 自 Vue 3.2.34 起，SFC 文件名会自动推断为名称
- [ ] 验证名称完全匹配（区分大小写）

## 问题

```vue
<!-- App.vue -->
<template>
  <KeepAlive include="TabA,TabB">
    <component :is="currentTab" />
  </KeepAlive>
</template>
```

```vue
<!-- TabA.vue - 没有声明名称 -->
<script setup>
// 没有 name 选项 - 不会匹配 include 中的 "TabA"！
const count = ref(0)
</script>

<template>
  <div>Count: {{ count }}</div>
</template>
```

**结果:** TabA 不会被缓存，因为它没有 `name` 选项来匹配。

## 解决方案

### 解决方案 1: 使用 defineOptions (Vue 3.3+)

```vue
<!-- TabA.vue -->
<script setup>
import { ref } from 'vue'

defineOptions({
  name: 'TabA'  // 现在匹配 include="TabA"
})

const count = ref(0)
</script>

<template>
  <div>Count: {{ count }}</div>
</template>
```

### 解决方案 2: 双脚本块

```vue
<!-- TabA.vue -->
<script>
export default {
  name: 'TabA'
}
</script>

<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>

<template>
  <div>Count: {{ count }}</div>
</template>
```

### 解决方案 3: 依赖自动推断 (Vue 3.2.34+)

自 Vue 3.2.34 起，使用 `<script setup>` 的 SFC 会自动从文件名推断其名称：

```
TabA.vue  -> name 是 "TabA"
UserProfile.vue -> name 是 "UserProfile"
my-component.vue -> name 是 "my-component" (保留 kebab-case)
```

```vue
<!-- 自 Vue 3.2.34+ 起自动工作 -->
<!-- 文件: TabA.vue -->
<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>

<template>
  <div>Count: {{ count }}</div>
</template>
```

```vue
<!-- App.vue -->
<template>
  <!-- 匹配文件名 "TabA" -->
  <KeepAlive include="TabA">
    <component :is="currentTab" />
  </KeepAlive>
</template>
```

## 常见错误

### 错误 1: 名称不匹配

```vue
<script>
export default {
  name: 'tab-a'  // 小写 kebab-case
}
</script>
```

```vue
<template>
  <!-- 不匹配 - include 使用 PascalCase -->
  <KeepAlive include="TabA">
    <component :is="currentTab" />
  </KeepAlive>
</template>
```

**修复:** 确保名称完全匹配：

```vue
<KeepAlive include="tab-a">  <!-- 或者将组件名称改为 'TabA' -->
```

### 错误 2: 没有名称的动态组件

```vue
<script setup>
import { defineAsyncComponent } from 'vue'

// 异步组件 - 可能没有名称！
const AsyncTab = defineAsyncComponent(() => import('./Tab.vue'))
</script>
```

**修复:** 确保导入的组件声明了名称。

### 错误 3: 在 Options API 中使用 Props

```vue
<script>
export default {
  // 这不会设置组件名称！
  props: ['name']  // 'name' prop 与组件名称选项不同
}
</script>
```

## 调试名称问题

检查 Vue 为您的组件看到的名称：

```vue
<script setup>
import { getCurrentInstance, onMounted } from 'vue'

onMounted(() => {
  const instance = getCurrentInstance()
  console.log('Component name:', instance?.type?.name)
  console.log('Component __name:', instance?.type?.__name)
})
</script>
```

## 使用不同的匹配格式

```vue
<template>
  <!-- 字符串（逗号分隔） -->
  <KeepAlive include="TabA,TabB,TabC">
    <component :is="current" />
  </KeepAlive>

  <!-- 正则表达式 -->
  <KeepAlive :include="/^Tab/">
    <component :is="current" />
  </KeepAlive>

  <!-- 数组 -->
  <KeepAlive :include="['TabA', 'TabB']">
    <component :is="current" />
  </KeepAlive>
</template>
```

## 关键点

1. **名称必须完全匹配** - 区分大小写的字符串匹配
2. **Vue 3.2.34+ 自动推断名称** - 对于 `<script setup>` SFC，从文件名推断
3. **使用 `defineOptions`** - 在 `<script setup>` 中设置名称的简洁方法
4. **使用 getCurrentInstance 调试** - 检查 Vue 实际看到的名称
5. **多种格式可用** - include/exclude 支持字符串、正则表达式或数组

## 参考
- [Vue.js KeepAlive - Include/Exclude](https://vuejs.org/guide/built-ins/keep-alive.html#include-exclude)
- [Vue.js SFC `<script setup>`](https://vuejs.org/api/sfc-script-setup.html)
- [defineOptions 文档](https://vuejs.org/api/sfc-script-setup.html#defineoptions)
