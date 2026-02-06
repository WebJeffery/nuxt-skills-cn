# 字体优化完整指南

## 为什么字体优化很重要

**影响:**
- FOIT (Flash of Invisible Text): 文本不可见
- FOUT (Flash of Unstyled Text): 字体跳变
- CLS (Cumulative Layout Shift): 布局偏移

**目标:**
- 最小化字体加载时间
- 避免文本不可见
- 防止布局偏移

## 字体格式选择

### 现代字体格式

| 格式 | 压缩率 | 兼容性 | 推荐场景 |
|------|--------|--------|----------|
| WOFF2 | ⭐⭐⭐⭐⭐ | 95%+ | 现代浏览器首选 |
| WOFF | ⭐⭐⭐⭐ | 98%+ | 老版本后备 |
| TTF/OTF | ⭐⭐⭐ | 100% | 不推荐用于Web |

### 使用多种格式

```vue
<template>
  <head>
    <!-- 现代: WOFF2 -->
    <link rel="preload" as="font" href="/font.woff2" type="font/woff2" crossorigin />

    <!-- 后备: WOFF -->
    <link rel="preload" as="font" href="/font.woff" type="font/woff" crossorigin />
  </head>
</template>
```

## 字体优化策略

### 1. font-display

```css
/* 立即显示后备字体 */
@font-face {
  font-family: 'Custom Font';
  src: url('/font.woff2') format('woff2');
  font-display: swap; /* 推荐 */
}

/* 短暂等待后显示后备字体 */
@font-face {
  font-family: 'Custom Font';
  src: url('/font.woff2') format('woff2');
  font-display: fallback; /* 100ms */
}

/* 等待字体加载 */
@font-face {
  font-family: 'Custom Font';
  src: url('/font.woff2') format('woff2');
  font-display: optional; /* 可选 */
}
```

**font-display 值:**
- `swap`: 立即显示后备字体 (推荐)
- `fallback`: 短暂等待 (100ms),然后显示后备
- `optional`: 短暂等待,可能不显示自定义字体
- `block`: 阻塞文本显示 (不推荐)

### 2. 子集化字体

```bash
# 使用 pyftsubset
pip install fonttools

# 只包含需要的字符
pyftsubset font.ttf \
  --output-file=font-subset.ttf \
  --text="ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789"

# 只包含拉丁字符
pyftsubset font.ttf \
  --output-file=font-latin.ttf \
  --unicodes=U+0020-007F

# 生成多个子集
pyftsubset font.ttf --output-file=latin.woff2 --unicodes=U+0020-007F
pyftsubset font.ttf --output-file=cyrillic.woff2 --unicodes=U+0400-04FF
```

### 3. 转换为 WOFF2

```bash
# 使用 sfnt2woff2
# https://github.com/google/woff2

# 编译
git clone https://github.com/google/woff2.git
cd woff2
make clean all

# 转换
./sfnt2woff2 font.ttf

# 批量转换
for file in *.ttf; do
  ./sfnt2woff2 "$file"
done
```

## 字体预加载

### 1. 预加载关键字体

```vue
<template>
  <head>
    <!-- 预加载字体 -->
    <link
      rel="preload"
      as="font"
      href="/fonts/main.woff2"
      type="font/woff2"
      crossorigin
    />
  </head>
</template>
```

### 2. 条件预加载

```vue
<script setup lang="ts">
// 只在需要时加载字体
const loadFont = (fontName: string) => {
  const link = document.createElement('link')
  link.rel = 'preload'
  link.as = 'font'
  link.href = `/fonts/${fontName}.woff2`
  link.type = 'font/woff2'
  link.crossOrigin = 'anonymous'
  document.head.appendChild(link)
}

onMounted(() => {
  // 延迟加载非关键字体
  setTimeout(() => {
    loadFont('secondary')
  }, 2000)
})
</script>
```

## Google Fonts 优化

### 1. 使用 font-display

```html
<!-- 添加 &display=swap 参数 -->
<link
  href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap"
  rel="stylesheet"
/>
```

### 2. 只选择需要的字重

```html
<!-- ❌ 加载所有字重 -->
<link href="https://fonts.googleapis.com/css2?family=Inter&display=swap" />

<!-- ✅ 只选择需要的字重 -->
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" />
```

### 3. 自托管 Google Fonts

```bash
# 使用 google-font-download
npx google-font-download --family "Inter:wght@400;600;700" --output fonts/
```

```vue
<template>
  <head>
    <!-- 使用自托管字体 -->
    <link
      rel="preload"
      as="font"
      href="/fonts/inter-400.woff2"
      type="font/woff2"
      crossorigin
    />
  </head>
</template>

<style>
@font-face {
  font-family: 'Inter';
  font-weight: 400;
  src: url('/fonts/inter-400.woff2') format('woff2');
  font-display: swap;
}
</style>
```

## 避免布局偏移

### 1. 使用 font-size-adjust

```css
/* 保持字体大小一致 */
body {
  font-family: 'Custom Font', system-ui, sans-serif;
  font-size-adjust: 0.5; /* 调整小写字母高度 */
}
```

### 2. 预留空间

```vue
<template>
  <!-- 为文本预留最小高度 -->
  <h1 class="title">{{ heading }}</h1>
</template>

<style scoped>
.title {
  /* 预留空间避免 CLS */
  min-height: 1.2em;
  font-family: 'Custom Font', system-ui, sans-serif;
  font-size: 2rem;
  line-height: 1.2;
}
</style>
```

