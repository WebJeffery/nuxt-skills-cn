---
name: nuxt-a11y
description: 专为 Nuxt.js 项目实施无障碍访问(Accessibility/a11y)最佳实践的技能。
  帮助开发者创建符合 WCAG 2.1 标准的无障碍 Web 应用,支持屏幕阅读器、键盘导航、语音控制等辅助技术。
  涵盖语义化 HTML、ARIA 属性、键盘交互、焦点管理、颜色对比度等内容。

  此技能应在以下情况使用:
  - 用户要求"Nuxt 无障碍"、"Nuxt a11y"、"Web accessibility"
  - 需要提升网站的无障碍性
  - 需要通过 WCAG 2.1 AA 或 AAA 级别认证
  - 需要支持屏幕阅读器或键盘导航
  - 需要修复无障碍审计发现的问题

allowed-tools:
  - Read
  - Write
  - Edit
  - AskUserQuestion
metadata:
  trigger: Nuxt 无障碍、a11y、accessibility、WCAG、ARIA
  version: 1.0
---

# Nuxt a11y: Nuxt.js 无障碍最佳实践技能

## 技能目的

为 Nuxt.js 项目提供全面的无障碍访问解决方案,确保所有用户(包括使用辅助技术的��户)都能顺利访问和使用网站内容。

## 使用时机

### 触发关键词
- "Nuxt 无障碍"、"Nuxt a11y"、"Nuxt accessibility"
- "WCAG 2.1"、"ARIA 属性"
- "屏幕阅读器"、"键盘导航"
- "无障碍审计"、"a11y test"
- "颜色对比度"、"焦点管理"

### 项目类型
- Nuxt 3/4 项目
- 需要符合政府/企业无障碍标准的项目
- 面向公众的 Web 应用
- 需要支持多样化用户群体的产品

### 优化场景
- 新项目需要建立无障碍基础
- 现有项目无障碍得分低
- 需要通过 WCAG 2.1 认证
- 需要支持辅助技术(屏幕阅读器、盲文设备等)
- 需要优化键盘交互体验

## 核心原则

无障碍设计的 4 个核心原则(POUR):

1. **Perceivable(可感知)** - 信息和 UI 组件必须以用户能够感知的方式呈现
2. **Operable(可操作)** - UI 组件和导航必须可操作
3. **Understandable(可理解)** - 信息和 UI 操作必须可理解
4. **Robust(健壮)** - 内容必须足够健壮,可被各种用户代理(包括辅助技术)可靠地解释

## WCAG 2.1 合规级别

- **A 级** - 基础无障碍要求
- **AA 级** - 推荐级别,大多数组织的标准
- **AAA 级** - 最高级别,难以完全达到

## Nuxt 3 无障碍配置

### Step 1: 安装无障碍工具

```bash
# 推荐: 安装 @nuxt/a11y (开发环境实时检查)
npm install --save-dev @nuxt/a11y

# 或安装 @nuxtjs/color-mode (��持深色/浅色模式)
npm install @nuxtjs/color-mode

# 可选: 安装 axe-core 用于无障碍测试
npm install --save-dev @axe-core/vue
```

### Step 2: 配置 @nuxt/a11y (推荐)

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  // 仅在开发环境启用
  modules: process.env.NODE_ENV === 'development'
    ? ['@nuxt/a11y']
    : [],

  a11y: {
    // 实时无障碍检查
    visualInspector: true,
    console: true,
  },
})
```

### Step 2 (备选): 配置基础设置

### Step 3: 使用无障碍友好的组件

```vue
<template>
  <!-- 语义化 HTML -->
  <header>
    <nav aria-label="主导航">
      <ul>
        <li><NuxtLink to="/">首页</NuxtLink></li>
        <li><NuxtLink to="/about">关于</NuxtLink></li>
      </ul>
    </nav>
  </header>

  <main>
    <h1>页面标题</h1>
    <article>
      <p>内容...</p>
    </article>
  </main>
</template>
```

## 关键无障碍实践

### 1. 语义化 HTML

**使用正确的 HTML 元素:**

```vue
<template>
  <!-- ✅ 正确: 语义化元素 -->
  <header>
    <nav aria-label="主导航">
      <ul>
        <li><a href="/">首页</a></li>
      </ul>
    </nav>
  </header>

  <main>
    <article>
      <h1>文章标题</h1>
      <p>文章内容...</p>
    </article>

    <aside>
      <h2>侧边栏</h2>
    </aside>
  </main>

  <footer>
    <p>&copy; 2024 版权所有</p>
  </footer>

  <!-- ❌ 错误: 过度使用 div -->
  <div class="header">
    <div class="nav">
      <div class="item">首页</div>
    </div>
  </div>
</template>
```

### 2. 标题层级

**正确的标题结构:**

```vue
<template>
  <h1>页面主标题 (每页只有一个)</h1>

  <section>
    <h2>第一部分</h2>
    <p>内容...</p>

    <section>
      <h3>子部分</h3>
      <p>内容...</p>
    </section>
  </section>

  <section>
    <h2>第二部分</h2>
    <p>内容...</p>
  </section>
