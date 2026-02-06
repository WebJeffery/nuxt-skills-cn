# @nuxt/a11y 开发环境配置

## 安装

```bash
npm install --save-dev @nuxt/a11y
```

## 基础配置

### nuxt.config.ts

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  modules: [
    // 仅在开发环境启用
    process.env.NODE_ENV === 'development' ? '@nuxt/a11y' : null,
  ].filter(Boolean),
})
```

## 完整配置选项

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@nuxt/a11y'],

  a11y: {
    // 启用/禁用特定检查
    checks: {
      'img-alt': true,           // 图片必须有 alt 属性
      'label': true,             // 表单元素必须有 label
      'button-name': true,       // 按钮必须有可访问名称
      'link-name': true,         // 链接必须有可访问名称
      'color-contrast': true,    // 文本必须有足够对比度
      'list': true,              // 列表必须正确嵌套
      'listitem': true,          // 列表项必须在列表内
      'doctype': true,           // 必须有 doctype
      'html-lang': true,         // html 元素必须有 lang 属性
    },

    // 自定义 axe-core 规则
    rules: {
      'color-contrast': {
        enabled: true,
        // 配置对比度级别
      },
      'heading-order': {
        enabled: false, // 禁用某个规则
      },
    },

    // 在浏览器中显示可视化问题
    visualInspector: true,

    // 在控制台显示详细日志
    console: true,

    // 自定义选项
    axeOptions: {
      // axe-core 配置
      runOnly: {
        type: 'tag',
        values: ['wcag2a', 'wcag2aa'], // 只检查 WCAG 2.0 A 和 AA 级别
      },
    },
  },
})
```

## 环境特定配置

### 开发环境

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  $development: {
    modules: ['@nuxt/a11y'],
    a11y: {
      visualInspector: true,
      console: true,
    },
  },
})
```

### 生产环境(禁用)

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  modules: [],
  // 生产环境不包含 a11y 模块
})
```

## 使用方式

### 1. 启动开发服务器

```bash
npm run dev
```

### 2. 访问页面

打开浏览器访问 `http://localhost:3000`,无障碍问题会自��显示在:

1. **浏览器控制台**
   ```
   ⚠️ [A11y] Elements must have sufficient color contrast
   ⚠️ [A11y] Images must have alt attribute
   ```

2. **页面叠加层**
   - 问题元素会被红色边框高亮
   - 悬停显示详细错误信息

3. **Vue DevTools**
   - 打开 Vue DevTools
   - 切换到 "A11y" 标签
   - 查看所有无障碍问题

## 常见配置示例

### 仅检查 WCAG 2.1 AA 级别

```typescript
a11y: {
  axeOptions: {
    runOnly: {
      type: 'tag',
      values: ['wcag21aa'],
    },
  },
}
```

### 忽略特定规则

```typescript
a11y: {
  rules: {
    'color-contrast': {
      enabled: false, // 忽略颜色对比度检查
    },
    'region': {
      enabled: false, // 忽略地标检查
    },
  },
}
```

### 忽略特定元素

```typescript
a11y: {
  axeOptions: {
    // 跳过某些元素的检查
    selectors: {
      // 跳过第三方组件
      '.third-party-widget': true,
      // 跳过开发专用元素
      '[data-dev-only]': true,
    },
  },
}
```

## 自定义检查

### 添加自定义规则

```typescript
// plugins/custom-a11y-rules.ts
import { axeRuntime } from '@nuxt/a11y'

export default defineNuxtPlugin(() => {
  if (process.env.NODE_ENV === 'development') {
    // 自定义规则
    const customRule = {
      id: 'custom-button-text',
      matches: (node: HTMLElement) => {
        return node.tagName === 'BUTTON'
      },
      evaluate: (node: HTMLElement) => {
        const text = node.textContent?.trim()
        return !!text && text.length > 0
      },
    }

    // 注册规则
    axeRuntime.register(customRule)
  }
})
```

### 监听无障碍事件

