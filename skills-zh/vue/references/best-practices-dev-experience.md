---
name: best-practices-dev-experience
description: Vue 3 开发体验优化最佳实践
---

# 开发体验优化

## TypeScript 集成

### 类型定义

```ts
// types/index.ts
export interface User {
  id: number
  name: string
  email: string
}

export interface ApiResponse<T> {
  data: T
  message: string
}
```

### 组件 Props 类型

```vue
<script setup lang="ts">
import type { User } from '@/types'

const props = defineProps<{
  user: User
  loading?: boolean
}>()
</script>
```

### 组合式函数类型

```ts
// composables/useUser.ts
import { ref } from 'vue'
import type { Ref } from 'vue'

export function useUser(id: Ref<number> | number) {
  const user = ref<User | null>(null)
  const loading = ref(false)
  const error = ref<Error | null>(null)

  return {
    user,
    loading,
    error
  }
}
```

## 代码组织

### 目录结构

```
src/
├── assets/          # 静态资源
├── components/      # 通用组件
│   ├── base/        # 基础组件
│   └── features/    # 功能组件
├── composables/     # 组合式函数
├── layouts/         # 布局组件
├── pages/           # 页面组件
├── router/          # 路由配置
├── stores/          # 状态管理
├── types/           # TypeScript 类型
├── utils/           # 工具函数
└── views/           # 视图组件
```

### 组件命名

```vue
<!-- 组件文件名使用 PascalCase -->
<!-- UserProfile.vue -->

<script setup lang="ts">
defineOptions({
  name: 'UserProfile'
})
</script>
```

### 组合式函数组织

```ts
// composables/index.ts
export { useUser } from './useUser'
export { useAuth } from './useAuth'
export { useFetch } from './useFetch'
```

## 开发工具

### Vue DevTools

```ts
// main.ts
import { createApp } from 'vue'
import App from './App.vue'

const app = createApp(App)

// 开发环境启用 DevTools
if (import.meta.env.DEV) {
  app.config.devtools = true
}
```

### 环境变量

```ts
// .env.development
VITE_API_BASE_URL=http://localhost:3000/api

// .env.production
VITE_API_BASE_URL=https://api.example.com
```

```ts
// 使用环境变量
const apiUrl = import.meta.env.VITE_API_BASE_URL
```

### ESLint 配置

```js
// .eslintrc.js
module.exports = {
  extends: [
    'plugin:vue/vue3-recommended',
    '@vue/typescript/recommended'
  ],
  rules: {
    'vue/multi-word-component-names': 'off',
    '@typescript-eslint/no-explicit-any': 'warn'
  }
}
```

## 热模块替换 (HMR)

### Vite HMR

```vue
<script setup lang="ts">
import { onMounted } from 'vue'

onMounted(() => {
  console.log('组件已挂载')
})

// HMR 时保留状态
if (import.meta.hot) {
  import.meta.hot.accept(() => {
    console.log('组件已更新')
  })
}
</script>
```

## 代码片段

### VS Code 片段

```json
{
  "Vue 3 Script Setup": {
    "prefix": "vue3",
    "body": [
      "<script setup lang=\"ts\">",
      "import { ref } from 'vue'",
      "",
      "const count = ref(0)",
      "</script>",
      "",
      "<template>",
      "  <div>{{ count }}</div>",
      "</template>"
    ]
  }
}
```

## 测试

### 单元测试

```ts
// tests/unit/Counter.spec.ts
import { describe, it, expect } from 'vitest'
import { mount } from '@vue/test-utils'
import Counter from '@/components/Counter.vue'

describe('Counter', () => {
  it('renders count', () => {
    const wrapper = mount(Counter, {
      props: { count: 5 }
    })
    expect(wrapper.text()).toContain('5')
  })
})
```

### 组件测试

```ts
// tests/components/UserProfile.spec.ts
import { describe, it, expect } from 'vitest'
import { mount } from '@vue/test-utils'
import UserProfile from '@/components/UserProfile.vue'

describe('UserProfile', () => {
  it('displays user name', async () => {
    const wrapper = mount(UserProfile, {
      props: {
        user: { id: 1, name: 'John', email: 'john@example.com' }
      }
    })
    expect(wrapper.text()).toContain('John')
  })
})
```

## 文档

### 组件文档

```vue
<script setup lang="ts">
/**
 * UserProfile 组件
 * 
 * @description 显示用户资料的组件
 * 
 * @example
 * <UserProfile :user="user" />
 */
const props = defineProps<{
  /**
   * 用户对象
   */
  user: {
    id: number
    name: string
    email: string
  }
  /**
   * 是否显示邮箱
   */
  showEmail?: boolean
}>()
</script>
```

## 最佳实践总结

1. **TypeScript 集成**：使用类型定义、组件 Props 类型、组合式函数类型
2. **代码组织**：合理的目录结构、组件命名、组合式函数组织
3. **开发工具**：Vue DevTools、环境变量、ESLint 配置
4. **热模块替换**：利用 HMR 保持开发状态
5. **代码片段**：创建 VS Code 片段提高开发效率
6. **测试**：单元测试和组件测试
7. **文档**：组件文档和注释

<!--
Source references:
- https://vuejs.org/guide/scaling-up/tooling
- https://vuejs.org/guide/typescript/overview
-->
