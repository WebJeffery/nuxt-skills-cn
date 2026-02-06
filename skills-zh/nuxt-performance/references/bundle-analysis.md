# 包体积分析与优化完整指南

## 为什么包体积分析很重要

**影响:**
- 下载时间: 2MB 包需要 10s+ (3G 网络)
- 解析时间: 大包解析执行慢
- 内存占用: 影响运行时性能

**目标:**
- 首屏 JS: < 200KB (gzipped)
- 总 JS: < 500KB (gzipped)
- 单个 chunk: < 100KB

## 分析工具

### 1. Nuxt DevTools

```bash
npm install --save-dev @nuxt/devtools
```

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  devtools: { enabled: true },
})
```

访问 `http://localhost:3000/__nuxt` 查看:
- 包体积分析
- 组件树
- 路由信息

### 2. build --analyze

```bash
# 分析构建产物
npm run build -- --analyze

# 或使用 nuxi
npx nuxi analyze
```

生成可视化报告:
```
.nuxt/analyze/
├── client.html
└── server.html
```

### 3. rollup-plugin-visualizer

```bash
npm install --save-dev rollup-plugin-visualizer
```

```typescript
// nuxt.config.ts
import { visualizer } from 'rollup-plugin-visualizer'

export default defineNuxtConfig({
  vite: {
    plugins: [
      visualizer({
        open: true,
        gzipSize: true,
        brotliSize: true,
      })
    ]
  }
})
```

### 4. webpack-bundle-analyzer

```bash
npm install --save-dev @nuxt/devtools
```

DevTools 内置 bundle analyzer。

## 包体积分析

### 1. 识别大型依赖

```bash
# 构建并分析
npm run build

# 查看输出
✓ Built in 5.23s
Client: 187 KB / 84 KB (gzip)
Server: 145 KB / 65 KB (gzip)
```

**常见大型依赖:**
- `lodash`: ~500KB
- `moment.js`: ~67KB
- `chart.js`: ~300KB
- `@element-plus`: ~500KB
- `ant-design-vue`: ~1MB

### 2. 分析 Chunk

```javascript
// 查看 .output/public/_nuxt/
index.js           // 主入口 (50KB)
app.js             // 应用代码 (100KB)
vendor.js          // 第三方库 (200KB)
pages/index.js     // 首页 (30KB)
pages/about.js     // 关于页 (20KB)
```

### 3. Tree Shaking 分析

```typescript
// 检查未使用的代码
// nuxt.config.ts
export default defineNuxtConfig({
  build: {
    analyze: true,
  },
})
```

## 优化策略

### 1. 移除未使用的依赖

```bash
# 查找未使用的包
npx depcheck

# 输出示例
Unused dependencies
* @types/node
* lodash-es

Unused devDependencies
* @types/jest
```

### 2. 按需导入

```typescript
// ❌ 导入整个 lodash
import { map, filter, sortBy } from 'lodash'
// bundle: +500KB

// ✅ 按需导入
import map from 'lodash/map'
import filter from 'lodash/filter'
import sortBy from 'lodash/sortBy'
// bundle: +15KB

// ✅ 使用 lodash-es
import { map, filter, sortBy } from 'lodash-es'
// bundle: +5KB
```

### 3. 替换大型库

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

### 4. 代码分割

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

### 5. 延迟加载

```vue
<script setup lang="ts">
// ❌ 立即加载大型库
import Chart from 'chart.js/auto'

// ✅ 按需加载
const loadChart = async () => {
  const { default: Chart } = await import('chart.js/auto')
  // 使用 Chart
}
</script>
```

## 常见场景优化

### 1. 管理后台

```typescript
// pages/admin/index.vue
<script setup lang="ts">
// 懒加载管理组件
const Dashboard = defineAsyncComponent(() =>
  import('~/components/admin/Dashboard.vue')
)

const Users = defineAsyncComponent(() =>
  import('~/components/admin/Users.vue')
)

const Settings = defineAsyncComponent(() =>
  import('~/components/admin/Settings.vue')
)
</script>
```

### 2. 富文本编辑器