```typescript
// plugins/a11y-monitor.ts
export default defineNuxtPlugin((nuxtApp) => {
  if (process.env.NODE_ENV === 'development') {
    nuxtApp.hook('app:mounted', () => {
      // 监听无障碍问题
      window.addEventListener('a11y-violation', (event: any) => {
        console.error('无障碍问题:', event.detail)

        // 发送到错误跟踪服务
        // trackError(event.detail)
      })
    })
  }
})
```

## 与 CI/CD 集成

### 在 CI 中运行无障碍测试

```bash
# .github/workflows/a11y.yml
name: 无障碍测试

on: [push, pull_request]

jobs:
  a11y:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: 安装依赖
        run: npm ci

      - name: 构建项目
        run: npm run build

      - name: 启动开发服务器
        run: npm run dev &

      - name: 等待服务器启动
        run: sleep 10

      - name: 运行无障碍测试
        run: npx pa11y http://localhost:3000

      - name: 关闭服务器
        run: pkill -f "npm run dev"
```

### 在测试中使用

```typescript
// tests/a11y/with-nuxt-a11y.spec.ts
import { describe, it, expect } from 'vitest'
import { setup, $fetch } from '@nuxt/test-utils'

await setup({
  dev: process.env.NODE_ENV !== 'production',
})

describe('无障碍测试', () => {
  it('主页无障碍得分 > 90', async () => {
    const html = await $fetch('/')
    const { violations } = await axeCore(html)

    // 计算得分
    const score = Math.max(0, 100 - violations.length * 10)
    expect(score).toBeGreaterThan(90)
  })
})
```

## 最佳实践

### 1. 仅开发环境使用

```typescript
// ✅ 正确
modules: [
  process.env.NODE_ENV === 'development' ? '@nuxt/a11y' : null,
].filter(Boolean),

// ❌ 错误 - 生产环境也会加载
modules: ['@nuxt/a11y'],
```

### 2. 早期发现问题

```typescript
// 在组件开发时就启用
// 每次保存都会重新检查
export default defineNuxtConfig({
  a11y: {
    visualInspector: true, // 显示可视化问题
    console: true,         // 控制台输出
  },
})
```

### 3. 定期审查

```typescript
// 定期查看无障碍报告
const a11yReport = await $fetch('/__a11y_report')
console.log(a11yReport.violations)
```

## 故障排除

### 问题 1: 模块未加载

**症状:** 无障碍问题未显示在控制台

**解决方案:**
```typescript
// 确保在开发环境
console.log('NODE_ENV:', process.env.NODE_ENV)

// 检查模块是否正确安装
npm list @nuxt/a11y
```

### 问题 2: 性能影响

**症状:** 开发服务器变慢

**解决方案:**
```typescript
a11y: {
  // 减少检查规则
  rules: {
    'color-contrast': { enabled: false },
  },

  // 禁用可视化检查器
  visualInspector: false,
}
```

### 问题 3: 假阳性

**症状:** 报告不存在的问题

**解决方案:**
```typescript
a11y: {
  axeOptions: {
    // 忽略特定元素
    locale: {
      lang: 'zh-CN',
    },
  },
}
```

## 与其他工具对比

| 工具 | 用途 | 优点 | 缺点 |
|------|------|------|------|
| @nuxt/a11y | 开发时实时检查 | 实时反馈,零配置 | 仅开发环境 |
| @axe-core/vue | 程序化测试 | 灵活,可集成到测试 | 需要额外配置 |
| Lighthouse | 完整审计 | 全面,权威 | 手动运行 |
| pa11y | CI/CD 集成 | 适合自动化 | 需要运行服务器 |

## 推荐配置组合

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  // 开发环境: @nuxt/a11y
  modules: process.env.NODE_ENV === 'development'
    ? ['@nuxt/a11y']
    : [],

  // 测试环境: @axe-core/vue
  // 在 vitest.config.ts 中配置

  // 生产环境: 无无障碍工具
  // 使用外部审计(如 Lighthouse)
})
```

## 参考资源

- [@nuxt/a11y 文档](https://a11y.nuxtjs.org/)
- [axe-core 文档](https://www.deque.com/axe/)
- [WCAG 2.1 快速参考](https://www.w3.org/WAI/WCAG21/quickref/)
