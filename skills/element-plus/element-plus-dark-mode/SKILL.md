---
name: "element-plus-dark-mode"
description: "启用和自定义 Element Plus 应用的暗黑模式。当用户需要实现暗黑模式、切换主题或自定义暗黑模式样式时调用---

# Element Plus 暗黑模式

此技能为 Element Plus 应用中实现和自定义暗黑模式提供全面指导（2.2.0 版本起可用）
## 何时调用

在以下情况下调用此技能：
- 用户想要启用暗黑模式
- 用户需要在亮色和暗黑主题之间切- 用户想要自定义暗黑模式颜色- 用户询问暗黑模式 CSS 变量
- 用户需要持久化暗黑模式偏好

## 快速开
### 启用暗黑模式

`dark` 类添加到 HTML 元素
```html
<html class="dark">
  <head></head>
  <body></body>
</html>
```

### 导入暗黑模式 CSS

导入暗黑模式 CSS 变量
```ts
import 'element-plus/theme-chalk/dark/css-vars.css'
```

就是这样！您Element Plus 组件现在将或使用暗黑模式样式
## 实现方法

### 1. 静态暗黑模
如果您只需要暗黑模式，只需要`dark` 类添加到 HTML
```html
<html class="dark">
```

并导CSS
```ts
import 'element-plus/theme-chalk/dark/css-vars.css'
```

### 2. 切换暗黑模式（推荐）

