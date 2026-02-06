# CSS 优化完整指南

## 为什么 CSS 优化很重要

**影响:**
- 阻塞渲染: 大型 CSS 文件延迟首屏渲染
- 重复代码: 增加包体积
- 未使用样式: 浪费带宽

**目标:**
- 关键 CSS: < 15KB (gzip)
- 总 CSS: < 50KB (gzip)
- 消除未使用的 CSS

## CSS 代码分割

### 1. Scoped CSS 自动分割

```vue
<!-- 每个组件的样式自动分割 -->
<template>
  <div class="card">
    <h2>Card Title</h2>
  </div>
</template>

<style scoped>
.card {
  padding: 1rem;
  border-radius: 0.5rem;
}
</style>
```

### 2. CSS Modules

```vue
<template>
  <div :class="$style.card">
    <h2>Card Title</h2>
  </div>
</template>

<style module>
.card {
  padding: 1rem;
  border-radius: 0.5rem;
}
</style>
```

### 3. 手动导入

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  css: [
    '~/assets/css/main.css',
    '~/assets/css/components.css',
  ],
})
```

## 关键 CSS 内联

### 1. 提取关键 CSS

```vue
<template>
  <head>
    <!-- 内联关键 CSS -->
    <style>
    .hero {
      min-height: 100vh;
      display: flex;
      align-items: center;
    }
    .hero-content {
      max-width: 800px;
      margin: 0 auto;
    }
    </style>
  </head>
</template>
```

### 2. 使用 Critters

```bash
npm install --save-dev critters
```

```typescript
// nuxt.config.ts
import critters from 'critters'

export default defineNuxtConfig({
  vite: {
    plugins: [
      critters({
        // 内联关键 CSS
        pruneSource: false,
        preload: 'swap',
      })
    ]
  }
})
```

### 3. 手动提取

```typescript
// scripts/critical-css.ts
import { extractCritical } from 'critters'

const critical = await extractCritical(`
  <html>
    <head>
      <link rel="stylesheet" href="/styles/main.css">
    </head>
    <body>
      <div class="hero">...</div>
    </body>
  </html>
`)

console.log(critical.html) // 包含内联关键 CSS
```

## 移除未使用的 CSS

### 1. PurgeCSS (自动)

```bash
npm install --save-dev @fullhuman/postcss-purgecss
```

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  postcss: {
    plugins: {
      '@fullhuman/postcss-purgecss': {
        content: [
          './components/**/*.{vue,js}',
          './layouts/**/*.vue',
          './pages/**/*.vue',
        ],
        defaultExtractor: (content) => content.match(/[\w-/:]+(?<!:)/g) || [],
        safelist: {
          standard: [/^vuex-/, /^route-/, /^nuxt-/],
          deep: [/^nuxt-link-/],
        },
      },
    },
  },
})
```

### 2. Tailwind CSS Purge

```javascript
// tailwind.config.js
module.exports = {
  content: [
    './components/**/*.{vue,js}',
    './layouts/**/*.vue',
    './pages/**/*.vue',
    './plugins/**/*.{js,ts}',
    './nuxt.config.{js,ts}',
  ],
  // 自动移除未使用的样式
}
```

### 3. 手动检查

```bash
# 使用 PurgeCSS CLI
npx purgecss --css styles.css --content index.html --output optimized.css

# 分析未使用的 CSS
npx purgecss --css styles.css --content index.html --keyframes
```

## CSS 压缩

### 1. 生产环境自动压缩

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  build: {
    // 自动压缩
    minify: 'terser',
  },

  vite: {
    css: {
      postcss: {
        plugins: [
          // CSS 压缩
          require('cssnano')({
            preset: 'default',
          }),
        ],
      },
    },
  },
})
```

### 2. 手动压缩

```bash
# 使用 csso
npm install -g csso-cli

csso styles.css styles.min.css

# 使用 clean-css
npm install -g clean-css-cli

cleancss -o styles.min.css styles.css
```

## CSS 优化技巧

### 1. 使用简写属性

```css
/* ❌ 冗长 */
margin-top: 10px;
margin-right: 20px;
margin-bottom: 10px;
margin-left: 20px;

/* ✅ 简写 */
margin: 10px 20px;

/* ❌ 冗长 */
font-family: Arial;
font-size: 16px;
font-weight: bold;
line-height: 1.5;

/* ✅ 简写 */
font: bold 16px/1.5 Arial;
```

### 2. 移除重复代码

```css
/* ❌ 重复 */
.header { padding: 1rem; }
.footer { padding: 1rem; }
.sidebar { padding: 1rem; }

/* ✅ 合并 */
.header,
.footer,
.sidebar {
  padding: 1rem;
}
```

### 3. 使用 CSS 变量

```css
/* 定义变量 */
:root {
  --primary-color: #007bff;
  --font-size: 16px;
  --spacing: 1rem;
}

/* 使用变量 */
.button {
  background: var(--primary-color);
  font-size: var(--font-size);
  padding: var(--spacing);
}
```

### 4. 避免深嵌套

```css
/* ❌ 深嵌套 */
.container .header .nav .menu .item a {
  color: blue;
}

/* ✅ 扁平化 */
.nav-link {
  color: blue;
}
```

## 现代CSS优化

### 1. 使用 CSS Grid

```css
/* ❌ 浮动布局 */
.container {
  overflow: hidden;
}
.item {
  float: left;
  width: 33.33%;
}

