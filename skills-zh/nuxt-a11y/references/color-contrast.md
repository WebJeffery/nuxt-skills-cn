# 颜色对比度标准

## WCAG 2.1 对比度要求

| 内容类型 | AA 级 | AAA 级 |
|---------|-------|--------|
| 正常文本(< 18pt) | 4.5:1 | 7:1 |
| 大文本(≥ 18pt 或 ≥ 14pt 粗体) | 3:1 | 4.5:1 |
| UI 组件和图形对象 | 3:1 | - |
| 文本图像 | 4.5:1 | 7:1 |

## 检查对比度

### 在线工具

- **WebAIM Contrast Checker** - https://webaim.org/resources/contrastchecker/
- **Colour Contrast Analyser (CCA)** - https://www.tpgi.com/color-contrast-checker/
- **Contrast Ratio** - https://contrast-ratio.com/

### Chrome 扩展

- **Axe DevTools**
- **WAVE**
- **Lighthouse**

## 实际示例

### 文本对比度

```css
/* ✅ 正确: 对比度 14.2:1 (AAA) */
.text-high-contrast {
  color: #333333;
  background-color: #FFFFFF;
}

/* ✅ 正确: 对比度 4.6:1 (AA) */
.text-medium-contrast {
  color: #666666;
  background-color: #FFFFFF;
}

/* ❌ 错误: 对比度 1.6:1 (不合格) */
.text-low-contrast {
  color: #CCCCCC;
  background-color: #FFFFFF;
}

/* ✅ 大文本可用较低对比度 (3.1:1, AA) */
.text-large {
  color: #767676;
  background-color: #FFFFFF;
  font-size: 18pt;
}
```

### 链接对比度

```css
/* ✅ 链接应有足够对比度 */
a {
  color: #0052cc; /* 对比度 4.5:1 */
}

a:hover {
  color: #003d99; /* 对比度 5.4:1 */
}

/* 确保焦点状态也可见 */
a:focus {
  outline: 2px solid #0052cc;
  outline-offset: 2px;
}
```

### 表单元素

```css
/* ✅ 输入框边框和背景的对比度 */
input[type="text"],
textarea {
  border: 1px solid #767676; /* 对比度 3.1:1 */
  background-color: #FFFFFF;
  color: #333333; /* 对比度 14.2:1 */
}

input[type="text"]:focus {
  border-color: #0052cc;
  outline: none;
}

/* 错误状态应有高对比度 */
input.error {
  border-color: #cc0000; /* 对比度 5.1:1 */
}
```

### 按钮对比度

```css
/* ✅ 主按钮 */
.btn-primary {
  background-color: #0052cc;
  color: #FFFFFF;
  /* 对比度 6.4:1 (AA) */
}

/* ✅ 次要按钮 */
.btn-secondary {
  background-color: #EBECF0;
  color: #333333;
  /* 对比度 12.8:1 (AAA) */
}

/* ❌ 低对比度按钮(避免) */
.btn-low-contrast {
  background-color: #F4F5F7;
  color: #B3BAC5;
  /* 对比度 1.3:1 (不合格!) */
}
```

### 图标对比度

```css
/* ✅ 图标应与背景有足够对比度 */
.icon {
  color: #333333; /* 对比度 14.2:1 */
}

/* 装饰性图标可以降低对比度但不要过度 */
.icon-subtle {
  color: #999999; /* 对比度 2.8:1 (稍低,但可接受) */
}

/* ❌ 太淡的图标 */
.icon-faint {
  color: #E0E0E0; /* 对比度 1.1:1 (不合格) */
}
```

## 深色模式

```css
/* 浅色模式 */
:root {
  --bg-primary: #FFFFFF;
  --text-primary: #333333; /* 14.2:1 */
  --text-secondary: #666666; /* 4.6:1 */
}

/* 深色模式 */
[data-theme="dark"] {
  --bg-primary: #1A1A1A;
  --text-primary: #F0F0F0; /* 13.2:1 */
  --text-secondary: #B0B0B0; /* 4.7:1 */
}

body {
  background-color: var(--bg-primary);
  color: var(--text-primary);
}
```

## 状态颜色

### 成功/错误/警告

```css
/* ✅ 成功绿色 */
.text-success {
  color: #0E8420; /* 对比度 4.6:1 */
  background-color: #E3FCEF;
}

/* ✅ 错误红色 */
.text-error {
  color: #BF2600; /* 对比度 4.6:1 */
  background-color: #FFEBE6;
}

/* ✅ 警告黄色 */
.text-warning {
  color: #FF991F; /* 对比度 3.1:1 */
  background-color: #FFFAE6;
}

/* ❌ 避免过于鲜艳的颜色 */
.text-bright-red {
  color: #FF0000; /* 可能过亮,难以阅读 */
}
```

## 禁用状态