</template>
```

**❌ 避免:**
```vue
<template>
  <h1>主标题</h1>
  <h3>跳过 h2</h3>
  <h4>再跳过一级</h4>
</template>
```

### 3. 图片替代文本

```vue
<template>
  <!-- 信息性图片 -->
  <img
    src="/chart.jpg"
    alt="2024年销售增长30%的柱状图"
  />

  <!-- 装饰性图片 -->
  <img
    src="/decorative.png"
    alt=""
    role="presentation"
  />

  <!-- 使用 Nuxt Image -->
  <NuxtImg
    src="/product.jpg"
    alt="红色连衣裙,短袖,V领"
    width="400"
    height="600"
  />
</template>
```

### 4. 表单无障碍

```vue
<template>
  <form @submit.prevent="handleSubmit">
    <!-- 必须关联 label 和 input -->
    <div>
      <label for="email">电子邮箱:</label>
      <input
        id="email"
        v-model="email"
        type="email"
        required
        aria-describedby="email-hint"
      />
      <span id="email-hint">我们将向您发送验证链接</span>
    </div>

    <!-- 错误提示 -->
    <div role="alert" aria-live="polite">
      <span v-if="error" class="error">{{ error }}</span>
    </div>

    <!-- 提交按钮 -->
    <button type="submit">注册</button>
  </form>
</template>
```

### 5. ARIA 属性

```vue
<template>
  <!-- 展开/收起组件 -->
  <button
    @click="toggle"
    :aria-expanded="isExpanded"
    aria-controls="content-panel"
  >
    {{ isExpanded ? '收起' : '展开' }}
  </button>

  <div
    id="content-panel"
    v-if="isExpanded"
    role="region"
    aria-labelledby="panel-title"
  >
    <h2 id="panel-title">面板标题</h2>
    <p>内容...</p>
  </div>

  <!-- 加载状态 -->
  <div
    v-if="loading"
    role="status"
    aria-live="polite"
    aria-busy="true"
  >
    正在加载...
  </div>
</template>
```

### 6. 键盘导航

```vue
<script setup lang="ts">
const handleKeydown = (event: KeyboardEvent) => {
  switch (event.key) {
    case 'Enter':
    case ' ':
      // 处理激活
      break
    case 'Escape':
      // 关闭弹窗/菜单
      break
    case 'ArrowDown':
      event.preventDefault()
      // 移动到下一项
      break
    case 'ArrowUp':
      event.preventDefault()
      // 移动到上一项
      break
  }
}
</script>

<template>
  <button
    @click="handleClick"
    @keydown="handleKeydown"
    tabindex="0"
  >
    可聚焦元素
  </button>
</template>
```

### 7. 焦点管理

```vue
<script setup lang="ts">
import { ref, watch } from 'vue'

const modalRef = ref<HTMLElement>()
const previousActiveElement = ref<HTMLElement>()

const openModal = () => {
  // 保存当前焦点元素
  previousActiveElement.value = document.activeElement as HTMLElement

  // 打开模态框后聚焦到第一个可交互元素
  nextTick(() => {
    const firstFocusable = modalRef.value?.querySelector(
      'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
    )
    firstFocusable?.focus()
  })
}

const closeModal = () => {
  // 关闭时恢复焦点
  previousActiveElement.value?.focus()
}
</script>

<template>
  <div
    v-if="isOpen"
    ref="modalRef"
    role="dialog"
    aria-modal="true"
    aria-labelledby="modal-title"
  >
    <h2 id="modal-title">模态框标题</h2>
    <button @click="closeModal">关闭</button>
  </div>
</template>
```

### 8. 颜色对比度

**使用工具检查对比度:**

```typescript
// 确保文本和背景的对比度至少为 4.5:1 (正常文本)
// 或 3:1 (大文本)

// ✅ 正确
const styles = {
  color: '#333333', // 深灰色
  backgroundColor: '#FFFFFF', // 白色
  // 对比度: 12.6:1 ✓
}

// ❌ 错误
const styles = {
  color: '#CCCCCC', // 浅灰色
  backgroundColor: '#FFFFFF', // 白色
  // 对比度: 1.6:1 ✗
}
```

**在线工具:**
- WebAIM Contrast Checker
- Colour Contrast Analyser (CCA)

### 9. 跳过链接

```vue
<template>
  <!-- "跳到主要内容"链接 -->
  <a
    href="#main-content"
    class="skip-link"
  >
    跳到主要内容
  </a>

  <header>...</header>

  <main id="main-content">
    <!-- 主要内容 -->
  </main>
</template>

<style scoped>
.skip-link {
  position: absolute;
  top: -40px;
  left: 0;
  padding: 8px;
  background: #000;
  color: #fff;
  text-decoration: none;
  z-index: 100;
}

.skip-link:focus {
  top: 0;
}
</style>
```

### 10. 响应式设计

```vue
<template>
  <!-- 确保触摸目标至少 44x44 像素 -->
  <button
    class="touch-friendly"
    @click="handleClick"
  >
    点击
  </button>
</template>

