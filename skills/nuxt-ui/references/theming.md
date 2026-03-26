# 主题

## 语义化颜色

| 颜色       | 默认值 | 用途                                     |
| ----------- | ------ | ---------------------------------------- |
| `primary`   | 绿色   | CTA 按钮、激活状态、品牌、重要链接       |
| `secondary` | 蓝色   | 次要按钮、替代选项                       |
| `success`   | 绿色   | 成功消息、积极状态                       |
| `info`      | 蓝色   | 信息提示、帮助文本                       |
| `warning`   | 黄色   | 警告、待处理状态                         |
| `error`     | 红色   | 错误、破坏性操作                         |
| `neutral`   | 石板灰 | 文本、边框、禁用状态                     |

## 配置颜色

### Nuxt (app.config.ts)

```ts
// app.config.ts
export default defineAppConfig({
  ui: {
    colors: {
      primary: 'indigo',
      secondary: 'violet',
      success: 'emerald',
      error: 'rose'
    }
  }
})
```

### Vue (vite.config.ts)

```ts
ui({
  ui: {
    colors: {
      primary: 'indigo',
      secondary: 'violet'
    }
  }
})
```

## 添加自定义颜色

1. 在主题配置中注册：

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  ui: {
    theme: {
      colors: ['primary', 'secondary', 'tertiary'] // 添加新颜色
    }
  }
})
```

2. 在 CSS 中定义（需要所有 11 个色阶）：

```css
@theme {
  --color-tertiary-50: #fef2f2;
  --color-tertiary-100: #fee2e2;
  --color-tertiary-200: #fecaca;
  --color-tertiary-300: #fca5a5;
  --color-tertiary-400: #f87171;
  --color-tertiary-500: #ef4444;
  --color-tertiary-600: #dc2626;
  --color-tertiary-700: #b91c1c;
  --color-tertiary-800: #991b1b;
  --color-tertiary-900: #7f1d1d;
  --color-tertiary-950: #450a0a;
}
```

3. 分配并使用：

```ts
// app.config.ts
export default defineAppConfig({
  ui: { colors: { tertiary: 'tertiary' } }
})
```

```vue
<UButton color="tertiary">自定义颜色</UButton>
```

## CSS 变量

### 文本工具类

| 类名              | 亮色模式 | 暗色模式 | 用途                   |
| ----------------- | -------- | -------- | ---------------------- |
| `text-dimmed`      | 400      | 500      | 占位符、提示文本       |
| `text-muted`       | 500      | 400      | 次要文本               |
| `text-toned`       | 600      | 300      | 副标题                 |
| `text-default`     | 700      | 200      | 正文文本               |
| `text-highlighted` | 900      | 100      | 标题、强调内容         |
| `text-inverted`    | 50       | 950      | 在深色/浅色背景上使用  |

### 背景工具类

| 类名         | 亮色模式 | 暗色模式 | 用途               |
| ------------ | -------- | -------- | ------------------ |
| `bg-default`  | 白色     | 900      | 页面背景           |
| `bg-muted`    | 50       | 800      | 微妙区域           |
| `bg-elevated` | 白色     | 800      | 卡片、模态框       |
| `bg-accented` | 100      | 700      | 悬停状态           |
| `bg-inverted` | 900      | 100      | 反转区域           |

### 边框工具类

| 类名             | 亮色模式 | 暗色模式 |
| ---------------- | -------- | -------- |
| `border-default`  | 200      | 800      |
| `border-muted`    | 100      | 800      |
| `border-accented` | 200      | 700      |
| `border-inverted` | 900      | 100      |

### 全局变量

```css
:root {
  --ui-radius: 0.25rem; /* 基础边框圆角 */
  --ui-container: 80rem; /* 容器最大宽度 */
  --ui-header-height: 4rem; /* 页头高度 */
}
```

## 自定义 CSS 变量

```css
/* assets/css/main.css */
:root {
  --ui-primary: var(--ui-color-primary-700);
  --ui-radius: 0.5rem;
}
.dark {
  --ui-primary: var(--ui-color-primary-200);
}
```

## 纯色（黑色/白色）

不能使用 `primary: 'black'` - 需要在 CSS 中设置：

```css
:root {
  --ui-primary: black;
}
.dark {
  --ui-primary: white;
}
```

## Tailwind 变体覆盖

### 全局覆盖 (app.config.ts)

```ts
export default defineAppConfig({
  ui: {
    button: {
      slots: {
        base: 'font-bold rounded-full'
      },
      variants: {
        size: {
          md: { base: 'px-6 py-3' }
        }
      },
      compoundVariants: [{
        color: 'neutral',
        variant: 'outline',
        class: { base: 'ring-2' }
      }],
      defaultVariants: {
        color: 'neutral',
        variant: 'outline'
      }
    }
  }
})
```

### 单组件覆盖

```vue
<!-- ui prop 覆盖插槽 -->
<UButton :ui="{ base: 'font-mono' }">自定义</UButton>

