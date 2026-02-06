# 代码分割与懒加载完整指南

## 什么是代码分割

代码分割(Code Splitting)是将代码拆分成多个 bundle,按需加载的技术,可以显著减少初始加载体积。

## 为什么需要代码分割

**问题:**
```typescript
// ❌ 所有代码打包成一个文件
main.js (2MB)
├─ vue-router (200KB)
├─ lodash (500KB)
├─ chart.js (300KB)
├─ 首页代码 (100KB)
├─ 关于页 (50KB)
├─ 管理页 (800KB)
└─ ...

// 结果: 首页需要下载所有代码
```

**解决:**
```typescript
// ✅ 按需加载
main.js (100KB) - 首页代码
about.chunk.js (50KB) - 关于页
admin.chunk.js (800KB) - 管理页
vendor-charts.js (300KB) - 图表库
vendor-lodash.js (500KB) - lodash

// 结果: 首页只下载必要的代码
```

## Nuxt 自动代码分割

### 1. 路由级分割 (自动)

```bash
pages/
├─ index.vue       # 自动分割
├─ about.vue       # 自动分割
└─ profile/
   └─ [id].vue     # 自动分割
```

**生成的 chunks:**
```
index.js
about.js
profile-[id].js
```

### 2. 组件级分割 (手动)

```vue
<template>
  <div>
    <!-- 自动导入 Lazy 前缀组件 -->
    <LazyHomePageHero v-if="showHero" />
    <LazyHomePageFeatures />
    <LazyHomePageTestimonials />
  </div>
</template>

<script setup lang="ts">
// Nuxt 自动扫描 ~/components 目录
// 任何组件都可以使用 Lazy 前缀懒加载
const showHero = ref(true)
</script>
```

### 3. 布局分割 (自动)

```bash
layouts/
├─ default.vue     # 自动分割
├─ admin.vue       # 自动分割
└── auth.vue       # 自动分割
```

## 手动代码分割

### 1. defineAsyncComponent

```vue
<script setup lang="ts">
import { defineAsyncComponent } from 'vue'

// 基础用法
const AsyncModal = defineAsyncComponent(() =>
  import('~/components/Modal.vue')
)

// 带加载状态
const AsyncChart = defineAsyncComponent({
  loader: () => import('~/components/Chart.vue'),
  loadingComponent: Spinner,
  errorComponent: ErrorDisplay,
  delay: 200,
  timeout: 3000,
})

// 条件加载
const AdminPanel = process.client
  ? defineAsyncComponent(() => import('~/components/AdminPanel.vue'))
  : () => null
</script>

<template>
  <AsyncModal v-if="showModal" />
  <AsyncChart :data="chartData" />
  <AdminPanel v-if="isAdmin" />
</template>
```

### 2. 动态导入

```typescript
// 函数级懒加载
const formatDate = (date: Date) => {
  return import('~/utils/date')
    .then(m => m.format(date))
}

// 或使用 async/await
const formatDate = async (date: Date) => {
  const { format } = await import('~/utils/date')
  return format(date)
}

// 事件处理
const openModal = async () => {
  const { default: Modal } = await import('~/components/Modal.vue')
  // 显示模态框
}
```

### 3. 条件导入

```typescript
// 根据环境导入
const utils = process.client
  ? await import('~/utils/browser')
  : await import('~/utils/server')

// 根据功能导入
if (useFeature('charts')) {
  const { initChart } = await import('~/utils/charts')
  initChart()
}
```

## 第三方库优化

### 1. 按需导入

```typescript
// ❌ 导入整个 lodash
import { debounce, throttle, cloneDeep } from 'lodash'
// bundle: +500KB

// ✅ 按需导入
import debounce from 'lodash/debounce'
import throttle from 'lodash/throttle'
import cloneDeep from 'lodash/cloneDeep'
// bundle: +15KB

// ✅ 使用 lodash-es (Tree Shaking)
import { debounce, throttle, cloneDeep } from 'lodash-es'
// bundle: +5KB
```

### 2. 替代库

```typescript
// ❌ moment.js (67KB)
import moment from 'moment'
moment().format('YYYY-MM-DD')

// ✅ date-fns (Tree Shaking)
import { format } from 'date-fns'
format(new Date(), 'yyyy-MM-dd')

// ✅ Day.js (2KB)
import dayjs from 'dayjs'
dayjs().format('YYYY-MM-DD')
```

### 3. UI 库按需导入

```typescript
// ❌ 导入整个 Element Plus
import ElementPlus from 'element-plus'
// bundle: +500KB

// ✅ 按需导入
import { ElButton, ElInput } from 'element-plus'
// bundle: +50KB
```

## 高级分割策略

### 1. 路由预加载

```typescript
// 预加载下一个可能访问的页面
onMounted(() => {
  const link = document.createElement('link')
  link.rel = 'prefetch'
  link.href = '/_nuxt/pages/about.js'
  document.head.appendChild(link)
})

// 或使用 Nuxt 的 prefetch 链接
<NuxtLink to="/about" prefetch>
  关于我们
</NuxtLink>
```

