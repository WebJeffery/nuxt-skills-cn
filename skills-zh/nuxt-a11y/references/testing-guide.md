# 无障碍测试完整指南

## 测试工具

### 自动化测试工具

#### 1. @nuxtjs/a11y (推荐用于开发环境)

```bash
npm install --save-dev @nuxtjs/a11y
```

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  modules: [
    // 仅在开发环境启用
    process.env.NODE_ENV === 'development' ? '@nuxtjs/a11y' : null,
  ].filter(Boolean),

  // 配置选项
  a11y: {
    // 启用/禁用特定检查
    checks: {
      'img-alt': true,           // 检查图片 alt 属性
      'label': true,             // 检查表单 label
      'button-name': true,       // 检查按钮名称
      'link-name': true,         // 检查链接名称
      'color-contrast': true,    // 检查颜色对比度
    },

    // 自定义规则
    rules: {
      'color-contrast': {
        enabled: true,
      },
    },

    // 在浏览器中显示可视化问题
    visualInspector: true,

    // 在控制台显示详细日志
    console: true,
  },
})
```

**功能特性:**
- ✅ 实时无障碍问题检测
- ✅ 页面叠加显示问题位置
- ✅ 控制台详细错误信息
- ✅ 支持 Vue DevTools 集成
- ✅ 零配置,开箱即用

**使用示例:**
```bash
# 启动开发服务器
npm run dev

# 访问页面后,无障碍问题会自动显示在:
# 1. 浏览器控制台
# 2. 页面叠加层(高亮问题元素)
# 3. Vue DevTools A11y 面板
```

#### 2. axe-core

```bash
npm install --save-dev @axe-core/vue
```

```typescript
// plugins/axe.client.ts
import VueAxe from '@axe-core/vue'

export default defineNuxtPlugin((nuxtApp) => {
  if (process.env.NODE_ENV === 'development') {
    nuxtApp.vueApp.use(VueAxe, {
      clearConsoleOnUpdate: false,
      allowConsoleClears: false,
    })
  }
})
```

#### 3. Lighthouse

```bash
# 命令行
npx lighthouse https://example.com --view

# 或在 Chrome DevTools 中运行
# Lighthouse > 选�� > 无障碍 > "分析页面加载"
```

#### 4. Vitest 测试

```typescript
// tests/a11y/basic.spec.ts
import { describe, it, expect } from 'vitest'
import { axe } from 'jest-axe'
import { mount } from '@vue/test-utils'
import HomePage from '~/pages/index.vue'

describe('无障碍测试', () => {
  it('页面不应有无障碍问题', async () => {
    const wrapper = mount(HomePage)
    const results = await axe(wrapper.element)

    expect(results).toHaveNoViolations()
  })
})
```

### 浏览器扩展

#### 1. axe DevTools

- 下载: [Chrome Web Store](https://chrome.google.com/webstore/detail/axe-devtools/)
- 功能: 扫描页面,识别无障碍问题

#### 2. WAVE

- 网址: https://wave.webaim.org/
- 功能: 可视化无障碍问题

#### 3. Lighthouse

- Chrome DevTools 内置
- 快捷键: F12 > Lighthouse > 无障碍

### 屏幕阅读器

#### Windows

**NVDA (免费,推荐)**
```bash
# 下载: https://www.nvaccess.org/
# 快捷键:
- NVDA + Q: 退出
- NVDA + ↓: 朗读当前元素
- NVDA + Tab: 朗读下一个焦点元素
- H: 跳到下一个标题
- 1-6: 跳到相应级别的标题
- B: 跳到下一个按钮
- L: 跳到下一个列表
- T: 跳到下一个表格
```

**JAWS (商业)**
```bash
# 快捷键:
- Insert + ↓: 朗读所有
- Insert + Tab: 朗读焦点元素
- H: 下一个标题
- Insert + F3: 列表所有标题
```

#### macOS

**VoiceOver (内置)**
```bash
# 启动: Cmd + F5
# 快捷键:
- VO + →: 移动到下一个元素
- VO + ←: 移动到上一个元素
- VO + Shift + ↓: 朗读所有
- VO + U: 打开转子(导航菜单)
```

#### 移动设备

**Android - TalkBack**
```
设置 > 无障碍 > TalkBack > 开启
```

**iOS - VoiceOver**
```
设置 > 辅助功能 > VoiceOver > 开启
```

## 手动测试清单

### 键盘导航测试

```markdown
## 键盘测试清单

