---
name: vite-performance
description: Vite 性能优化最佳实践
---

# 性能优化

## 开发服务器优化

### 依赖预优化

```ts
export default defineConfig({
  optimizeDeps: {
    include: ['vue', 'react', 'react-dom'],
    exclude: ['some-dev-only-package'],
    force: false, // 仅在依赖变化时重新预优化
  },
})
```

### 禁用源映射（开发环境）

```ts
export default defineConfig({
  build: {
    sourcemap: false, // 开发环境禁用
  },
})
```

### 文件监听优化

```ts
export default defineConfig({
  server: {
    watch: {
      ignored: ['**/node_modules/**', '**/.git/**'],
      usePolling: false, // Windows 上可能需要 true
    },
  },
})
```

## 生产构建优化

### 代码分割

```ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          'vendor-vue': ['vue', 'vue-router'],
          'vendor-ui': ['element-plus', '@element-plus/icons-vue'],
        },
      },
    },
  },
})
```

### 压缩配置

```ts
import { defineConfig } from 'vite'

export default defineConfig({
  build: {
    minify: 'terser', // 或 'esbuild'
    terserOptions: {
      compress: {
        drop_console: true, // 移除 console
        drop_debugger: true,
      },
    },
  },
})
```

### 资源内联阈值

```ts
export default defineConfig({
  build: {
    assetsInlineLimit: 4096, // 小于 4kb 的资源内联为 base64
  },
})
```

### CSS 代码分割

```ts
export default defineConfig({
  build: {
    cssCodeSplit: true, // 启用 CSS 代码分割
  },
})
```

### 目标浏览器优化

```ts
export default defineConfig({
  build: {
    target: ['es2015', 'chrome58', 'firefox57', 'safari11'],
  },
})
```

## 构建缓存

### 持久化缓存

```ts
export default defineConfig({
  build: {
    cacheDir: 'node_modules/.vite', // 默认值
  },
})
```

### 清理缓存

```bash
# 手动清理缓存
rm -rf node_modules/.vite
# 或使用 Vite 命令
vite build --force
```

## 资源优化

### 图片优化

```ts
import { defineConfig } from 'vite'
import viteImagemin from 'vite-plugin-imagemin'

export default defineConfig({
  plugins: [
    viteImagemin({
      gifsicle: { optimizationLevel: 7 },
      optipng: { optimizationLevel: 7 },
      mozjpeg: { quality: 80 },
      webp: { quality: 80 },
    }),
  ],
})
```

### SVG 优化

```ts
import { defineConfig } from 'vite'
import { createSvgIconsPlugin } from 'vite-plugin-svg-icons'

export default defineConfig({
  plugins: [
    createSvgIconsPlugin({
      iconDirs: [path.resolve(process.cwd(), 'src/icons')],
      symbolId: 'icon-[dir]-[name]',
    }),
  ],
})
```

## 第三方库优化

### 大型库按需导入

```ts
// Element Plus
import { defineConfig } from 'vite'
import AutoImport from 'unplugin-auto-import/rspack'
import Components from 'unplugin-vue-components/rspack'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'

export default defineConfig({
  plugins: [
    AutoImport({
      resolvers: [ElementPlusResolver()],
    }),
    Components({
      resolvers: [ElementPlusResolver()],
    }),
  ],
})
```

### Lodash 按需导入

```ts
import { defineConfig } from 'vite'
import AutoImport from 'unplugin-auto-import/rspack'

export default defineConfig({
  plugins: [
    AutoImport({
      imports: ['lodash'],
      dts: 'src/auto-imports.d.ts',
    }),
  ],
})
```

## 网络优化

### 预加载关键资源

```ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks(id) {
          if (id.includes('node_modules')) {
            return 'vendor'
          }
        },
      },
    },
  },
})
```

### HTTP/2 推送

```ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        // 使用 HTTP/2 推送
        assetFileNames: 'assets/[name]-[hash][extname]',
        chunkFileNames: 'assets/[name]-[hash].js',
        entryFileNames: 'assets/[name]-[hash].js',
      },
    },
  },
})
```

## 性能监控

### 构建分析

```bash
# 安装 rollup-plugin-visualizer
pnpm add -D rollup-plugin-visualizer
```

```ts
import { defineConfig } from 'vite'
import { visualizer } from 'rollup-plugin-visualizer'

export default defineConfig({
  plugins: [
    visualizer({
      open: true,
      gzipSize: true,
      brotliSize: true,
    }),
  ],
})
```

### 包大小分析

```ts
import { defineConfig } from 'vite'
import vitePluginInspect from 'vite-plugin-inspect'

export default defineConfig({
  plugins: [
    vitePluginInspect(),
  ],
})
```

## 最佳实践总结

1. **开发环境**：禁用不必要的源映射，优化文件监听
2. **生产构建**：启用代码分割，压缩资源，优化目标浏览器
3. **依赖管理**：按需导入大型库，预优化常用依赖
4. **缓存策略**：利用 Vite 的持久化缓存，必要时手动清理
5. **资源优化**：压缩图片，优化 SVG，内联小资源
6. **监控分析**：使用构建分析工具定期检查包大小

<!--
Source references:
- https://vite.dev/guide/build
- https://vite.dev/guide/performance
-->
