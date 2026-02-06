# 图片优化完整指南

## 为什么图片优化很重要

图片通常占网页总大小的 50-80%,优化图片可以显著提升加载速度。

**性能影响:**
- 未优化的图片: 2-5MB
- 优化后的图片: 200-500KB
- **节省: 80-90%**

## Nuxt Image 模块

### 安装

```bash
npm install @nuxt/image
```

### 基础配置

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@nuxt/image'],

  image: {
    // 图片格式优先级
    formats: ['webp', 'avif', 'jpeg'],

    // 默认质量
    quality: 80,

    // 允许的外部域名
    domains: [
      'cdn.example.com',
      'images.unsplash.com',
      'res.cloudinary.com'
    ],

    // 响应式断点
    screens: {
      'xs': 320,
      'sm': 640,
      'md': 768,
      'lg': 1024,
      'xl': 1280,
      'xxl': 1536,
    },

    // CDN 提供商
    providers: {
      cloudinary: {
        baseURL: 'https://res.cloudinary.com/your-cloud-name/image/fetch/',
      },
    },
  },
})
```

## 基础用法

### 1. 本地图片

```vue
<template>
  <!-- 基础用法 -->
  <NuxtImg
    src="/hero.jpg"
    alt="Hero banner"
    width="800"
    height="600"
  />

  <!-- 懒加载 -->
  <NuxtImg
    src="/photo.jpg"
    loading="lazy"
    alt="Photo"
  />

  <!-- 预加载关键图片 -->
  <NuxtImg
    src="/logo.png"
    priority
    alt="Logo"
  />
</template>
```

### 2. 响应式图片

```vue
<template>
  <!-- 使用 sizes 属性 -->
  <NuxtImg
    src="/hero.jpg"
    sizes="sm:100vw md:50vw lg:400px"
    alt="Responsive hero"
  />

  <!-- 完全响应式 -->
  <NuxtImg
    src="/banner.jpg"
    width="800"
    height="600"
    sizes="
      (max-width: 640px) 100vw,
      (max-width: 1024px) 50vw,
      800px
    "
    alt="Banner"
  />
</template>
```

### 3. 格式转换

```vue
<template>
  <!-- 自动转换为 WebP/AVIF -->
  <NuxtImg
    src="/image.jpg"
    format="webp"
    alt="Optimized"
  />

  <!-- 多格式支持 -->
  <NuxtImg
    src="/image.png"
    format="avif,webp,jpeg"
    alt="Fallback"
  />
</template>
```

### 4. 质量控制

```vue
<template>
  <!-- 自定义质量 -->
  <NuxtImg
    src="/photo.jpg"
    quality="60"
    alt="Lower quality"
  />

  <!-- 响应式质量 -->
  <NuxtImg
    src="/photo.jpg"
    :quality="{ sm: 40, md: 60, lg: 80 }"
    alt="Responsive quality"
  />
</template>
```

## 高级用法

### 1. 图片修饰符

```vue
<template>
  <!-- 裁剪 -->
  <NuxtImg
    src="/photo.jpg"
    fit="cover"
    width="400"
    height="300"
    alt="Cropped"
  />

  <!-- 滤镜 -->
  <NuxtImg
    src="/photo.jpg"
    :modifiers="{ blur: 10, grayscale: true }"
    alt="Blurred"
  />

  <!-- 旋转 -->
  <NuxtImg
    src="/photo.jpg"
    :modifiers="{ rotate: 90 }"
    alt="Rotated"
  />
</template>
```

### 2. 占位符

```vue
<template>
  <!-- 模糊占位符 (LQIP) -->
  <NuxtImg
    src="/photo.jpg"
    placeholder
    width="800"
    height="600"
    alt="With placeholder"
  />

  <!-- 自定义占位符 -->
  <NuxtImg
    src="/photo.jpg"
    placeholder="/placeholder.jpg"
    alt="Custom placeholder"
  />
</template>
```

### 3. 外部图片

```vue
<template>
  <!-- 使用配置的域名 -->
  <NuxtImg
    src="https://cdn.example.com/image.jpg"
    alt="External"
  />

  <!-- 使用 provider -->
  <NuxtImg
    provider="cloudinary"
    src="/remote-image.jpg"
    width="800"
    height="600"
    alt="CDN image"
  />

  <!-- 任意 URL (需要配置 allowList) -->
  <NuxtImg
    src="https://example.com/image.jpg"
    :modifiers="{ resize: '800x600' }"
    alt="Arbitrary URL"
  />
