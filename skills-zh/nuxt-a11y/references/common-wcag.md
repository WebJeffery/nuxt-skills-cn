# WCAG 2.1 常见要求

## WCAG 2.1 概述

WCAG 2.1 (Web Content Accessibility Guidelines) 是 Web 无障碍的国际标准,包含 4 个原则:

1. **Perceivable(可感知)** - 用户必须能够感知信息
2. **Operable(可操作)** - 用户必须能够操作 UI
3. **Understandable(可理解)** - 信息和操作必须可理解
4. **Robust(健壮)** - 内容必须足够健壮

## 可感知 (Perceivable)

### 1.1 文本替代

**级别: A**

```vue
<template>
  <!-- ✅ 所有非文本内容有替代文本 -->
  <img src="/chart.jpg" alt="2024年销售增长30%的柱状图" />

  <!-- ✅ 装饰性图片使用空 alt -->
  <img src="/divider.png" alt="" role="presentation" />

  <!-- ✅ 图标按钮有 aria-label -->
  <button aria-label="关闭">
    <icon-close />
  </button>

  <!-- ❌ 缺少 alt -->
  <img src="/photo.jpg" />
</template>
```

### 1.2 时基媒体

**级别: A**

```vue
<template>
  <!-- ✅ 音频有替代内容 -->
  <audio controls>
    <source src="/audio.mp3" type="audio/mpeg" />
    <track kind="captions" src="/captions.vtt" srclang="zh" />
    <a href="/transcript.html">文字记录</a>
  </audio>

  <!-- ✅ 视频有字幕 -->
  <video controls>
    <source src="/video.mp4" type="video/mp4" />
    <track kind="captions" src="/captions.vtt" srclang="zh" />
  </video>

  <!-- ✅ 音频描述 -->
  <video>
    <track kind="descriptions" src="/audio-desc.vtt" />
  </video>
</template>
```

### 1.3 可适应性

**级别: AA**

```vue
<template>
  <!-- ✅ 可以用不同方式呈现信息 -->
  <div>
    <img src="/info-graphic.jpg" alt="信息图" />
    <p>文字版本的详细信息...</p>
  </div>

  <!-- ✅ 响应式设计 -->
  <div class="responsive-layout">
    <!-- 在不同视口大小下重新排列 -->
  </div>
</template>

<style>
/* ✅ 避免水平滚动(除非必要) */
@media (max-width: 768px) {
  .container {
    max-width: 100vw;
    overflow-x: hidden;
  }
}
</style>
```

### 1.4 可辨别性

**级别: AA**

```vue
<template>
  <!-- ✅ 文本和背景有足够对比度 -->
  <p style="color: #333; background: #fff">
    对比度 ≥ 4.5:1
  </p>

  <!-- ✅ 可以禁用文字缩放限制 -->
  <div style="max-width: 100%">
    <!-- 不固定宽度 -->
  </div>

  <!-- ✅ 图像不必依赖颜色 -->
  <button class="error">
    <span class="icon" aria-hidden="true">❌</span>
    <span>错误 (红色)</span>
  </button>
</template>
```

## 可操作 (Operable)

### 2.1 键盘可访问

**级别: A**

```vue
<template>
  <!-- ✅ 所有功能可通过键盘访问 -->
  <button @click="doSomething">点击</button>

  <!-- ✅ 自定义组件支持键盘 -->
  <div
    role="button"
    tabindex="0"
    @click="doSomething"
    @keydown.enter="doSomething"
    @keydown.space.prevent="doSomething"
  >
    自定义按钮
  </div>

  <!-- ❌ 只能鼠标操作 -->
  <div @click="doSomething">点击</div>
</template>
```

### 2.2 足够时间

**级别: AA**

```vue
<script setup lang="ts>
const showTimeout = ref(false)

// ✅ 允许用户延长会话超时
const extendSession = () => {
  clearTimeout(timeoutId)
  timeoutId = setTimeout(() => {
    showTimeout.value = true
  }, 60000) // 1分钟后超时
}

// ✅ 可调整超时时间
const timeoutDuration = ref(60000)
</script>

<template>
  <!-- ✅ 警告即将超时 -->
  <div v-if="showTimeout" role="alert">
    <p>您的会话即将超时</p>
    <button @click="extendSession">延长时间</button>
  </div>

  <!-- ✅ 可暂停自动播放 -->
  <button @click="toggleAutoplay">
    {{ autoplay ? '暂停' : '播放' }}
  </button>
</template>
```

### 2.3 癫痫和身体反应

**级别: A**

```vue
<template>
  <!-- ❌ 避免闪烁内容(超过3次/秒) -->
  <div class="blinking">闪烁文本</div>

  <!-- ✅ 提供控制 -->
  <button @click="disableAnimation">禁用动画</button>

  <!-- ✅ 尊重用户偏好 */
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
</template>
```

### 2.4 可导航性

**级别: AA**

```vue
<template>
  <!-- ✅ 跳过链接 -->
  <a href="#main-content" class="skip-link">
    跳到主要内容
  </a>

  <main id="main-content">
    <!-- 主要内容 -->
  </main>

  <!-- ✅ 页面标题 -->
  <h1>页面主标题(每页一个)</h1>

  <!-- ✅ 焦点指示器可见 */
button:focus-visible {
  outline: 3px solid #0052cc;
  outline-offset: 2px;
}

/* ✅ 焦点位置合理 */
button:focus-visible {
  outline: 3px solid #0052cc;
  outline-offset: 2px;
}
</template>
```