使用 [VueUse useDark](https://vueuse.org/core/useDark/) 进行轻松切换
```vue
<script setup>
import { useDark, useToggle } from '@vueuse/core'

const isDark = useDark()
const toggleDark = useToggle(isDark)
</script>

<template>
  <el-button @click="toggleDark()">
    切换暗黑模式
  </el-button>
</template>
```

### 3. 手动切换

实现您自己切换
```vue
<script setup>
import { ref, watch } from 'vue'

const isDark = ref(false)

watch(isDark, (value) => {
  if (value) {
    document.documentElement.classList.add('dark')
  } else {
    document.documentElement.classList.remove('dark')
  }
})
</script>

<template>
  <el-switch v-model="isDark" active-text="暗黑" inactive-text="亮色" />
</template>
```

### 4. 系统偏好

遵循系统偏好
```vue
<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const isDark = ref(false)

const updateTheme = (e) => {
  isDark.value = e.matches
  document.documentElement.classList.toggle('dark', e.matches)
}

onMounted(() => {
  const mediaQuery = window.matchMedia('(prefers-color-scheme: dark)')
  isDark.value = mediaQuery.matches
  updateTheme(mediaQuery)
  mediaQuery.addEventListener('change', updateTheme)
})

onUnmounted(() => {
  window.matchMedia('(prefers-color-scheme: dark)')
    .removeEventListener('change', updateTheme)
})
</script>
```

## 自定义暗黑模
### 1. CSS 变量覆盖

创建自定义暗黑模CSS 文件
```css
/* styles/dark/css-vars.css */
html.dark {
  /* 自定义暗黑背景颜色*/
  --el-bg-color: #141414;
  --el-bg-color-page: #0a0a0a;
  --el-bg-color-overlay: #1d1e1f;

  /* 自定义文本颜色*/
  --el-text-color-primary: #E5EAF3;
  --el-text-color-regular: #CFD3DC;
  --el-text-color-secondary: #A3A6AD;
  --el-text-color-placeholder: #8D9095;

  /* 自定义边框颜色*/
  --el-border-color: #4C4D4F;
  --el-border-color-light: #414243;
  --el-border-color-lighter: #363637;
  --el-border-color-extra-light: #2B2B2C;
}
```

Element Plus 暗黑模式 CSS 之后导入它：

```ts
import 'element-plus/theme-chalk/dark/css-vars.css'
import './styles/dark/css-vars.css'
```

### 2. SCSS 变量覆盖

对于基于 SCSS 自定义
```scss
/* styles/element/index.scss */
@forward 'element-plus/theme-chalk/src/dark/var.scss' with (
  $bg-color: (
    'page': #0a0a0a,
    '': #141414,
    'overlay': #1d1e1f,
  )
);
```

然后导入
```ts
import './styles/element/index.scss'
```

直接导SCSS
```ts
import 'element-plus/theme-chalk/src/dark/css-vars.scss'
```

## 可用暗黑模式变
### 背景颜色

| 变量 | 描述 | 默认|
|----------|-------------|---------|
| `--el-bg-color` | 主背| `#141414` |
| `--el-bg-color-page` | 页面背景 | `#0a0a0a` |
| `--el-bg-color-overlay` | 遮罩背景 | `#1d1e1f` |

### 文本颜色

| 变量 | 描述 | 默认|
|----------|-------------|---------|
| `--el-text-color-primary` | 主要文本 | `#E5EAF3` |
| `--el-text-color-regular` | 常规文本 | `#CFD3DC` |
| `--el-text-color-secondary` | 次要文本 | `#A3A6AD` |
| `--el-text-color-placeholder` | 占位符文| `#8D9095` |

### 边框颜色

| 变量 | 描述 | 默认|
|----------|-------------|---------|
| `--el-border-color` | 主边| `#4C4D4F` |
| `--el-border-color-light` | 浅边| `#414243` |
| `--el-border-color-lighter` | 更浅边框 | `#363637` |

### 填充颜色

| 变量 | 描述 | 默认|
|----------|-------------|---------|
| `--el-fill-color` | 主填| `#303030` |
| `--el-fill-color-light` | 浅填| `#262727` |
| `--el-fill-color-lighter` | 更浅填充 | `#1D1D1D` |

## 完整示例

```vue
<template>
  <div class="app">
    <el-header>
      <el-button @click="toggleDark()">
        <el-icon>
          <component :is="isDark ? 'Sunny' : 'Moon'" />
        </el-icon>
        {{ isDark ? '亮色模式' : '暗黑模式' }}
      </el-button>
    </el-header>

    <el-main>
      <el-card>
        <template #header>
          <span>卡片标题</span>
        </template>
        <p>{{ isDark ? '暗黑' : '亮色' }}模式下卡片内容</p>
        <el-button type="primary">主要按钮</el-button>
      </el-card>
    </el-main>
  </div>
</template>

<script setup>
import { useDark, useToggle } from '@vueuse/core'
import { Sunny, Moon } from '@element-plus/icons-vue'

const isDark = useDark()
const toggleDark = useToggle(isDark)
</script>

<style>
.app {
  min-height: 100vh;
  background-color: var(--el-bg-color-page);
  color: var(--el-text-color-primary);
  transition: background-color 0.3s, color 0.3s;
}
</style>
```

## 最佳实践
### 1. 持久化偏
将用户偏好存储在 localStorage 中：

```ts
import { useDark } from '@vueuse/core'

const isDark = useDark({
  storageKey: 'theme-mode',
  valueDark: 'dark',
  valueLight: 'light',
})
```

### 2. 平滑过渡

添加过渡以实现平滑主题切换
```css
html {
  transition: background-color 0.3s, color 0.3s;
}

html.dark {
  background-color: #0a0a0a;
  color: #E5EAF3;
}
```

### 3. 自定义组件样式
为暗黑模式自定义组件时：

```css
html.dark .my-custom-component {
  background-color: var(--el-bg-color);
  border-color: var(--el-border-color);
  color: var(--el-text-color-primary);
}
```

### 4. 图像调整

为暗黑模式调整图像：

```css
html.dark img {
  filter: brightness(0.9);
}
```

## 输入参数

使用此技能时，提供：
- **实现方法**：静态、切换系统偏好
- **自定义需要*：默认颜色自定义颜色- **框架**：VueUse 集成手动实现- **持久*：是否保存用户偏
## 输出格式

此技能提供：
1. 暗黑模式启用代码
2. 切换实现示例
3. CSS 变量自定义义4. SCSS 变量覆盖示例
5. 最佳实践和过渡效果

## 使用限制

- 需要 Element Plus 2.2.0 或更高版本- 暗黑模式基于 CSS 变量
- 自定义颜色可能需要额外测试- 某些第三方组件可能需要手动暗黑模式样式- 需要 CSS 变量支持（不支持 IE11）
## 示例项目

参考[element-plus-vite-starter](https://github.com/element-plus/element-plus-vite-starter) 获取完整的暗黑模式实现示例