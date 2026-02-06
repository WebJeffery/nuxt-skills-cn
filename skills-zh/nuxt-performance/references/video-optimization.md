# 视频优化完整指南

## 为什么视频优化很重要

视频通常是最重的资源,未优化的视频可能达到几十甚至几百 MB。

**影响:**
- 页面加载时间: 增加 10-30 秒
- 数据消耗: 移动用户耗尽流量
- 用户体验: 高跳出率

## 视频格式选择

### 现代视频格式对比

| 格式 | 压缩率 | 兼容性 | 浏览器支持 | 推荐场景 |
|------|--------|--------|------------|----------|
| MP4 (H.264) | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | 95%+ | 兼容性后备 |
| WebM (VP9) | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | 85%+ | 现代浏览器 |
| WebM (AV1) | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | 70%+ | 最佳压缩 |
| HLS | ⭐⭐⭐ | ⭐⭐⭐⭐ | 90%+ | 流式传输 |

### 使用多种格式

```vue
<template>
  <video width="800" height="450" controls>
    <!-- AV1: 最佳压缩 -->
    <source src="/video.av1.mp4" type="video/mp4; codecs=av01.0.01M.08">

    <!-- VP9: 次优选择 -->
    <source src="/video.webm" type="video/webm; codecs=vp9">

    <!-- H.264: 兼容性后备 -->
    <source src="/video.mp4" type="video/mp4">

    您的浏览器不支持视频标签。
  </video>
</template>
```

## 视频压缩

### 1. 使用 FFmpeg

```bash
# 安装 FFmpeg
# macOS: brew install ffmpeg
# Ubuntu: sudo apt install ffmpeg
# Windows: https://ffmpeg.org/download.html

# 压缩为 H.264 (兼容性最好)
ffmpeg -i input.mov -c:v libx264 -crf 28 -preset slow output.mp4

# 压缩为 VP9 (更好的压缩)
ffmpeg -i input.mov -c:v libvpx-vp9 -crf 31 -b:v 0 -b:a 128k output.webm

# 压缩为 AV1 (最佳压缩)
ffmpeg -i input.mov -c:v libaom-av1 -crf 50 -b:v 0 output.av1.mp4

# 调整分辨率
ffmpeg -i input.mp4 -vf scale=1280:720 -c:v libx264 -crf 28 output_720p.mp4

# 提取音频
ffmpeg -i input.mp4 -vn -acodec libmp3lame -qscale:a 2 output.mp3
```

### 2. CRF 值指南

**CRF (Constant Rate Factor)** - 质量控制参数

```bash
# H.264 CRF 值
# 18-28: 推荐范围 (数值越大质量越低,文件越小)
# 18: 几乎无损
# 23: 高质量 (默认)
# 28: 中等质量
# 30+: 低质量

ffmpeg -i input.mp4 -c:v libx264 -crf 26 output.mp4

# VP9 CRF 值
# 15-35: 推荐范围
# 31: 中等质量 (相当于 H.264 的 28)

ffmpeg -i input.mp4 -c:v libvpx-vp9 -crf 31 -b:v 0 output.webm
```

### 3. 分辨率适配

```bash
# 生成多个分辨率版本
# 1080p
ffmpeg -i input.mp4 -vf scale=1920:1080 -c:v libx264 -crf 28 output_1080p.mp4

# 720p
ffmpeg -i input.mp4 -vf scale=1280:720 -c:v libx264 -crf 28 output_720p.mp4

# 480p
ffmpeg -i input.mp4 -vf scale=854:480 -c:v libx264 -crf 28 output_480p.mp4
```

## 懒加载视频

### 1. 使用 loading 属性

```vue
<template>
  <!-- 原生懒加载 -->
  <video
    width="800"
    height="450"
    controls
    loading="lazy"
    poster="/video-poster.jpg"
  >
    <source src="/video.mp4" type="video/mp4">
  </video>
</template>
```

### 2. 视口内加载