/* ✅ Grid 布局 */
.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
}
```

### 2. 使用 Flexbox

```css
/* ❌ 清除浮动 */
.clearfix::after {
  content: '';
  display: block;
  clear: both;
}

/* ✅ Flexbox */
.container {
  display: flex;
}
```

### 3. 使用 clamp()

```css
/* ❌ 多个媒体查询 */
h1 {
  font-size: 24px;
}
@media (min-width: 768px) {
  h1 {
    font-size: 32px;
  }
}
@media (min-width: 1024px) {
  h1 {
    font-size: 48px;
  }
}

/* ✅ clamp() */
h1 {
  font-size: clamp(24px, 5vw, 48px);
}
```

### 4. 使用 min() / max()

```css
/* ❌ 固定值 */
.container {
  width: 1200px;
}

/* ✅ 响应式 */
.container {
  width: min(1200px, 100% - 2rem);
}
```

## 动态导入 CSS

### 1. 按需加载主题

```vue
<script setup lang="ts">
const loadTheme = async (theme: string) => {
  await import(`~/assets/css/themes/${theme}.css`)
}

// 切换主题
const switchTheme = (theme: string) => {
  loadTheme(theme)
}
</script>

<template>
  <button @click="switchTheme('dark')">暗色主题</button>
  <button @click="switchTheme('light')">亮色主题</button>
</template>
```

### 2. 条件加载

```vue
<script setup lang="ts>
onMounted(() => {
  // 只在需要时加载
  if (useFeature('print')) {
    import('~/assets/css/print.css')
  }
})
</script>
```

## CSS-in-JS 优化

### 1. 使用 UnoCSS

```bash
npm install -D unocss
```

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@unocss/nuxt'],

  unocss: {
    // 按需生成,无 unused CSS
  },
})
```

```vue
<template>
  <!-- 原子类 -->
  <div class="flex items-center justify-center p-4 bg-blue-500">
    <h1 class="text-2xl font-bold">Hello</h1>
  </div>
</template>
```

### 2. 使用 Tailwind CSS

```bash
npm install -D tailwindcss postcss autoprefixer
```

```javascript
// tailwind.config.js
module.exports = {
  // 只扫描使用的文件
  content: [
    './components/**/*.{vue,js}',
    './layouts/**/*.vue',
    './pages/**/*.vue',
  ],
  // 自动移除未使用的样式
}
```

## 性能监控

### 1. CSS 大小监控

```typescript
// scripts/check-css-size.ts
import { readFileSync } from 'fs'
import { glob } from 'glob'

const cssFiles = await glob('.output/public/**/*.css')

let totalSize = 0

cssFiles.forEach(file => {
  const content = readFileSync(file)
  const size = content.length
  totalSize += size

  console.log(`${file}: ${(size / 1024).toFixed(2)} KB`)

  if (size > 50 * 1024) {
    console.warn(`⚠️ ${file} exceeds 50KB`)
  }
})

console.log(`\nTotal CSS: ${(totalSize / 1024).toFixed(2)} KB`)
```

### 2. 运行时监控

```typescript
// plugins/css-monitor.client.ts
export default defineNuxtPlugin(() => {
  if (process.env.NODE_ENV === 'development') {
    // 监控 CSS 加载
    performance.getEntriesByType('resource').forEach((entry) => {
      if (entry.name.includes('.css')) {
        console.log('CSS:', entry.name)
        console.log('Size:', entry.transferSize, 'bytes')
        console.log('Duration:', entry.duration, 'ms')
      }
    })
  }
})
```

## 最佳实践

### ✅ 推荐做法

1. **代码分割**
   ```vue
   <style scoped>
   /* 组件级 CSS */
   </style>
   ```

2. **移除未使用的 CSS**
   ```javascript
   // 使用 PurgeCSS
   ```

3. **压缩 CSS**
   ```typescript
   cssnano: { preset: 'default' }
   ```

4. **使用现代 CSS**
   ```css
   display: grid;
   display: flex;
   ```

5. **按需加载**
   ```typescript
   await import('~/css/theme.css')
   ```

### ❌ 避免做法

1. **大型 CSS 文件**
   ```css
   /* ❌ 500KB 的 CSS */
   /* ✅ 分割成小文件 */
   ```

2. **深嵌套**
   ```css
   /* ❌ */
   .a .b .c .d { }

   /* ✅ */
   .d { }
   ```

3. **!important 滥用**
   ```css
   /* ❌ */
   .text { color: red !important; }

   /* ✅ */
   .text.highlight { color: red; }
   ```

## 检查清单

```markdown
## CSS 优化清单

### 代码分割
- [ ] 使用 Scoped CSS
- [ ] 组件级样式分离
- [ ] 按需加载主题

### 移除冗余
- [ ] 移除未使用的 CSS
- [ ] 压缩 CSS 代码
- [ ] 合并重复样式

### 性能
- [ ] 关键 CSS < 15KB
- [ ] 总 CSS < 50KB
- [ ] 内联关键 CSS

### 现代 CSS
- [ ] 使用 Grid/Flexbox
- [ ] 使用 CSS 变量
- [ ] 使用现代函数 (clamp/min/max)
```

## 参考资源

- [CSS Optimization](https://web.dev/fast/#remove-unused-css)
- [Critical CSS](https://web.dev/extract-critical-css/)
- [PurgeCSS](https://purgecss.com/)
- [CSSnano](https://cssnano.co/)
- [UnoCSS](https://unocss.dev/)
