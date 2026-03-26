---
title: 单文件组件 (SFC) 最佳实践
impact: MEDIUM
impactDescription: SFC 是 Vue 的核心特性;遵循最佳实践可确保代码可维护和性能优化
type: best-practice
tags: [vue3, sfc, script-setup, style-scoped, organization]
---

# 单文件组件 (SFC) 最佳实践

**影响: MEDIUM** - 单文件组件是 Vue 的核心特性。遵循最佳实践可确保代码可维护、类型安全和性能优化。

## 任务列表

- 使用 `<script setup>` 语法
- 为组件 props 和 emits 提供类型
- 使用 `defineProps` 和 `defineEmits`
- 使用 scoped 样式避免样式污染
- 按逻辑组织组件代码
- 避免在模板中使用复杂表达式

## 使用 script setup

**正确:**
```vue
<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}
</script>

<template>
  <button @click="increment">{{ count }}</button>
</template>
```

**错误:**
```vue
<script>
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)

    function increment() {
      count.value++
    }

    return { count, increment }
  }
}
</script>
```

## 类型化 Props 和 Emits

**正确:**
```vue
<script setup lang="ts">
interface Props {
  title: string
  count?: number
}

interface Emits {
  (e: 'update', value: number): void
  (e: 'delete', id: string): void
}

const props = defineProps<Props>()
const emit = defineEmits<Emits>()
</script>
```

## 使用 Scoped 样式

**正确:**
```vue
<template>
  <div class="container">
    <h1 class="title">{{ title }}</h1>
  </div>
</template>

<style scoped>
.container {
  padding: 16px;
}

.title {
  font-size: 24px;
}
</style>
```

**错误:**
```vue
<template>
  <div class="container">
    <h1 class="title">{{ title }}</h1>
  </div>
</template>

<style>
.container {
  padding: 16px;
}

.title {
  font-size: 24px;
}
</style>
```

## 组织组件代码

**推荐顺序:**
1. `<script setup>` - 组件逻辑
2. `<template>` - 组件模板
3. `<style scoped>` - 组件样式

在 `<script setup>` 内部:
1. 导入
2. Props 和 Emits 定义
3. 响应式状态
4. 计算属性
5. 方法
6. 生命周期钩子

**正确:**
```vue
<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import type { User } from '@/types'

// Props 和 Emits
interface Props {
  userId: string
}

const props = defineProps<Props>()

// 响应式状态
const user = ref<User | null>(null)
const loading = ref(false)

// 计算属性
const displayName = computed(() => user.value?.name || '未知用户')

// 方法
async function fetchUser() {
  loading.value = true
  try {
    user.value = await api.getUser(props.userId)
  } finally {
    loading.value = false
  }
}

// 生命周期
onMounted(fetchUser)
</script>
```

## 避免复杂表达式

**错误:**
```vue
<template>
  <div>
    {{ items.filter(item => item.active).map(item => item.name).join(', ') }}
  </div>
</template>
```

**正确:**
```vue
<script setup>
import { computed } from 'vue'

const activeNames = computed(() =>
  items.value
    .filter(item => item.active)
    .map(item => item.name)
    .join(', ')
)
</script>

<template>
  <div>{{ activeNames }}</div>
</template>
```

## 最佳实践

1. **组件命名:**
   - 使用 PascalCase 命名组件文件
   - 组件名应该具有描述性
   - 避免通用名称如 `Component.vue`

2. **Props 定义:**
   - 始终为 props 提供类型
   - 为可选 props 提供默认值
   - 使用验证函数进行复杂验证

3. **样式组织:**
   - 使用 scoped 样式避免污染
   - 使用 CSS Modules 进行更复杂的样式需求
   - 考虑使用 CSS-in-JS 库进行动态样式

4. **性能优化:**
   - 使用 `v-once` 优化静态内容
   - 使用 `v-memo` 优化昂贵计算
   - 避免不必要的响应式
