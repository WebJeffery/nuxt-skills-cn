# 第三方脚本加载优化完整指南

## 为什么第三方脚本优化很重要

**影响:**
- 阻塞主线程: 导致 FID 差
- 增加 CLS: 动态插入内容
- 降低性能: 大型脚本 (>100KB)

**常见第三方脚本:**
- Google Analytics
- Facebook Pixel
- 聊天 widget (Intercom, Drift)
- A/B 测试工具 (Optimizely)
- 广告代码
- 社交分享按钮

## 加载策略

### 1. defer vs async vs 正常加载

```vue
<template>
  <head>
    <!-- ❌ 正常加载: 阻塞渲染 -->
    <script src="/analytics.js"></script>

    <!-- ✅ async: 异步加载,不保证顺序 -->
    <script async src="/analytics.js"></script>

    <!-- ✅ defer: 异步加载,保持顺序 -->
    <script defer src="/analytics.js"></script>
  </head>
</template>
```

**对比:**
- **正常加载**: 阻塞 HTML 解析
- **async**: 尽快加载执行,不保证顺序
- **defer**: 在 DOM 准备好后按顺序执行 (推荐)

### 2. 使用 Nuxt scripts

```bash
npm install @nuxt/scripts
```

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@nuxt/scripts'],

  scripts: {
    registry: {
      // Google Analytics
      gtag: {
        id: 'G-XXXXXXXXXX',
      },
    },
  },
})
```

### 3. 延迟加载

```vue
<script setup lang="ts">
// 延迟加载第三方脚本
onMounted(() => {
  // 等待关键资源加载完成
  setTimeout(() => {
    loadAnalytics()
  }, 2000)
})

const loadAnalytics = () => {
  const script = document.createElement('script')
  script.async = true
  script.src = 'https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX'
  document.head.appendChild(script)

  // 初始化
  window.dataLayer = window.dataLayer || []
  function gtag() {
    dataLayer.push(arguments)
  }
  gtag('js', new Date())
  gtag('config', 'G-XXXXXXXXXX')
}
</script>
```

## 具体优化案例

### 1. Google Analytics

```vue
<script setup lang="ts">
// 方案 1: 使用官方模块
// npm install @nuxtjs/google-analytics

// nuxt.config.ts
/*
export default defineNuxtConfig({
  modules: ['@nuxtjs/google-analytics'],
  googleAnalytics: {
    id: 'G-XXXXXXXXXX'
  }
})
*/

// 方案 2: 手动延迟加载
onMounted(() => {
  // 只在生产环境加载
  if (process.env.NODE_ENV === 'production') {
    setTimeout(() => {
      loadGoogleAnalytics()
    }, 3000)
  }
})

const loadGoogleAnalytics = () => {
  // 创建 script 标签
  const script = document.createElement('script')
  script.async = true
  script.src = `https://www.googletagmanager.com/gtag/js?id=${GA_ID}`
  document.head.appendChild(script)

  // 初始化
  window.dataLayer = window.dataLayer || []
  function gtag() {
    dataLayer.push(arguments)
  }
  gtag('js', new Date())
  gtag('config', GA_ID)
}
</script>
```

### 2. Google Tag Manager

```vue
<script setup lang="ts">
const GTM_ID = 'GTM-XXXXXXX'

// 方案 1: 延迟加载
onMounted(() => {
  if (process.env.NODE_ENV === 'production') {
    loadGTM()
  }
})

const loadGTM = () => {
  // 加载 GTM 脚本
  (function(w,d,s,l,i){
    w[l]=w[l]||[];w[l].push({'gtm.start':
    new Date().getTime(),event:'gtm.js'});
    var f=d.getElementsByTagName(s)[0],
    j=d.createElement(s),dl=l!='dataLayer'?'&l='+l:'';
    j.async=true;j.src='https://www.googletagmanager.com/gtm.js?id='+i+dl;
    f.parentNode.insertBefore(j,f);
  })(window,document,'script','dataLayer',GTM_ID)
}
</script>

<template>
  <!-- GTM noscript (iframe) -->
  <body>
    <noscript v-if="process.env.NODE_ENV === 'production'">
      <iframe
        :src="`https://www.googletagmanager.com/ns.html?id=${GTM_ID}`"
        height="0"
        width="0"
        style="display:none;visibility:hidden"
      ></iframe>
    </noscript>
  </body>