- [ ] **Tab 导航**
  - [ ] 所有交互元素可通过 Tab 访问
  - [ ] Tab 顺序符合逻辑(从上到下,从左到右)
  - [ ] 焦点指示器清晰可见

- [ ] **Shift + Tab**
  - [ ] 可以反向导航

- [ ] **Enter / Space**
  - [ ] 按钮可通过 Enter/Space 激活
  - [ ] 链接可通过 Enter 激活
  - [ ] 复选框可通过 Space 切换

- [ ] **方向键**
  - [ ] 列表/菜单可用方向键导航
  - [ ] 单选按钮组可用方向键选择
  - [ ] 滑块可用方向键调整

- [ ] **Escape**
  - [ ] 可关闭模态框
  - [ ] 可关闭下拉菜单
  - [ ] 可退出全屏模式

- [ ] **Home / End**
  - [ ] 列表中跳到首/尾
  - [ ] 文本框中跳到行首/尾

- [ ] **焦点管理**
  - [ ] 模态框打开时焦点移到模态框
  - [ ] 模态框关闭时焦点返回触发元素
  - [ ] 焦点不会被困在某个区域
```

### 屏幕阅读器测试

```markdown
## 屏幕阅读器测试清单

- [ ] **页面结构**
  - [ ] 页面标题正确朗读
  - [ ] 可以通过标题快速导航
  - [ ] 地标(landmark)可识别

- [ ] **文本内容**
  - [ ] 所有文本可被朗读
  - [ ] 文本语言正确识别
  - [ ] 特殊符号正确朗读

- [ ] **链接和按钮**
  - [ ] 链接目的清晰明确
  - [ ] 按钮标签有意义
  - [ ] "点击这里"链接有上下文

- [ ] **图片**
  - [ ] 所有图片有 alt 文本
  - [ ] 装饰性图片标记为 aria-hidden 或 alt=""
  - [ ] 复杂图片有长描述

- [ ] **表单**
  - [ ] 所有输入有关联的 label
  - [ ] 必填字段被标记
  - [ ] 错误消息可被朗读
  - [ ] 字段用途清晰

- [ ] **动态内容**
  - [ ] 新内容出现时被通知
  - [ ] 错误/成功消息被朗读
  - [ ] 加载状态被通知
```

### 颜色对比度测试

```markdown
## 对比度测试清单

- [ ] **文本对比度**
  - [ ] 正常文本 ≥ 4.5:1
  - [ ] 大文本 ≥ 3:1
  - [ ] 文本在所有背景下可读

- [ ] **UI 组件**
  - [ ] 按钮文本 ≥ 4.5:1
  - [ ] 表单边框 ≥ 3:1
  - [ ] 图标 ≥ 3:1

- [ ] **链接**
  - [ ] 未访问链接可识别
  - [ ] 访问过的链接可识别(如适用)
  - [ ] 不仅靠颜色区分(有下划线)

- [ ] **错误提示**
  - [ ] 错误信息有足够对比度
  - [ ] 不只依赖颜色传达错误

- [ ] **深色模式**
  - [ ] 深色模式下对比度也符合标准
```

## 自动化测试脚本

### CI/CD 集成

```yaml
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

      - name: 运行无障碍测试
        run: npm run test:a11y

      - name: 上传报告
        uses: actions/upload-artifact@v3
        with:
          name: a11y-report
          path: a11y-report.html
```

### 测试脚本

```json
// package.json
{
  "scripts": {
    "test:a11y": "pa11y-ci --config .pa11yci.json",
    "test:a11y:watch": "pa11y-ci --config .pa11yci.json --watch"
  }
}
```

```json
// .pa11yci.json
{
  "defaults": {
    "viewport": {
      "width": 1280,
      "height": 1024
    },
    "standard": "WCAG2AA",
    "chromeLaunchConfig": {
      "args": ["--no-sandbox"]
    }
  },
  "urls": [
    "http://localhost:3000",
    "http://localhost:3000/about",
    "http://localhost:3000/products"
  ]
}
```

### Vitest + axe-core

```typescript
// tests/a11y/automated.spec.ts
import { describe, it, expect } from 'vitest'
import { axe, toHaveNoViolations } from 'jest-axe'
import { mount } from '@vue/test-utils'

expect.extend(toHaveNoViolations)

