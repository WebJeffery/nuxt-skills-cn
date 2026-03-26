---
name: app-development
description: Vue/Nuxt/UnoCSS 应用程序约定。用于构建 Web 应用程序、在 Vite 和 Nuxt 之间选择或编写 Vue 组件时。
---

# 应用开发

## 框架选择

| 使用场景 | 选择 |
|----------|--------|
| SPA、仅客户端、库游乐场 | Vite + Vue |
| SSR、SSG、SEO 关键、基于文件的路由、API 路由 | Nuxt |

## Vue 约定

| 约定 | 偏好 |
|------------|------------|
| 脚本语法 | 始终 `<script setup lang="ts">` |
| 状态 | 优先使用 `shallowRef()` 而不是 `ref()` |
| 对象 | 使用 `ref()`，避免 `reactive()` |
| 样式 | UnoCSS |
| 工具 | VueUse |

### Props 和 Emits

```vue
<script setup lang="ts">
interface Props {
  title: string
  count?: number
}

interface Emits {
  (e: 'update', value: number): void
  (e: 'close'): void
}

const props = withDefaults(defineProps<Props>(), {
  count: 0,
})

const emit = defineEmits<Emits>()
</script>
```