```vue
<script setup lang="ts>
const showEditor = ref(false)

// 只在编辑时加载
const loadEditor = async () => {
  const { default: Editor } = await import('~/components/Editor.vue')
  return Editor
}

watch(showEditor, async (val) => {
  if (val) {
    await loadEditor()
  }
})
</script>
```

### 3. 图表组件

```vue
<script setup lang="ts>
const ChartComponent = defineAsyncComponent({
  loader: () => import('~/components/Chart.vue'),
  loadingComponent: () => h('div', 'Loading chart...'),
  delay: 200,
  timeout: 3000,
})
</script>
```

## 包体积优化技巧

### 1. 生产优化

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  // 生产环境移除 console
  sourcemap: {
    client: false,
    server: false,
  },

  // 压缩代码
  vite: {
    build: {
      minify: 'terser',
      terserOptions: {
        compress: {
          drop_console: true,
          drop_debugger: true,
        },
      },
    },
  },
})
```

### 2. Tree Shaking

```typescript
// 只导入需要的函数
import { debounce } from 'lodash-es'
// 而不是 import _ from 'lodash'

// 使用 ES 模块
import { format } from 'date-fns'
// 而不是 const dateFns = require('date-fns')
```

### 3. 副作用标记

```json
// package.json
{
  "sideEffects": [
    "*.css",
    "*.vue"
  ]
}
```

### 4. 外部化

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  vite: {
    build: {
      rollupOptions: {
        external: ['vue', 'vue-router'],
        output: {
          globals: {
            vue: 'Vue',
            'vue-router': 'VueRouter',
          },
        },
      },
    },
  },
})
```

## 监控包体积

### 1. 自动检查

```typescript
// scripts/check-bundle-size.ts
import { readFileSync } from 'fs'
import { join } from 'path'

const MAX_SIZE = 200 * 1024 // 200KB

function checkSize(filePath: string) {
  const content = readFileSync(filePath)
  const size = content.length

  if (size > MAX_SIZE) {
    console.error(`❌ ${filePath} exceeds ${MAX_SIZE} bytes`)
    process.exit(1)
  }

  console.log(`✅ ${filePath} is ${(size / 1024).toFixed(2)} KB`)
}

checkSize(join('.output', 'public', '_nuxt', 'index.js'))
```

### 2. CI/CD 集成

```yaml
# .github/workflows/bundle-size.yml
name: Bundle Size Check

on: [pull_request]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm ci
      - run: npm run build
      - run: npm run check:bundle-size
```

## 最佳实践

### ✅ 推荐做法

1. **定期分析**
   ```bash
   npm run build -- --analyze
   ```

2. **按需导入**
   ```typescript
   import debounce from 'lodash/debounce'
   ```

3. **代码分割**
   ```typescript
   manualChunks: {
     'vue-vendor': ['vue', 'vue-router']
   }
   ```

4. **懒加载**
   ```vue
   <LazyHeavyComponent />
   ```

### ❌ 避免做法

1. **导入整个库**
   ```typescript
   // ❌
   import _ from 'lodash'

   // ✅
   import debounce from 'lodash/debounce'
   ```

2. **忽略体积**
   ```typescript
   // ❌ 不检查包体积
   // ✅ 定期分析
   ```

3. **过早优化**
   ```typescript
   // ❌ 还没分析就优化
   // ✅ 先分析,再优化
   ```

## 检查清单

```markdown
## 包体积优化清单

### 分析
- [ ] 使用 DevTools 分析
- [ ] 运行 build --analyze
- [ ] 检查每个 chunk 大小
- [ ] 识别大型依赖

### 优化
- [ ] 移除未使用的依赖
- [ ] 按需导入
- [ ] 替换大型库
- [ ] 代码分割
- [ ] 懒加载组件

### 监控
- [ ] 设置体积限制
- [ ] CI/CD 检查
- [ ] 定期审查
- [ ] 性能预算
```

## 参考资源

- [Nuxt Bundle Analysis](https://nuxt.com/docs/getting-started/performance#bundle-analysis)
- [Rollup Options](https://vitejs.dev/config/build-options.html#build-rollupoptions)
- [Webpack Bundle Analyzer](https://github.com/webpack-contrib/webpack-bundle-analyzer)
- [Bundlephobia](https://bundlephobia.com/) - 查找替代库