```css
/* ✅ 禁用按钮应有较低但可辨认的对比度 */
button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

/* 或者使用灰色 */
button:disabled {
  background-color: #F4F5F7;
  color: #B3BAC5; /* 对比度 2.1:1 */
}
```

## 边框和分隔线

```css
/* ✅ 边框至少 3:1 对比度 */
.border-light {
  border: 1px solid #DFE1E6; /* 对比度 2.2:1 (稍低) */
}

.border-medium {
  border: 1px solid #C1C7D0; /* 对比度 3.0:1 (AA) */
}

.border-strong {
  border: 1px solid #6B778C; /* 对比度 5.2:1 (AA+) */
}

/* 分隔线 */
hr {
  border-color: #DFE1E6; /* 3:1 */
}
```

## Vue/Nuxt 实现

### 动态对比度检查

```typescript
// utils/color.ts
export function getContrastRatio(foreground: string, background: string): number {
  const fg = hexToRgb(foreground)
  const bg = hexToRgb(background)

  const fgLuminance = getLuminance(fg.r, fg.g, fg.b)
  const bgLuminance = getLuminance(bg.r, bg.g, bg.b)

  const lighter = Math.max(fgLuminance, bgLuminance)
  const darker = Math.min(fgLuminance, bgLuminance)

  return (lighter + 0.05) / (darker + 0.05)
}

function getLuminance(r: number, g: number, b: number): number {
  const [rs, gs, bs] = [r, g, b].map(v => {
    v = v / 255
    return v <= 0.03928 ? v / 12.92 : Math.pow((v + 0.055) / 1.055, 2.4)
  })
  return 0.2126 * rs + 0.7152 * gs + 0.0722 * bs
}

function hexToRgb(hex: string): { r: number; g: number; b: number } {
  const result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex)
  return result ? {
    r: parseInt(result[1], 16),
    g: parseInt(result[2], 16),
    b: parseInt(result[3], 16)
  } : { r: 0, g: 0, b: 0 }
}

export function meetsWCAG(foreground: string, background: string, level: 'AA' | 'AAA' = 'AA', large = false): boolean {
  const ratio = getContrastRatio(foreground, background)

  if (large) {
    return level === 'AAA' ? ratio >= 4.5 : ratio >= 3
  }

  return level === 'AAA' ? ratio >= 7 : ratio >= 4.5
}
```

### 使用示例

```vue
<script setup lang="ts>
import { computed } from 'vue'
import { getContrastRatio, meetsWCAG } from '~/utils/color'

const props = defineProps<{
  backgroundColor: string
}>()

const textColor = computed(() => {
  const darkRatio = getContrastRatio('#000000', props.backgroundColor)
  const lightRatio = getContrastRatio('#FFFFFF', props.backgroundColor)

  return lightRatio > darkRatio ? '#FFFFFF' : '#000000'
})

const isAccessible = computed(() => {
  return meetsWCAG(textColor.value, props.backgroundColor, 'AA')
})
</script>

<template>
  <div
    :style="{ backgroundColor, color: textColor }"
    :aria-label="isAccessible ? '可访问的颜色组合' : '颜色对比度不足'"
  >
    <slot />
  </div>
</template>
```

## 自动测试

```typescript
// tests/a11y/contrast.spec.ts
import { test, expect } from 'vitest'
import { getContrastRatio } from '~/utils/color'

test('主文本对比度符合 AA 标准', () => {
  const ratio = getContrastRatio('#333333', '#FFFFFF')
  expect(ratio).toBeGreaterThanOrEqual(4.5)
})

test('按钮对比度符合 AA 标准', () => {
  const ratio = getContrastRatio('#FFFFFF', '#0052cc')
  expect(ratio).toBeGreaterThanOrEqual(4.5)
})
```

## 最佳实践

1. **始终测试** - 不要凭肉眼判断
2. **考虑所有状态** - hover, focus, active, disabled
3. **支持深色模式** - 提供高对比度主题
4. **不要只依赖颜色** - 结合图标、文本传达信息
5. **尊重用户设置** - 支持系统的"高对比度"模式

## 常见错误

### ❌ 错误 1: 灰色文本

```css
/* 对比度不足 */
.text {
  color: #CCCCCC; /* 1.6:1 */
}

/* ✅ 使用更深的灰色 */
.text {
  color: #666666; /* 4.6:1 */
}
```

### ❌ 错误 2: 浅色按钮

```css
/* 对比度不足 */
button {
  background-color: #E0E0E0;
  color: #FFFFFF; /* 不可见 */
}

/* ✅ 使用合适的颜色 */
button {
  background-color: #0052cc;
  color: #FFFFFF; /* 6.4:1 */
}
```

### ❌ 错误 3: 链接仅靠颜色区分

```vue
<!-- ❌ 不够明显 -->
<a href="/" style="color: #0052cc">链接</a>

<!-- ✅ 添加下划线 -->
<a href="/" style="color: #0052cc; text-decoration: underline;">链接</a>
```