</template>
```

### 3. Facebook Pixel

```vue
<script setup lang="ts>
onMounted(() => {
  if (process.env.NODE_ENV === 'production') {
    setTimeout(() => {
      loadFacebookPixel()
    }, 2000)
  }
})

const loadFacebookPixel = () => {
  // 加载 Facebook Pixel
  !(function(f, b, e, v, n, t, s) {
    if (f.fbq) return
    n = f.fbq = function() {
      n.callMethod ? n.callMethod.apply(n, arguments) : n.queue.push(arguments)
    }
    if (!f._fbq) f._fbq = n
    n.push = n
    n.loaded = !0
    n.version = '2.0'
    n.queue = []
    t = b.createElement(e)
    t.async = !0
    t.src = v
    s = b.getElementsByTagName(e)[0]
    s.parentNode.insertBefore(t, s)
  })(window, document, 'script', 'https://connect.facebook.net/en_US/fbevents.js')

  fbq('init', 'YOUR_PIXEL_ID')
  fbq('track', 'PageView')
}
</script>
```

### 4. 聊天 Widget (Intercom)

```vue
<script setup lang="ts>
// 延迟加载聊天 widget
const showChat = ref(false)

onMounted(() => {
  // 用户交互后再加载
  const timer = setTimeout(() => {
    showChat.value = true
    loadIntercom()
  }, 5000)

  // 或用户点击后才加载
  window.addEventListener('scroll', () => {
    if (!showChat.value) {
      showChat.value = true
      loadIntercom()
    }
  }, { once: true })
})

const loadIntercom = () => {
  const APP_ID = 'YOUR_APP_ID'

  window.intercomSettings = {
    app_id: APP_ID,
  }

  ;(function() {
    var w = window
    var ic = w.Intercom
    if (typeof ic === 'function') {
      ic('reattach_activator')
      ic('update', w.intercomSettings)
    } else {
      var d = document
      var i = function() {
        i.c(arguments)
      }
      i.q = []
      i.c = function(args) {
        i.q.push(args)
      }
      w.Intercom = i
      var l = function() {
        var s = d.createElement('script')
        s.type = 'text/javascript'
        s.async = true
        s.src = 'https://widget.intercom.io/widget/' + APP_ID
        var x = d.getElementsByTagName('script')[0]
        x.parentNode.insertBefore(s, x)
      }
      if (document.readyState === 'complete') {
        l()
      } else if (w.attachEvent) {
        w.attachEvent('onload', l)
      } else {
        w.addEventListener('load', l, false)
      }
    }
  })()
}
</script>
```

### 5. YouTube/Vimeo 嵌入

```vue
<script setup lang="ts">
// 延迟加载视频
const videoLoaded = ref(false)
const thumbnailLoaded = ref(false)

const loadVideo = () => {
  videoLoaded.value = true

  // 动态加载 YouTube API
  const tag = document.createElement('script')
  tag.src = 'https://www.youtube.com/iframe_api'
  document.head.appendChild(tag)
}
</script>

<template>
  <div class="video-container">
    <!-- 缩略图 -->
    <div
      v-if="!videoLoaded"
      class="video-thumbnail"
      @click="loadVideo"
    >
      <img
        :src="`https://img.youtube.com/vi/${videoId}/maxresdefault.jpg`"
        alt="Video thumbnail"
        loading="lazy"
      />
      <button class="play-button">▶</button>
    </div>

    <!-- 视频 iframe -->
    <iframe
      v-if="videoLoaded"
      :src="`https://www.youtube.com/embed/${videoId}?autoplay=1`"
      frameborder="0"
      allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
      allowfullscreen
    ></iframe>
  </div>
</template>

<style scoped>
.video-container {
  position: relative;
  aspect-ratio: 16 / 9;
}

.video-thumbnail {
  cursor: pointer;
}

.video-thumbnail img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.play-button {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 3rem;
  color: white;
  background: rgba(0, 0, 0, 0.7);
  border: none;
  border-radius: 50%;
  width: 80px;
  height: 80px;
}
</style>
```

## 隐私优化

### 1. 用户同意后加载

```vue
<script setup lang="ts>
const userConsent = ref(false)
const analyticsLoaded = ref(false)

// 监听用户同意
watch(userConsent, (consent) => {
  if (consent && !analyticsLoaded.value) {
    loadAnalytics()
    analyticsLoaded.value = true
  }
})

const loadAnalytics = () => {
  // 加载分析脚本
  const script = document.createElement('script')
  script.async = true
  script.src = '/analytics.js'
  document.head.appendChild(script)
}
</script>

