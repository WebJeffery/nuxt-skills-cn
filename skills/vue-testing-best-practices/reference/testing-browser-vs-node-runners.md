---
title: 为样式和 DOM 事件测试选择基于浏览器的运行器
impact: MEDIUM
impactDescription: 基于 Node 的运行器无法测试真实 CSS 行为、原生浏览器事件、cookies 或计算样式
type: capability
tags: [vue3, testing, component-testing, vitest, browser, jsdom]
---

# 为样式和 DOM 事件测试选择基于浏览器的运行器

**影响: MEDIUM** - 基于 Node 的测试运行器(Vitest + jsdom/happy-dom)模拟 DOM 但无法测试真实 CSS 渲染、原生浏览器事件、cookies、计算样式或跨浏览器行为。当这些重要时使用基于浏览器的运行器。

对大多数组件测试使用 Vitest(快速),但在测试视觉/DOM 依赖功能时使用 Vitest Browser Mode。

## 任务清单

- [ ] 使用 Vitest(node)进行逻辑聚焦的组件测试
- [ ] 使用 Vitest Browser Mode 进行样式依赖测试
- [ ] 使用 Vitest Browser Mode 进行原生事件(focus、drag、resize)
- [ ] 使用 Vitest Browser Mode 进行 cookies 和计算 CSS 样式
- [ ] 接受较慢的速度权衡以获得浏览器准确性

## 何时使用每种方法

### 基于 Node 的运行器(Vitest + happy-dom/jsdom)
最适合:
- 纯逻辑测试
- 状态管理
- 事件发射
- Props/slots 行为
- 大多数组件交互
- 快速 CI/CD 管道

```javascript
// vitest.config.js
export default defineConfig({
  test: {
    environment: 'happy-dom',  // 或 'jsdom'
  }
})
```

```javascript
// 快速但有限 - 适用于大多数测试
test('按钮发出点击事件', async () => {
  const wrapper = mount(Button)
  await wrapper.trigger('click')
  expect(wrapper.emitted('click')).toBeTruthy()
})
```

### Vitest Browser Mode
需要用于:
- CSS 计算样式验证
- CSS 过渡/动画
- 真实 focus/blur 行为
- 拖放
- Cookie 操作
- 视口依赖行为
- 跨浏览器验证

## Vitest Browser Mode 设置

```bash
npm install -D @vitest/browser playwright
```

```javascript
// vitest.config.js
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    browser: {
      enabled: true,
      name: 'chromium',
      provider: 'playwright',
    },
  },
})
```

```javascript
// Button.browser.test.js
import { render } from 'vitest-browser-vue'
import Button from './Button.vue'

test('具有正确的悬停样式', async () => {
  const { getByRole } = render(Button, { props: { label: 'Click me' } })

  const button = getByRole('button')

  // 检查初始样式
  await expect.element(button).toHaveStyle({
    backgroundColor: 'rgb(59, 130, 246)'  // blue
  })
})

test('点击后保持焦点', async () => {
  const { getByRole } = render(Button)

  const button = getByRole('button')
  await button.click()

  await expect.element(button).toHaveFocus()
})
```

## 示例:每个运行器可以/不能测试什么

### 样式 - 需要浏览器
```javascript
// Node 运行器: 无法验证实际 CSS
test('危险按钮具有红色背景', () => {
  const wrapper = mount(Button, { props: { variant: 'danger' } })
  // 这只检查类存在,不检查实际颜色
  expect(wrapper.classes()).toContain('bg-red-500')
})

// Vitest Browser Mode: 可以验证计算样式
test('危险按钮渲染红色', async () => {
  const { getByRole } = render(Button, { props: { variant: 'danger' } })
  await expect.element(getByRole('button')).toHaveStyle({
    backgroundColor: 'rgb(239, 68, 68)'
  })
})
```

### 计算 CSS 样式 - 需要浏览器
```javascript
// Node 运行器: 无法获取真实计算样式
test('按钮具有正确的填充', () => {
  const wrapper = mount(Button)
  // getComputedStyle 在 jsdom 中返回空/默认值
  const style = window.getComputedStyle(wrapper.element)
  // style.padding 将是空字符串,不是实际计算值
})

// Vitest Browser Mode: 真实计算样式
test('按钮具有正确的填充', async () => {
  const { getByRole } = render(Button)
  const button = getByRole('button')

  await expect.element(button).toHaveStyle({
    padding: '12px 24px'
  })
})
```

### 原生事件 - 需要浏览器
```javascript
// Node 运行器: 仅合成事件
test('处理拖放', async () => {
  const wrapper = mount(DraggableList)
  // trigger('dragstart') 是合成的 - 可能无法按预期工作
  await wrapper.find('.item').trigger('dragstart')
})

// Vitest Browser Mode: 通过 userEvent 的真实原生事件
import { userEvent } from '@vitest/browser/context'

test('拖动时重新排序项目', async () => {
  const { getByTestId } = render(DraggableList)

  const item = getByTestId('item-1')
  const target = getByTestId('item-3')

  await userEvent.dragAndDrop(item, target)

  // 断言重新排序
})
```

## 推荐的测试策略

```javascript
// vitest.config.js - 分离的测试配置

export default defineConfig({
  test: {
    // 默认: Node 环境以获得速度
    environment: 'happy-dom',

    // 浏览器测试在单独目录中
    include: ['src/**/*.test.{js,ts}'],
  },
})

// 单独运行浏览器测试
// npx vitest --browser.enabled
```

### 目录结构
```
tests/
├── unit/              # 快速基于 node 的测试
│   ├── Button.test.js
│   └── useCounter.test.js
├── component/         # 较慢基于浏览器的测试
│   ├── Button.browser.test.js
│   └── DragDrop.browser.test.js
└── e2e/               # 完整 E2E 测试(Playwright)
    └── user-flow.spec.ts
```

## 参考
- [Vue.js 测试 - 组件测试](https://vuejs.org/guide/scaling-up/testing#component-testing)
- [Vitest Browser Mode](https://vitest.dev/guide/browser.html)