<!-- class prop 覆盖根/base 插槽 -->
<UButton class="rounded-none">方形</UButton>
```

## 在 app.config 中匹配主题结构

**重要**：组件有两种主题结构模式。您的 app.config 必须匹配组件的主题结构。

### 模式 1：基于插槽的主题（大多数组件）

Button、Card、Input、Select 等组件在主题中使用 `slots:`：

```ts
// 组件主题（Button、Card 等）
export default {
  slots: {
    base: '...',
    root: '...',
    icon: '...'
  }
}
```

**app.config 用法**：

```ts
ui: {
  button: {
    slots: { base: 'font-bold' }  // ✅ 匹配 slots 结构
  }
}
```

### 模式 2：扁平基础主题（Container、Skeleton 等）

Container、Skeleton、Form、Main 等组件在主题中使用扁平的 `base:`：

```ts
// 组件主题（Container、Skeleton 等）
export default {
  base: 'w-full max-w-container'
}
```

**app.config 用法**：

```ts
ui: {
  container: {
    base: 'max-w-lg'  // ✅ 匹配扁平结构
  }
}
```

### 常见错误

```ts
// ❌ 错误 - 不要为扁平基础组件使用 slots
ui: {
  container: {
    slots: { base: 'max-w-lg' }  // TypeScript 错误！
  }
}

// ❌ 错误 - 不要为基于插槽的组件使用扁平结构
ui: {
  button: {
    base: 'font-bold'  // 不会正常工作
  }
}
```

### 如何检查组件结构

1. 查看组件文档：https://ui.nuxt.com/components/[component]
2. 查看 "Theme" 部分 - 显示结构
3. 在您的 app.config 中匹配该结构

## 组件主题结构

```ts
// 每个组件都有 slots、variants、compoundVariants、defaultVariants
export default {
  slots: {
    root: 'relative',
    base: 'px-4 py-2',
    icon: 'size-5'
  },
  variants: {
    color: {
      primary: { base: 'bg-primary text-inverted' },
      neutral: { base: 'bg-neutral text-default' }
    },
    size: {
      sm: { base: 'text-sm', icon: 'size-4' },
      md: { base: 'text-base', icon: 'size-5' },
      lg: { base: 'text-lg', icon: 'size-6' }
    }
  },
  defaultVariants: {
    color: 'primary',
    size: 'md'
  }
}
```

## 暗色模式

由 `@nuxtjs/color-mode` 处理。通过以下方式访问：

```ts
const colorMode = useColorMode()
colorMode.preference = 'dark' // 'light', 'dark', 'system'
```

```vue
<UColorModeButton /> <!-- 切换按钮 -->
<UColorModeSelect /> <!-- 下拉选择 -->
```

## 最佳实践

| 应该做                                 | 不应该做                     |
| -------------------------------------- | ---------------------------- |
| 使用语义化颜色                         | 硬编码十六进制值             |
| 通过 app.config 覆盖                  | 修改源主题文件               |
| 使用 CSS 变量定义自定义颜色           | 跳过暗色模式变体             |
| 为自定义颜色定义所有 11 个色阶        | 使用部分色阶定义             |