<template>
  <!-- Cookie 同意横幅 -->
  <div v-if="!userConsent" class="cookie-banner">
    <p>我们使用 cookies 来改善您的体验。</p>
    <button @click="userConsent = true">接受</button>
  </div>
</template>
```

### 2. 使用 Cookiebot

```vue
<script setup lang="ts>
// 只在用户同意后加载
onMounted(() => {
  window.addEventListener('CookiebotOnAccept', () => {
    if (window.Cookiebot.consent.statistics) {
      loadAnalytics()
    }

    if (window.Cookiebot.consent.marketing) {
      loadMarketingScripts()
    }
  })
})
</script>
```

## 性能监控

### 1. 监控脚本加载时间

```typescript
// plugins/third-party-monitor.client.ts
export default defineNuxtPlugin(() => {
  if (process.env.NODE_ENV === 'development') {
    const observer = new PerformanceObserver((list) => {
      for (const entry of list.getEntries()) {
        if (entry.entryType === 'resource') {
          const resource = entry as PerformanceResourceTiming

          // 监控第三方域名
          if (['googletagmanager.com', 'facebook.net', 'intercom.io'].some(d =>
            resource.name.includes(d)
          )) {
            console.log(`Third-party: ${resource.name}`)
            console.log(`Duration: ${resource.duration}ms`)
            console.log(`Size: ${resource.transferSize} bytes`)
          }
        }
      }
    })

    observer.observe({ entryTypes: ['resource'] })
  }
})
```

### 2. 监控 CLS 影响

```typescript
// 监控第三方脚本导致的 CLS
import { getCLS } from 'web-vitals'

getCLS((metric) => {
  if (metric.value > 0.1) {
    console.warn('High CLS detected, possibly caused by third-party scripts')
  }
})
```

## 最佳实践

### ✅ 推荐做法

1. **延迟加载**
   ```typescript
   setTimeout(() => loadScript(), 2000)
   ```

2. **用户交互后加载**
   ```typescript
   button.addEventListener('click', loadScript)
   ```

3. **使用 defer/async**
   ```vue
   <script defer src="/script.js"></script>
   ```

4. **仅生产环境**
   ```typescript
   if (process.env.NODE_ENV === 'production')
   ```

5. **请求用户同意**
   ```vue
   <CookieConsent @accept="loadScripts" />
   ```

### ❌ 避免做法

1. **同步加载**
   ```vue
   <!-- ❌ 阻塞渲染 -->
   <script src="/analytics.js"></script>
   ```

2. **首屏加载**
   ```typescript
   // ❌ 立即加载所有脚本
   onMounted(() => {
     loadAllScripts()
   })

   // ✅ 延迟加载
   onMounted(() => {
     setTimeout(() => loadScripts(), 3000)
   })
   ```

3. **忽略隐私**
   ```typescript
   // ❌ 不请求同意直接加载
   loadAnalytics()

   // ✅ 请求用户同意
   if (userConsent) loadAnalytics()
   ```

## 检查清单

```markdown
## 第三方脚本优化清单

### 加载策略
- [ ] 使用 defer/async
- [ ] 延迟非关键脚本
- [ ] 用户交互后加载

### 性能
- [ ] 只在生产环境加载
- [ ] 避免阻塞渲染
- [ ] 监控加载时间

### 隐私
- [ ] 请求用户同意
- [ ] 提供 opt-out 选项
- [ ] 符合 GDPR/CCPA

### 维护
- [ ] 定期审查第三方脚本
- [ ] 移除未使用的脚本
- [ ] 寻找更轻量的替代品
```

## 替代方案

### 1. 自托管分析

```bash
# 使用 Plausible (隐私友好, < 1KB)
docker run -p 8080:80 plausible/analytics
```

### 2. 轻量替代品

```typescript
// ❌ Google Analytics (45KB)
// ✅ Plausible Analytics (1KB)
// ✅ Simple Analytics (1KB)
```

### 3. 禁用不必要的功能

```javascript
// 禁用某些功能
gtag('config', 'G-XXXXXXXXXX', {
  'anonymize_ip': true,
  'send_page_view': false,
})
```

## 参考资源

- [Third-Party Web Impact](https://web.dev/third-party-summit/)
- [Loading Third-Party JavaScript](https://developer.chrome.com/docs/lighthouse/performance/third-party-summary/)
- [Defer Non-Critical JavaScript](https://web.fast.com/defer-non-critical-javascript/)
- [Privacy-Friendly Analytics](https://plausible.io/)