```vue
<script setup lang="ts">
import { useIntersectionObserver } from '@vueuse/core'

const videoContainer = ref<HTMLDivElement>()
const shouldLoad = ref(false)

const { stop } = useIntersectionObserver(
  videoContainer,
  ([{ isIntersecting }]) => {
    if (isIntersecting) {
      shouldLoad.value = true
      stop() // 停止观察
    }
  }
)
</script>

<template>
  <div ref="videoContainer">
    <video v-if="shouldLoad" width="800" height="450" controls>
      <source src="/video.mp4" type="video/mp4">
    </video>

    <!-- 占位符 -->
    <div v-else class="video-placeholder">
      <img src="/video-poster.jpg" alt="Video thumbnail" />
      <span>加载中...</span>
    </div>
  </div>
</template>
```

## 视频封面优化

### 1. 使用高质量封面

```vue
<template>
  <video
    width="800"
    height="450"
    controls
    poster="/video-poster.jpg"
  >
    <source src="/video.mp4" type="video/mp4">
  </video>
</template>
```

**封面优化建议:**
- 格式: WebP 或 JPEG (80% 质量)
- 尺寸: 与视频相同 (如 1280x720)
- 大小: < 100KB
- 内容: 视频中有代表性的帧

### 2. 自动生成封面

```bash
# 提取视频第一帧
ffmpeg -i input.mp4 -vframes 1 -q:v 2 poster.jpg

# 提取特定时间的帧 (如 5 秒处)
ffmpeg -i input.mp4 -ss 00:00:05 -vframes 1 -q:v 2 poster.jpg

# 生成多个缩略图
ffmpeg -i input.mp4 -vf "fps=1/10,scale=320:-1" thumb%04d.jpg
```

## 自适应流媒体

### 1. HLS (HTTP Live Streaming)

```bash
# 生成 HLS 流
# 需要 ffmpeg 和 Nginx/Apache 支持

# 1. 生成多个分辨率
ffmpeg -i input.mp4 -vf scale=1920:1080 -c:v libx264 -crf 28 -f hls -hls_time 10 -hls_list_size 0 stream_1080p.m3u8
ffmpeg -i input.mp4 -vf scale=1280:720 -c:v libx264 -crf 28 -f hls -hls_time 10 -hls_list_size 0 stream_720p.m3u8
ffmpeg -i input.mp4 -vf scale=854:480 -c:v libx264 -crf 28 -f hls -hls_time 10 -hls_list_size 0 stream_480p.m3u8

# 2. 创建主播放列表
# master.m3u8
# #EXTM3U
# #EXT-X-VERSION:3
# #EXT-X-STREAM-INF:BANDWIDTH=5000000,RESOLUTION=1920x1080
# stream_1080p.m3u8
# #EXT-X-STREAM-INF:BANDWIDTH=3000000,RESOLUTION=1280x720
# stream_720p.m3u8
# #EXT-X-STREAM-INF:BANDWIDTH=1500000,RESOLUTION=854x480
# stream_480p.m3u8
```

```vue
<template>
  <video width="800" height="450" controls>
    <source src="/stream.m3u8" type="application/x-mpegURL">
  </video>
</template>
```

### 2. 使用视频 CDN

```vue
<script setup lang="ts">
// 使用 Mux、Cloudinary 或其他视频服务
const videoUrl = computed(() => {
  // 根据带宽自动选择质量
  return isSlowConnection.value
    ? 'https://cdn.example.com/video-480p.m3u8'
    : 'https://cdn.example.com/video-1080p.m3u8'
})
</script>

<template>
  <video width="800" height="450" controls>
    <source :src="videoUrl" type="application/x-mpegURL">
  </video>
</template>
```

## 背景视频优化

### 1. 静音背景视频

```vue
<template>
  <div class="hero-section">
    <!-- 背景视频 -->
    <video
      autoplay
      muted
      loop
      playsinline
      class="background-video"
      poster="/hero-poster.jpg"
    >
      <source src="/background.webm" type="video/webm">
      <source src="/background.mp4" type="video/mp4">
    </video>

    <!-- 内容 -->
    <div class="content">
      <h1>Welcome</h1>
    </div>
  </div>
</template>

<style scoped>
.hero-section {
  position: relative;
  height: 100vh;
  overflow: hidden;
}

.background-video {
  position: absolute;
  top: 50%;
  left: 50%;
  min-width: 100%;
  min-height: 100%;
  transform: translate(-50%, -50%);
  object-fit: cover;
}

.content {
  position: relative;
  z-index: 1;
}
</style>
```