<style scoped>
.touch-friendly {
  min-width: 44px;
  min-height: 44px;
  /* 移动设备友好 */
}

@media (pointer: coarse) {
  .touch-friendly {
    padding: 12px 24px;
  }
}
</style>
```

## 无障碍测试工具

### 1. 自动化测试

**安装 axe-core:**

```bash
npm install --save-dev @axe-core/vue
```

**配置:**

```typescript
// plugins/axe.client.ts
import VueAxe from '@axe-core/vue'

export default defineNuxtPlugin((nuxtApp) => {
  if (process.env.NODE_ENV === 'development') {
    nuxtApp.vueApp.use(VueAxe, {
      clearConsoleOnUpdate: false,
    })
  }
})
```

### 2. 手动测试清单

**键盘导航:**
- [ ] 可以使用 Tab 键导航所有交互元素
- [ ] 焦点指示器清晰可见
- [ ] 可以使用 Enter/Space 激活按钮
- [ ] 可以使用 Escape 关闭模态框/下拉菜单

**屏幕阅读器:**
- [ ] 所有内容可被屏幕阅读器识别
- [ ] 图片有适当的 alt 文本
- [ ] 表单有关联的 label
- [ ] 错误消息可被朗读

**颜色对比度:**
- [ ] 文本对比度至少 4.5:1
- [ ] 大文本(18pt+)对比度至少 3:1
- [ ] 图标和边框有足够对比度

### 3. 浏览器扩展

- axe DevTools
- WAVE (Web Accessibility Evaluation Tool)
- Lighthouse (内置无障碍审计)
- ARC Toolkit

### 4. 屏幕阅读器测试

**Windows:**
- NVDA (免费)
- JAWS (商业)
- Narrator (Windows 内置)

**macOS:**
- VoiceOver (内置)

**移动设备:**
- TalkBack (Android)
- VoiceOver (iOS)

## 工作流程

### Step 1: 评估现状

运行无障碍审计,识别问题:
```bash
# 使用 Lighthouse
npx lighthouse https://example.com --view

# 或使用 axe-core
npm run test:a11y
```

### Step 2: 制定计划

根据审计结果确定优先级:
1. 修复阻塞性问题(如缺失 alt 文本)
2. 改进键盘导航
3. 提升颜色对比度
4. 优化 ARIA 属性

### Step 3: 实施改进

逐项修复无障碍问题,参考本技能的最佳实践。

### Step 4: 测试验证

- 自动化测试
- 手动键盘测试
- 屏幕阅读器测试
- 用户测试(如有残障用户)

### Step 5: 持续维护

建立无障碍开发规范:
- Code Review 时检查无障碍
- 定期运行审计
- 新功能开发时考虑无障碍

## 输出格式

**代码示例:**

```vue
<template>
  <!-- 无障碍友好的组件示例 -->
</template>

<script setup lang="ts">
// 组件逻辑
</script>

<style scoped>
/* 样式 */
</style>
```

**检查清单:**

- [ ] 语义化 HTML
- [ ] 正确的标题层级
- [ ] 图片 alt 文本
- [ ] 表单 label 关联
- [ ] 键盘可访问
- [ ] 焦点管理
- [ ] ARIA 属性正确
- [ ] 颜色对比度合格
- [ ] 响应式设计
- [ ] 跳过链接

## 参考资源

在需要更详细指导时,加载以下参考文件:

- `references/semantic-html.md` - 语义化 HTML 最佳实践
- `references/aria-guide.md` - ARIA 属性完整指南
- `references/keyboard-navigation.md` - 键盘导航实现
- `references/color-contrast.md` - 颜色对比度标准
- `references/testing-guide.md` - 无障碍测试完整指南
- `references/common-wcag.md` - WCAG 2.1 常见要求

## 注意事项

1. **渐进增强** - 先确保基础功能可用,再添加高级特性
2. **原生优先** - 优先使用原生 HTML 元素,其次考虑 ARIA
3. **真实测试** - 不仅要通过自动化测试,还要用真实设备测试
4. **持续改进** - 无障碍不是一次性的,需要持续维护
5. **用户反馈** - 尽可能获取残障用户的反馈
6. **文档规范** - 为团队建立无障碍开发规范

## 常见错误

### ❌ 错误 1: 过度使用 ARIA

```vue
<!-- ❌ 不必要 -->
<div role="button" @click="handleClick">点击</div>

<!-- ✅ 正确 -->
<button @click="handleClick">点击</button>
```

### ❌ 错误 2: 缺少 label

```vue
<!-- ❌ 缺少 label -->
<input v-model="email" type="email" />

<!-- ✅ 正确 -->
<label for="email">邮箱</label>
<input id="email" v-model="email" type="email" />
```

### ❌ 错误 3: 焦点陷阱

```vue
<!-- ❌ 模态框无法关闭或焦点被困 -->
<div v-if="showModal">
  <p>内容...</p>
</div>

<!-- ✅ 正确实现焦点管理 -->
<div
  v-if="showModal"
  role="dialog"
  aria-modal="true"
>
  <p>内容...</p>
  <button @click="closeModal">关闭</button>
</div>
```
