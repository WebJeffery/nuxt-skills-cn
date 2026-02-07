---
title: 模板 Ref 解包仅适用于顶级属性
impact: MEDIUM
impactDescription: 模板表达式中的嵌套 refs 渲染为 [object Object] 而不是它们的值
type: capability
tags: [vue3, reactivity, ref, template, unwrapping]
---

# 模板 Ref 解包仅适用于顶级属性

**影响：中** - Vue 只会自动解包作为模板渲染上下文中顶级属性的 refs。嵌套 refs（对象中的 refs）在表达式中不会被解包，导致 `[object Object]` 渲染或计算错误。

当开发人员将 refs 存储在响应式对象或普通对象中并尝试在模板表达式中使用它们（如 `{{ object.count + 1 }}`）时，这个注意事项会让他们绊倒。

## 任务清单

- [ ] 将 refs 保持在 setup 返回或 script setup 的顶级
- [ ] 在表达式中使用之前将嵌套 refs 解构为顶级变量
- [ ] 注意文本插值 `{{ object.ref }}` 会解包，但表达式 `{{ object.ref + 1 }}` 不会
- [ ] 考虑重构数据以避免模板中的嵌套 refs

**错误：**
```vue
<script setup>
import { ref } from 'vue'

const count = ref(0)
const object = { id: ref(1) }
</script>

<template>
  <!-- 错误：表达式中的嵌套 ref - 不会解包 -->
  <p>ID + 1 = {{ object.id + 1 }}</p>
  <!-- 渲染："ID + 1 = [object Object]1" -->

  <!-- 令人惊讶的是，普通插值有效 -->
  <p>ID = {{ object.id }}</p>
  <!-- 渲染："ID = 1"（解包因为它是最终表达式）-->
</template>
```

**正确：**
```vue
<script setup>
import { ref } from 'vue'

const count = ref(0)
const object = { id: ref(1) }

// 解决方案 1：解构到顶级
const { id } = object
</script>

<template>
  <!-- 正确：顶级 ref 在所有表达式中解包 -->
  <p>Count + 1 = {{ count + 1 }}</p>
  <!-- 渲染："Count + 1 = 1" -->

  <!-- 正确：解构的 ref 现在是顶级 -->
  <p>ID + 1 = {{ id + 1 }}</p>
  <!-- 渲染："ID + 1 = 2" -->
</template>
```

```vue
<script setup>
import { ref, computed } from 'vue'

const object = { id: ref(1) }

// 解决方案 2：对派生值使用 computed
const idPlusOne = computed(() => object.id.value + 1)
</script>

<template>
  <!-- 正确：Computed 处理 .value 访问 -->
  <p>ID + 1 = {{ idPlusOne }}</p>
</template>
```

```vue
<script setup>
import { reactive } from 'vue'

// 解决方案 3：改用响应式对象（响应式中的 refs 自动解包）
const object = reactive({ id: 1 })
</script>

<template>
  <!-- 正确：普通响应式属性在表达式中工作 -->
  <p>ID + 1 = {{ object.id + 1 }}</p>
</template>
```

```javascript
// 为什么会发生这种情况：
// - 模板编译只向顶级标识符添加 .value
// - {{ count + 1 }} 编译为：count.value + 1
// - {{ object.id + 1 }} 编译为：object.id + 1（没有添加 .value！）
// - 普通 {{ object.id }} 对显示目的有特殊处理
```

## 参考
- [Vue.js 响应式基础 - 在模板中解包时的注意事项](https://vuejs.org/guide/essentials/reactivity-fundamentals.html#caveat-when-unwrapping-in-templates)