</template>
```

### 4. 动态图片

```vue
<script setup lang="ts">
const images = [
  '/photo1.jpg',
  '/photo2.jpg',
  '/photo3.jpg'
]
const currentIndex = ref(0)

const currentImage = computed(() => images[currentIndex.value])
</script>

<template>
  <NuxtImg
    :src="currentImage"
    width="800"
    height="600"
    alt="Gallery"
  />

  <button @click="currentIndex++">下一张</button>
</template>
```

## CDN 提供商

### 1. Cloudinary

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  image: {
    cloudinary: {
      baseURL: 'https://res.cloudinary.com/your-cloud-name/image/fetch/',
    },
  },
})
```

```vue
<template>
  <NuxtImg
    provider="cloudinary"
    src="https://example.com/image.jpg"
    width="800"
    height="600"
    loading="lazy"
    alt="Cloudinary"
  />
</template>
```

### 2. Imgix

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  image: {
    imgix: {
      baseURL: 'https://your-subdomain.imgix.net',
    },
  },
})
```

### 3. Unsplash

```vue
<template>
  <NuxtImg
    provider="unsplash"
    src="photo-1618005182384-a83a8bd57fbe"
    alt="Unsplash"
    width="800"
    height="600"
  />
</template>
```

## 原生优化方法

### 1. 现代格式

```vue
<template>
  <!-- 手动实现 picture 标签 -->
  <picture>
    <source srcset="/hero.avif" type="image/avif" />
    <source srcset="/hero.webp" type="image/webp" />
    <img
      src="/hero.jpg"
      alt="Hero"
      loading="lazy"
      decoding="async"
    />
  </picture>
</template>
```

### 2. 响应式图片

```vue
<template>
  <img
    src="/image-800.jpg"
    srcset="
      /image-400.jpg 400w,
      /image-800.jpg 800w,
      /image-1200.jpg 1200w
    "
    sizes="
      (max-width: 640px) 100vw,
      (max-width: 1024px) 50vw,
      800px
    "
    alt="Responsive"
    loading="lazy"
  />
</template>
```

### 3. 预加载关键图片

```vue
<template>
  <head>
    <!-- 预加载首屏图片 -->
    <link
      rel="preload"
      as="image"
      href="/hero.webp"
      imagesrcset="/hero-400.webp 400w, /hero-800.webp 800w"
      imagesizes="(max-width: 640px) 100vw, 800px"
    />
  </head>
</template>
```

## 图片优化策略

### 1. 懒加载

```vue
<template>
  <!-- NuxtImg 自动支持 -->
  <NuxtImg
    src="/photo.jpg"
    loading="lazy"
    alt="Lazy"
  />

  <!-- 原生 lazy loading -->
  <img
    src="/photo.jpg"
    loading="lazy"
    alt="Lazy"
  />

  <!-- 视口内加载 -->
  <NuxtImg
    src="/photo.jpg"
    :threshold="0.1"
    alt="Threshold"
  />
</template>
```

### 2. 渐进式加载

```vue
<script setup lang="ts">
const isLoaded = ref(false)
const loadImage = () => {
  const img = new Image()
  img.onload = () => {
    isLoaded.value = true
  }
  img.src = '/high-quality.jpg'
}

onMounted(loadImage)
</script>

<template>
  <div class="image-container">
    <!-- 低质量占位符 -->
    <img
      v-if="!isLoaded"
      src="/low-quality-blur.jpg"
      class="blur"
      alt="Placeholder"
    />

    <!-- 高质量图片 -->
    <img
      v-if="isLoaded"
      src="/high-quality.jpg"
      class="fade-in"
      alt="High quality"
    />
  </div>
</template>

<style scoped>
.blur {
  filter: blur(10px);
}

.fade-in {
  animation: fadeIn 0.3s ease-in;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}
</style>
```

### 3. 响应式图片集

```vue
<script setup lang="ts">
const imageSizes = {
  thumbnail: 150,
  small: 400,
  medium: 800,
  large: 1200,
  xlarge: 1600,
}
</script>

<template>
  <NuxtImg
    src="/photo.jpg"
    :sizes="
      Object.entries(imageSizes)
        .map(([key, width]) => `${width}px`)
        .join(', ')
    "
    :srcset="
      Object.entries(imageSizes)
        .map(([key, width]) => `/photo-${key}.jpg ${width}w`)
        .join(', ')
    "
    alt="Responsive set"
  />