## 可理解 (Understandable)

### 3.1 可读性

**级别: AA**

```vue
<script setup lang="ts>
// ✅ 声明页面语言
useHead({
  htmlAttrs: {
    lang: 'zh-CN'
  }
})

// ✅ 部分使用不同语言
</script>

<template>
  <p lang="en">This is English text.</p>
  <p>这是中文文本。</p>

  <!-- ✅ 解释术语 -->
  <abbr title="Web Content Accessibility Guidelines">
    WCAG
  </abbr>

  <!-- ✅ 表单验证提供清晰错误 -->
  <input
    v-model="email"
    aria-invalid="true"
    aria-describedby="error-message"
  />
  <span id="error-message" role="alert">
    请输入有效的邮箱地址
  </span>
</template>
```

### 3.2 可预测性

**级别: AA**

```vue
<script setup lang="ts>
// ❌ 避免焦点意外改变
const unexpectedFocusChange = () => {
  // 不要在用户输入时自动移动焦点
}

// ✅ 上下文变化前提示
const confirmNavigation = () => {
  if (hasUnsavedChanges.value) {
    return '您有未保存的更改,确定要离开吗?'
  }
}
</script>

<template>
  <!-- ✅ 相同功能在不同页面保持一致 -->
  <button class="btn-primary">主要操作</button>

  <!-- ✅ 导航一致 -->
  <nav>
    <NuxtLink to="/">首页</NuxtLink>
    <NuxtLink to="/about">关于</NuxtLink>
  </nav>
</template>
```

### 3.3 输入协助

**级别: AA**

```vue
<template>
  <!-- ✅ 必填字段有标识 -->
  <label for="email">
    电子邮箱 <span aria-label="必填">*</span>
  </label>
  <input
    id="email"
    type="email"
    required
    aria-required="true"
  />

  <!-- ✅ 提供输入格式提示 -->
  <label for="phone">
    电话号码 (格式: 138-xxxx-xxxx)
  </label>
  <input
    id="phone"
    type="tel"
    pattern="[0-9]{3}-[0-9]{4}-[0-9]{4}"
    aria-describedby="phone-hint"
  />
  <span id="phone-hint">格式: 138-xxxx-xxxx</span>

  <!-- ✅ 错误建议 -->
  <div role="alert" aria-live="polite">
    <span v-if="errors.email">{{ errors.email }}</span>
  </div>
</template>
```

## 健壮 (Robust)

### 4.1 兼容性

**级别: A**

```vue
<template>
  <!-- ✅ 使用有效的 HTML -->
  <button type="button">点击</button>

  <!-- ✅ 正确嵌套元素 -->
  <ul>
    <li>项目 1</li>
    <li>项目 2</li>
  </ul>

  <!-- ✅ 使用唯一 ID -->
  <label for="email-1">邮箱</label>
  <input id="email-1" type="email" />

  <!-- ❌ 无效 HTML -->
  <button type="submit">提交</button>
  <button type="button">取消</button>
  <!-- 按钮不应嵌套 -->

  <!-- ✅ ARIA 属性正确 -->
  <button aria-label="关闭">&times;</button>

  <!-- ❌ 不正确的 ARIA -->
  <div role="button" aria-pressed="true">
    <!-- aria-pressed 需要 aria-haspopup -->
  </div>
</template>
```

## 优先级建议

### 优先修复(A 级)

1. 所有图片有 alt 文本
2. 所有功能可键盘访问
3. 表单有 label 关联
4. 使用有效的 HTML
5. 不使用会诱发癫痫的闪烁内容

### 尽快修复(AA 级)

1. 文本对比度 ≥ 4.5:1
2. 调整文本大小不影响布局
3. 键盘焦点可见
4. 错误标识清晰
5. 页面标题唯一

### 优化修复(AAA 级)

1. 文本对比度 ≥ 7:1
2. 所有内容可由屏幕阅读器访问
3. 上下文变化明确提示
4. 提供高级错误恢复

## 快速检查清单

```markdown
## WCAG 2.1 AA 快速检查

- [ ] 页面有 title
- [ ] 页面有 lang 属性
- [ ] 所有图片有 alt
- [ ] 所有输入有 label
- [ ] 链接目的清晰
- [ ] 文本对比度 ≥ 4.5:1
- [ ] 键盘可访问所有功能
- [ ] 焦点指示器可见
- [ ] 错误消息清晰
- [ ] 表单验证有帮助
```

## 测试工具

- **axe DevTools** - 浏览器扩展
- **WAVE** - 在线工具
- **Lighthouse** - Chrome 内置
- **WebAIM Contrast Checker** - 对比度检查

## 参考资源

- [WCAG 2.1 官方标准](https://www.w3.org/WAI/WCAG21/quickref/)
- [WebAIM WCAG 检查表](https://webaim.org/standards/wcag/checklist)
- [WCAG 2.1 理解](https://www.w3.org/WAI/WCAG21/Understanding/)