### 2. 分组策略

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  vite: {
    build: {
      rollupOptions: {
        output: {
          manualChunks: {
            // Vue 生态
            'vue-vendor': ['vue', 'vue-router', 'pinia'],

            // UI 库
            'ui-vendor': ['@nuxtjs/color-mode'],

            // 工具库
            'utils': ['lodash-es', 'date-fns'],

            // 图表
            'charts': ['chart.js'],
          },
        },
      },
    },
  },
})
```

### 3. 依赖分析

```bash
# 分析包体积
npm run build -- --analyze

# 使用 webpack-bundle-analyzer
npx nuxi analyze

# 查看 vendor chunks
npx nuxi build --debug
```

## 实际应用案例

### 案例 1: 管理后台

```vue
<template>
  <div class="admin-panel">
    <AdminSidebar />

    <!-- 只有激活时加载 -->
    <component :is="activeTabComponent" />
  </div>
</template>

<script setup lang="ts">
const activeTab = ref('dashboard')

// 懒加载各个模块
const modules = {
  dashboard: defineAsyncComponent(() =>
    import('~/components/admin/Dashboard.vue')
  ),
  users: defineAsyncComponent(() =>
    import('~/components/admin/Users.vue')
  ),
  settings: defineAsyncComponent(() =>
    import('~/components/admin/Settings.vue')
  ),
}

const activeTabComponent = computed(() => modules[activeTab.value])
</script>
```

### 案例 2: 图表组件

```vue
<template>
  <div>
    <button @click="showChart = true">显示图表</button>

    <LazyChart
      v-if="showChart"
      :data="chartData"
    />
  </div>
</template>

<script setup lang="ts">
// 仅在需要时加载大型图表库
const showChart = ref(false)
const chartData = ref([])
</script>
```

### 案例 3: 富文本编辑器

```vue
<template>
  <div>
    <button @click="enableEditing">编辑内容</button>

    <LazyRichTextEditor
      v-if="isEditing"
      v-model="content"
      @save="saveContent"
    />
  </div>
</template>

<script setup lang="ts>
// 富文本编辑器通常很大 (200KB+)
// 只在编辑时加载
const isEditing = ref(false)
const content = ref('')

const enableEditing = () => {
  isEditing.value = true
}
</script>
```

## Web Workers 分割

```typescript
// workers/heavy-task.worker.ts
export default defineWorkerHandler(() => {
  onmessage = (e) => {
    const result = heavyComputation(e.data)
    postMessage(result)
  }
})

// components/TaskRunner.vue
<script setup lang="ts">
const runTask = async () => {
  const worker = new Worker(
    new URL('~/workers/heavy-task.worker.ts', import.meta.url),
    { type: 'module' }
  )

  worker.postMessage(largeDataSet)
  worker.onmessage = (e) => {
    console.log('计算完成:', e.data)
  }
}
</script>
```

## 性能监控

```typescript
// plugins/performance.client.ts
export default defineNuxtPlugin((nuxtApp) => {
  if (process.env.NODE_ENV === 'development') {
    // 监控组件加载时间
    nuxtApp.hook('component:recover', (error) => {
      console.error('组件加载失败:', error)
    })

    // 监控异步组件加载
    const originalDefineAsyncComponent = defineAsyncComponent
    // Override to add logging
  }
})
```

## 最佳实践

### ✅ 推荐做法

1. **首页优先**
   ```typescript
   // 首页只加载必需代码
   // 其他页面按需加载
   ```

2. **Lazy 组件**
   ```vue
   <LazyHeavyComponent />
   ```

3. **路由级分割**
   ```bash
   pages/
   ├── index.vue    # 首页
   ├── about.vue    # 自动分割
   └── admin.vue    # 自动分割
   ```

4. **第三方库按需加载**
   ```typescript
   import debounce from 'lodash/debounce'
   ```

### ❌ 避免做法

1. **过度分割**
   ```typescript
   // ❌ 太小的 chunk 增加请求开销
   // 每个 chunk 至少 20KB
   ```

2. **延迟加载首屏内容**
   ```vue
   // ❌ 首屏内容不应懒加载
   <LazyHeroSection />
   ```

3. **忽略移动端**
   ```typescript
   // ✅ 考虑移动网络
   const chunkSize = isMobile ? 'small' : 'large'
   ```

## 工具推荐

### 1. bundlealyzer
```bash
npm install --save-dev rollup-plugin-visualizer
```

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  vite: {
    build: {
      rollupOptions: {
        plugins: [
          visualizer({
            open: true,
            gzipSize: true,
            brotliSize: true,
          })
        ]
      }
    }
  }
})
```

### 2. webpack-bundle-analyzer
```bash
npm install --save-dev @nuxt/devtools
```

### 3. Lighthouse
```bash
npx lighthouse https://example.com --view
```

## 参考资源

- [Nuxt Code Splitting](https://nuxt.com/docs/guide/directory-struct/components)
- [Vue defineAsyncComponent](https://vuejs.org/api/general.html#defineasynccomponent)
- [Webpack Code Splitting](https://webpack.js.org/guides/code-splitting/)
- [VITE Code Splitting](https://vitejs.dev/guide/build.html#chunk-rebuild-strategy)