describe('无障碍自动化测试', () => {
  it('首页无障碍', async () => {
    const wrapper = mount(HomePage, {
      global: {
        plugins: [createNuxtApp({})]
      }
    })
    const results = await axe(wrapper.element)
    expect(results).toHaveNoViolations()
  })

  it('按钮有可访问名称', () => {
    const wrapper = mount(Button, {
      slots: {
        default: '点击我'
      }
    })

    const button = wrapper.find('button')
    expect(button.attributes('aria-label') || button.text()).toBeTruthy()
  })

  it('表单有 label 关联', () => {
    const wrapper = mount(FormInput, {
      props: {
        id: 'email',
        label: '电子邮箱'
      }
    })

    const input = wrapper.find('input')
    const label = wrapper.find('label')

    expect(label.attributes('for')).toBe('email')
    expect(input.attributes('id')).toBe('email')
  })
})
```

## 组件级测试

### 测试可访问性属性

```typescript
// tests/components/Button.spec.ts
import { describe, it, expect } from 'vitest'
import { render } from '@testing-library/vue'
import Button from '~/components/Button.vue'

describe('Button 组件', () => {
  it('有可访问名称', () => {
    const { getByRole } = render(Button, {
      slots: { default: '提交' }
    })

    const button = getByRole('button')
    expect(button).toHaveAccessibleName('提交')
  })

  it('禁用状态正确标记', () => {
    const { getByRole } = render(Button, {
      props: { disabled: true }
    })

    const button = getByRole('button')
    expect(button).toBeDisabled()
  })

  it('loading 状态有 aria-live', () => {
    const { getByRole } = render(Button, {
      props: { loading: true }
    })

    const button = getByRole('button')
    expect(button).toHaveAttribute('aria-busy', 'true')
  })
})
```

## 测试报告

### 生成报告

```typescript
// scripts/generate-a11y-report.ts
import { writeFileSync } from 'fs'
import { axe } from 'axe-core'

async function generateA11yReport(url: string) {
  const results = await axe(url)

  const report = `
# 无障碍测试报告

## 测试时间: ${new Date().toLocaleString()}

## 总体结果
- 通过: ${results.passes.length}
- 失败: ${results.violations.length}

## 问题详情

${results.violations.map(v => `
### ${v.id} - ${v.description}

**影响:** ${v.impact}

**元素:**
${v.nodes.map(n => `- \`${n.html}\``).join('\n')}

**修复建议:**
${v.helpUrl}
`).join('\n')}
  `

  writeFileSync('a11y-report.md', report)
}

generateA11yReport('http://localhost:3000')
```

## 持续监控

### 真实用户监控(RUM)

```typescript
// composables/useA11yMonitoring.ts
export function useA11yMonitoring() {
  const reportIssue = (issue: string) => {
    // 发送到监控服务
    $fetch('/api/a11y-report', {
      method: 'POST',
      body: {
        issue,
        url: window.location.href,
        userAgent: navigator.userAgent,
        timestamp: new Date().toISOString()
      }
    })
  }

  // 监听常见无障碍问题
  onMounted(() => {
    // 检测键盘使用
    let keyboardUser = false
    window.addEventListener('keydown', (e) => {
      if (e.key === 'Tab') {
        keyboardUser = true
        document.body.classList.add('keyboard-user')
      }
    })

    window.addEventListener('mousedown', () => {
      keyboardUser = false
      document.body.classList.remove('keyboard-user')
    })

    // 监听屏幕阅读器
    window.addEventListener('resize', () => {
      // 某些屏幕阅读器会触发 resize
    })
  })

  return { reportIssue }
}
```

## 测试最佳实践

1. **分层测试**
   - 单元测试: 组件可访问性属性
   - 集成测试: 用户流程
   - E2E 测试: 完整页面

2. **自动化 + 手动**
   - 自动化测试覆盖基础问题
   - 手动测试验证真实体验

3. **定期测试**
   - 每次 PR 运行自动化测试
   - 每次发布前完整手动测试
   - 每季度使用屏幕阅读器测试

4. **真实用户测试**
   - 邀请残障用户测试
   - 收集反馈并改进
   - 持续优化

## 常用命令速查

```bash
# 运行所有无障碍测试
npm run test:a11y

# 只测试特定页面
npx pa11y http://localhost:3000/about

# Lighthouse CLI
npx lighthouse http://localhost:3000 --only-categories=accessibility

# axe CLI
npx axe http://localhost:3000 --tags wcag2aa
```

## 参考资源

- [WAI-ARIA Authoring Practices](https://www.w3.org/WAI/ARIA/apg/)
- [WebAIM Checklist](https://webaim.org/standards/wcag/checklist)
- [a11y-project checklist](https://www.a11yproject.com/checklist/)
