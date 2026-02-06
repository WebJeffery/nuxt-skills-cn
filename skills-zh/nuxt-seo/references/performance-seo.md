# 性能优化与 SEO

## Core Web Vitals

| 指标 | 良好 | 需改进 | 不及格 |
|------|------|--------|--------|
| LCP | < 2.5s | < 4s | ≥ 4s |
| INP | < 200ms | < 500ms | ≥ 500ms |
| CLS | < 0.1 | < 0.25 | ≥ 0.25 |

## LCP 优化

### 1. 优化关键资源加载

```typescript
export default defineNuxtConfig({
  app: {
    head: {
      link: [
        {
          rel: 'preconnect',
          href: 'https://api.example.com',
        },
      ],
    },
  },
})
```

### 2. 优化字体

```typescript
export default defineNuxtConfig({
  googleFonts: {
    display: 'swap',
    prefetch: true,
    preconnect: true,
  },
})
```

### 3. 优化图片

```vue
<NuxtImg
  src="/image.jpg"
  width="800"
  height="600"
  loading="lazy"
  format="webp"
  alt="Description"
/>
```

## INP 优化

```typescript
import { useDebounceFn } from '@vueuse/core'

const handleInput = useDebounceFn((value: string) => {
  // 处理输入
}, 300)
```

## CLS 优化

```vue
<div style="aspect-ratio: 16 / 9;">
  <img src="/image.jpg" style="width: 100%; height: 100%; object-fit: cover;" />
</div>
```

## 性能监控

```bash
npm install web-vitals
```

```typescript
import { onCLS, onFID, onLCP, onINP } from 'web-vitals'

export default defineNuxtPlugin(() => {
  onCLS((metric) => console.log('CLS:', metric))
  onLCP((metric) => console.log('LCP:', metric))
  onINP((metric) => console.log('INP:', metric))
})
```

## 性能测试工具

- [PageSpeed Insights](https://pagespeed.web.dev/)
- [WebPageTest](https://www.webpagetest.org/)
- [GTmetrix](https://gtmetrix.com/)