### 3. 使用后备字体

```css
/* 字体栈 */
body {
  font-family: 'Custom Font', /* 自定义字体 */
               -apple-system, /* macOS */
               BlinkMacSystemFont, /* Chrome */
               'Segoe UI', /* Windows */
               Roboto, /* Android */
               sans-serif; /* 后备 */
}
```

## 实际应用

### 1. 主字体优化

```css
/* assets/css/fonts.css */
/* 主字体 */
@font-face {
  font-family: 'Inter';
  font-weight: 400;
  src: url('/fonts/inter-400.woff2') format('woff2');
  font-display: swap;
  unicode-range: U+0020-007F; /* 拉丁字符 */
}

@font-face {
  font-family: 'Inter';
  font-weight: 600;
  src: url('/fonts/inter-600.woff2') format('woff2');
  font-display: swap;
}

@font-face {
  font-family: 'Inter';
  font-weight: 700;
  src: url('/fonts/inter-700.woff2') format('woff2');
  font-display: swap;
}

/* 应用字体 */
body {
  font-family: 'Inter', -apple-system, sans-serif;
}
```

```vue
<template>
  <head>
    <!-- 预加载关键字体 -->
    <link
      rel="preload"
      as="font"
      href="/fonts/inter-400.woff2"
      type="font/woff2"
      crossorigin
    />
  </head>
</template>
```

### 2. 图标字体优化

```css
/* 使用 SVG 代替图标字体 */
/* ❌ 不推荐: 字体图标 */
.icon {
  font-family: 'Font Awesome';
}

/* ✅ 推荐: SVG 图标 */
.icon {
  background-image: url('/icon.svg');
}

/* 或使用内联 SVG */
```

### 3. 动态加载字体

```vue
<script setup lang="ts">
// Web Font Loader
const loadWebFont = async () => {
  const WebFont = await import('webfontloader')

  WebFont.load({
    custom: {
      families: ['Inter:n4,n6,n7'],
      urls: ['/fonts/inter.css'],
    },
    active: () => {
      console.log('字体加载完成')
    },
    inactive: () => {
      console.log('字体加载失败')
    },
  })
}

onMounted(() => {
  loadWebFont()
})
</script>
```

## 性能监控

### 1. 字体加载时间

```typescript
// plugins/font-monitor.client.ts
export default defineNuxtPlugin(() => {
  if (process.env.NODE_ENV === 'development') {
    // 监控字体加载
    document.fonts.ready.then(() => {
      console.log('所有字体已加载')
    })

    // 监控特定字体
    document.fonts.load('16px Inter').then(() => {
      console.log('Inter 字体已加载')
    })

    // 使用 Performance API
    performance.getEntriesByType('resource').forEach((entry) => {
      if (entry.name.includes('.woff2')) {
        console.log('字体加载时间:', entry.duration)
        console.log('字体大小:', entry.transferSize, 'bytes')
      }
    })
  }
})
```

### 2. 字体渲染指标

```typescript
// 使用 web-vitals 测量字体对 CLS 的影响
import { getCLS } from 'web-vitals'

getCLS((metric) => {
  console.log('CLS:', metric.value)
  // 高 CLS 可能是由于字体加载导致的
})
```

## 最佳实践

### ✅ 推荐做法

1. **使用 WOFF2**
   ```css
   src: url('/font.woff2') format('woff2');
   ```

2. **font-display: swap**
   ```css
   font-display: swap;
   ```

3. **预加载关键字体**
   ```vue
   <link rel="preload" as="font" href="/main.woff2" />
   ```

4. **子集化字体**
   ```bash
   pyftsubset font.ttf --text="ABC..."
   ```

5. **自托管字体**
   ```bash
   # 不依赖外部请求
   ```

### ❌ 避免做法

1. **加载过多字体**
   ```css
   /* ❌ 加载 10+ 字体文件 */
   @font-face { font-family: 'Font1'; ... }
   @font-face { font-family: 'Font2'; ... }
   /* ... */

   /* ✅ 只加载 2-3 个字体 */
   ```

2. **阻塞渲染**
   ```css
   /* ❌ 阻塞文本显示 */
   font-display: block;

   /* ✅ 立即显示后备字体 */
   font-display: swap;
   ```

3. **忽略 CLS**
   ```vue
   <!-- ❌ 导致布局偏移 -->
   <h1>{{ heading }}</h1>

   <!-- ✅ 预留空间 -->
   <h1 style="min-height: 1.2em;">{{ heading }}</h1>
   ```

## 检查清单

```markdown
## 字体优化清单

### 格式
- [ ] 使用 WOFF2 格式
- [ ] 提供 WOFF 后备
- [ ] 子集化字体字符

### 加载
- [ ] 预加载关键字体
- [ ] 使用 font-display: swap
- [ ] 延迟加载非关键字体

### 性能
- [ ] 字体文件 < 50KB
- [ ] 只加载需要的字重
- [ ] 自托管字体

### 用户体验
- [ ] 避免 FOIT/FOUT
- [ ] 防止布局偏移
- [ ] 提供好的后备字体
```

## 参考资源

- [Web Font Optimization](https://web.dev/fast/#avoid-massive-network-payloads)
- [font-display](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/font-display)
- [WOFF2](https://www.w3.org/TR/WOFF2/)
- [Google Fonts](https://fonts.google.com/)
- [Font Optimization Guide](https://web.dev/optimize-webfont-loading/)
