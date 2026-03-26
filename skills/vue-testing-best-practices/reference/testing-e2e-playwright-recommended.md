---
title: 使用 Playwright 进行 E2E 测试 - 跨浏览器支持和更好的 DX
impact: MEDIUM
impactDescription: Cypress 有浏览器限制,某些功能需要付费订阅
type: best-practice
tags: [vue3, testing, e2e, playwright, cypress, end-to-end]
---

# 使用 Playwright 进行 E2E 测试 - 跨浏览器支持和更好的 DX

**影响: MEDIUM** - Playwright 提供卓越的跨浏览器测试(Chromium、WebKit、Firefox)、出色的调试工具,并且完全开源。Cypress 对 WebKit 支持有限,某些功能需要付费订阅。

对于新的 E2E 测试设置使用 Playwright。如果团队已经有专业知识或其视觉调试 UI,则考虑 Cypress。

## 任务清单

- [ ] 为目标平台安装带有浏览器的 Playwright
- [ ] 配置 Vue 开发服务器集成
- [ ] 为不同浏览器设置项目
- [ ] 使用匹配组件测试模式的定位器策略
- [ ] 配置 CI 以进行并行测试执行
- [ ] 使用 trace 和截图功能进行调试

## 快速设置

```bash
# 安装 Playwright
npm init playwright@latest

# 这将创建:
# - playwright.config.ts
# - tests/ 目录
# - tests-examples/ 目录
```

**playwright.config.ts:**
```typescript
import { defineConfig, devices } from '@playwright/test'

export default defineConfig({
  testDir: './e2e',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: 'html',

  use: {
    // 用于导航的基本 URL
    baseURL: 'http://localhost:5173',
    // 在第一次重试时捕获 trace
    trace: 'on-first-retry',
    // 失败时截图
    screenshot: 'only-on-failure',
  },

  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] },
    },
    {
      name: 'webkit',
      use: { ...devices['Desktop Safari'] },
    },
    // 移动视口
    {
      name: 'Mobile Chrome',
      use: { ...devices['Pixel 5'] },
    },
  ],

  // 在测试前运行本地开发服务器
  webServer: {
    command: 'npm run dev',
    url: 'http://localhost:5173',
    reuseExistingServer: !process.env.CI,
  },
})
```

## E2E 测试示例

```typescript
// e2e/user-flow.spec.ts
import { test, expect } from '@playwright/test'

test.describe('用户认证', () => {
  test('用户可以登录并查看仪表板', async ({ page }) => {
    // 导航到登录
    await page.goto('/login')

    // 填写登录表单
    await page.getByLabel('Email').fill('user@example.com')
    await page.getByLabel('Password').fill('password123')
    await page.getByRole('button', { name: 'Sign In' }).click()

    // 验证重定向到仪表板
    await expect(page).toHaveURL('/dashboard')
    await expect(page.getByRole('heading', { name: 'Welcome' })).toBeVisible()
  })

  test('对无效凭据显示错误', async ({ page }) => {
    await page.goto('/login')

    await page.getByLabel('Email').fill('wrong@example.com')
    await page.getByLabel('Password').fill('wrongpassword')
    await page.getByRole('button', { name: 'Sign In' }).click()

    await expect(page.getByRole('alert')).toContainText('Invalid credentials')
    await expect(page).toHaveURL('/login')
  })
})
```

## Playwright vs Cypress 比较

| 功能 | Playwright | Cypress |
|---------|------------|---------|
| 浏览器 | Chromium, Firefox, WebKit | Chromium, Firefox, Electron (WebKit 实验性) |
| 跨浏览器 | 完全支持 | 有限 |
| 并行化 | 内置 | 需要 Cypress Cloud |
| 开源 | 完全 | 仅核心 |
| 移动测试 | 设备模拟 | 有限 |
| 调试 | Inspector, trace viewer | 时间旅行 UI |
| API 测试 | 内置 | 需要插件 |
| Iframes | 完全支持 | 有限 |

## 使用 Data-Testid 测试 Vue 组件

```typescript
// e2e/product-list.spec.ts
import { test, expect } from '@playwright/test'

test('用户可以将产品添加到购物车', async ({ page }) => {
  await page.goto('/products')

  // 使用 data-testid 进行可靠的定位器
  await page.getByTestId('product-card').first().click()

  // 验证产品详情页面
  await expect(page.getByTestId('product-title')).toBeVisible()

  // 添加到购物车
  await page.getByTestId('add-to-cart-button').click()

  // 验证购物车更新
  await expect(page.getByTestId('cart-count')).toHaveText('1')
})
```

## Vue 应用的页面对象模式

```typescript
// e2e/pages/LoginPage.ts
import { Page, Locator } from '@playwright/test'

export class LoginPage {
  readonly page: Page
  readonly emailInput: Locator
  readonly passwordInput: Locator
  readonly submitButton: Locator
  readonly errorMessage: Locator

  constructor(page: Page) {
    this.page = page
    this.emailInput = page.getByLabel('Email')
    this.passwordInput = page.getByLabel('Password')
    this.submitButton = page.getByRole('button', { name: 'Sign In' })
    this.errorMessage = page.getByRole('alert')
  }

  async goto() {
    await this.page.goto('/login')
  }

  async login(email: string, password: string) {
    await this.emailInput.fill(email)
    await this.passwordInput.fill(password)
    await this.submitButton.click()
  }
}
```

```typescript
// e2e/auth.spec.ts
import { test, expect } from '@playwright/test'
import { LoginPage } from './pages/LoginPage'

test('成功登录', async ({ page }) => {
  const loginPage = new LoginPage(page)
  await loginPage.goto()
  await loginPage.login('user@example.com', 'password123')

  await expect(page).toHaveURL('/dashboard')
})
```

## 视觉回归测试

```typescript
test('主页视觉回归', async ({ page }) => {
  await page.goto('/')

  // 全页截图比较
  await expect(page).toHaveScreenshot('homepage.png')

  // 元素特定截图
  await expect(page.getByTestId('hero-section')).toHaveScreenshot('hero.png')
})
```

## 运行测试

```bash
# 运行所有测试
npx playwright test

# 在 headed 模式下运行(查看浏览器)
npx playwright test --headed

# 运行特定文件
npx playwright test e2e/auth.spec.ts

# 在特定浏览器中运行
npx playwright test --project=chromium

# 调试模式
npx playwright test --debug

# 从操作生成测试
npx playwright codegen localhost:5173
```

## 参考
- [Playwright 文档](https://playwright.dev/)
- [Vue.js E2E 测试推荐](https://vuejs.org/guide/scaling-up/testing#e2e-testing)
- [Playwright 最佳实践](https://playwright.dev/docs/best-practices)