### 2. 懒加载背景视频

```vue
<script setup lang="ts">
const videoLoaded = ref(false)

onMounted(() => {
  // 延迟加载背景视频
  setTimeout(() => {
    videoLoaded.value = true
  }, 1000)
})
</script>

<template>
  <div class="hero">
    <!-- 先显示封面 -->
    <img
      v-if="!videoLoaded"
      src="/hero-poster.jpg"
      class="hero-poster"
      alt="Hero"
    />

    <!-- 再加载视频 -->
    <video
      v-if="videoLoaded"
      autoplay
      muted
      loop
      playsinline
      class="hero-video"
    >
      <source src="/background.webm" type="video/webm">
      <source src="/background.mp4" type="video/mp4">
    </video>
  </div>
</template>
```

## 视频性能监控

### 1. 监控加载时间

```typescript
// plugins/video-monitor.client.ts
export default defineNuxtPlugin(() => {
  if (process.env.NODE_ENV === 'development') {
    const videos = document.querySelectorAll('video')

    videos.forEach(video => {
      video.addEventListener('loadstart', () => {
        console.log('开始加载:', video.src)
      })

      video.addEventListener('loadedmetadata', () => {
        console.log('元数据加载完成')
      })

      video.addEventListener('canplay', () => {
        console.log('可以播放')
      })

      video.addEventListener('canplaythrough', () => {
        console.log('可以流畅播放')
      })
    })
  }
})
```

### 2. 使用 Performance API

```typescript
// 监控视频资源
const observer = new PerformanceObserver((list) => {
  for (const entry of list.getEntries()) {
    if (entry.initiatorType === 'video') {
      console.log('视频加载时间:', entry.duration)
      console.log('视频大小:', entry.transferSize, 'bytes')
    }
  }
})

observer.observe({ entryTypes: ['resource'] })
```

## 最佳实践

### ✅ 推荐做法

1. **使用现代格式**
   ```vue
   <source src="/video.webm" type="video/webm">
   <source src="/video.mp4" type="video/mp4">
   ```

2. **提供封面图**
   ```vue
   <video poster="/video-poster.jpg">
   ```

3. **懒加载视频**
   ```vue
   <video loading="lazy">
   ```

4. **压缩视频**
   ```bash
   ffmpeg -i input.mov -c:v libx264 -crf 28 output.mp4
   ```

5. **响应式分辨率**
   ```vue
   <source src="/video-480p.mp4" media="(max-width: 768px)">
   <source src="/video-1080p.mp4">
   ```

### ❌ 避免做法

1. **自动播放有声视频**
   ```vue
   <!-- ❌ 用户体验差 -->
   <video autoplay>

   <!-- ✅ 静音自动播放 -->
   <video autoplay muted>
   ```

2. **未压缩视频**
   ```bash
   # ❌ 直接上传原始视频 (几百MB)
   # ✅ 压缩后上传 (几MB)
   ffmpeg -i input.mov -crf 28 output.mp4
   ```

3. **忽略移动端**
   ```vue
   <!-- ❌ 移动端也加载 4K 视频 -->
   <source src="/video-4k.mp4">

   <!-- ✅ 响应式 -->
   <source src="/video-480p.mp4" media="(max-width: 768px)">
   <source src="/video-1080p.mp4">
   ```

## 检查清单

```markdown
## 视频优化清单

### 格式
- [ ] 使用 WebM/AV1 格式
- [ ] 提供 MP4 后备
- [ ] 压缩视频 (CRF 23-28)

### 加载
- [ ] 懒加载非首屏视频
- [ ] 提供视频封面
- [ ] 预加载关键视频

### 性能
- [ ] 响应式分辨率
- [ ] 使用自适应流
- [ ] 限制视频大小 (< 10MB)

### 用户体验
- [ ] 不要自动播放有声视频
- [ ] 提供控制选项
- [ ] 考虑移动端流量
```

## 参考资源

- [Video Optimization Guide](https://web.dev/fast/#optimize-your-media)
- [FFmpeg Documentation](https://ffmpeg.org/documentation.html)
- [HLS Authoring Specification](https://datatracker.ietf.org/doc/html/rfc8216)
- [WebM Project](https://www.webmproject.org/)