</template>
```

## 性能最佳实践

### ✅ 推荐做法

1. **使用现代格式**
   ```vue
   <NuxtImg src="/image.jpg" format="webp" />
   ```

2. **懒加载非首屏图片**
   ```vue
   <NuxtImg src="/photo.jpg" loading="lazy" />
   ```

3. **响应式尺寸**
   ```vue
   <NuxtImg
     src="/photo.jpg"
     sizes="sm:100vw md:50vw lg:400px"
   />
   ```

4. **设置宽高**
   ```vue
   <NuxtImg
     src="/photo.jpg"
     width="800"
     height="600"
   />
   ```

5. **使用 CDN**
   ```vue
   <NuxtImg
     provider="cloudinary"
     src="/image.jpg"
   />
   ```

### ❌ 避免做法

1. **加载过大图片**
   ```vue
   <!-- ❌ 移动端也加载 4K 图片 -->
   <img src="/image-4k.jpg" />

   <!-- ✅ 响应式 -->
   <NuxtImg
     src="/image.jpg"
     sizes="sm:100vw lg:2000px"
   />
   ```

2. **不设置宽高**
   ```vue
   <!-- ❌ 导致布局偏移 -->
   <img src="/photo.jpg" />

   <!-- ✅ 避免CLS -->
   <NuxtImg
     src="/photo.jpg"
     width="800"
     height="600"
   />
   ```

3. **忽略格式**
   ```vue
   <!-- ❌ 只用 JPEG -->
   <img src="/photo.jpg" />

   <!-- ✅ 使用现代格式 -->
   <NuxtImg
     src="/photo.jpg"
     format="webp,avif"
   />
   ```

## 工具推荐

### 1. 图片压缩工具

```bash
# Squoosh (CLI)
npx @squoosh/lib --src /images --dest /optimized

# Sharp (Node.js)
npm install sharp

# ImageMagick
convert input.jpg -quality 80 output.jpg
```

### 2. 批量优化

```typescript
// scripts/optimize-images.ts
import sharp from 'sharp'
import { readdir } from 'fs/promises'

const optimizeImage = async (input: string, output: string) => {
  await sharp(input)
    .resize(800, 600, { fit: 'cover' })
    .webp({ quality: 80 })
    .toFile(output)
}

const main = async () => {
  const files = await readdir('./images/raw')

  for (const file of files) {
    await optimizeImage(
      `./images/raw/${file}`,
      `./public/images/${file.replace(/\.[^.]+$/, '.webp')}`
    )
  }
}

main()
```

### 3. 自动优化

```bash
# 使用 Nuxt Image 的自动优化
# 所有图片通过 /_ipx/ 路径自动优化
```

## 性能监控

```typescript
// plugins/image-performance.client.ts
export default defineNuxtPlugin(() => {
  if (process.env.NODE_ENV === 'development') {
    // 监控图片加载时间
    const images = document.querySelectorAll('img')
    const observer = new PerformanceObserver((list) => {
      for (const entry of list.getEntries()) {
        if (entry.entryType === 'resource') {
          const resource = entry as PerformanceResourceTiming
          if (resource.initiatorType === 'img') {
            console.log(`图片加载: ${resource.name}`)
            console.log(`大小: ${resource.transferSize} bytes`)
            console.log(`耗时: ${resource.duration}ms`)
          }
        }
      }
    })

    observer.observe({ entryTypes: ['resource'] })
  }
})
```

## 检查清单

```markdown
## 图片优化清单

- [ ] 所有图片使用 WebP/AVIF 格式
- [ ] 非首屏图片懒加载
- [ ] 设置正确的宽高 (避免CLS)
- [ ] 使用响应式图片
- [ ] 压缩图片质量 (70-85%)
- [ ] 使用 CDN 托管
- [ ] 优先加载关键图片
- [ ] 提供占位符 (避免空白)
- [ ] 使用适当的图片格式
  - 照片: JPEG/WebP
  - 图标: SVG
  - 透明图: PNG/WebP
- [ ] 监控图片性能
```

## 参考资源

- [Nuxt Image 文档](https://image.nuxt.com/)
- [WebP 规范](https://developers.google.com/speed/webp)
- [AVIF 规范](https://aomediacodec.github.io/av1-avif/)
- [Image Optimization Best Practices](https://web.dev/fast/#optimize-your-images)
