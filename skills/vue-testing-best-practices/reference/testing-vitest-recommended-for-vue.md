---
title: 使用 Vitest 进行 Vue 3 测试 - Vue 团队推荐
impact: MEDIUM
impactDescription: 在 Vite 项目中使用 Jest 或其他运行器需要复杂的配置,并导致测试运行速度变慢
type: best-practice
tags: [vue3, testing, vitest, vite, configuration, setup]
---

# 使用 Vitest 进行 Vue 3 测试 - Vue 团队推荐

**影响: MEDIUM** - Vitest 由 Vue/Vite 团队成员创建和维护,与 Vite 共享相同的配置和转换管道。在基于 Vite 的项目中使用 Jest 或其他测试运行器需要额外的配置,并可能导致测试执行速度变慢和兼容性问题。

为新的 Vue 3 项目使用 Vitest。仅在迁移现有测试套件时考虑 Jest。

## 任务清单

- [ ] 安装 Vitest 和 Vue 测试相关包
- [ ] 在 vite.config.js 或 vitest.config.js 中配置 vitest
- [ ] 设置正确的测试环境(happy-dom 或 jsdom)
- [ ] 在 package.json 中添加测试脚本
- [ ] 如果需要更简洁的测试语法,配置 globals
- [ ] 使用 @vue/test-utils 进行组件挂载

## 快速设置

```bash
# 安装必需的包
npm install -D vitest @vue/test-utils happy-dom
# 或使用 jsdom
npm install -D vitest @vue/test-utils jsdom
```

**vite.config.js:**
```javascript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
  test: {
    // 启用全局测试 API(describe, it, expect)
    globals: true,
    // 使用 happy-dom 以获得更快的测试(或 'jsdom' 以获得更好的兼容性)
    environment: 'happy-dom',
    // 可选:用于全局配置的设置文件
    setupFiles: ['./src/test/setup.js']
  }
})
```

**package.json:**
```json
{
  "scripts": {
    "test": "vitest",
    "test:run": "vitest run",
    "test:coverage": "vitest run --coverage"
  }
}
```

**tsconfig.json (如果使用 TypeScript):**
```json
{
  "compilerOptions": {
    "types": ["vitest/globals"]
  }
}
```

## 测试文件示例

```javascript
// src/components/Counter.test.js
import { describe, it, expect, beforeEach } from 'vitest'  // 使用 globals: true 时可选
import { mount } from '@vue/test-utils'
import Counter from './Counter.vue'

describe('Counter', () => {
  let wrapper

  beforeEach(() => {
    wrapper = mount(Counter)
  })

  it('渲染初始计数', () => {
    expect(wrapper.find('[data-testid="count"]').text()).toBe('0')
  })

  it('点击按钮时递增', async () => {
    await wrapper.find('[data-testid="increment"]').trigger('click')
    expect(wrapper.find('[data-testid="count"]').text()).toBe('1')
  })
})
```

## Vitest vs Jest 比较

| 功能 | Vitest | Jest |
|---------|--------|------|
| Vite 集成 | 原生 | 需要配置 |
| 速度 | 非常快(原生 ESM) | 与 Vite 一起使用时较慢 |
| 监视模式 | 优秀 | 良好 |
| Vue SFC 支持 | 与 Vite 一起工作 | 需要 vue-jest |
| 配置共享 | 与 vite.config 相同 | 分离 |
| API | Jest 兼容 | 标准 |

## 与 Testing Library 一起使用

```bash
npm install -D @testing-library/vue @testing-library/jest-dom
```

```javascript
// src/test/setup.js
import { expect } from 'vitest'
import * as matchers from '@testing-library/jest-dom/matchers'

expect.extend(matchers)
```

```javascript
// Component.test.js
import { render, screen, fireEvent } from '@testing-library/vue'
import UserCard from './UserCard.vue'

test('显示用户名', () => {
  render(UserCard, {
    props: { name: 'John Doe' }
  })

  expect(screen.getByText('John Doe')).toBeInTheDocument()
})
```

## 高级配置

```javascript
// vitest.config.js (如果首选单独文件)
import { defineConfig } from 'vitest/config'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
  test: {
    globals: true,
    environment: 'happy-dom',
    include: ['**/*.{test,spec}.{js,ts,jsx,tsx}'],
    exclude: ['node_modules', 'dist', 'e2e'],
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      exclude: ['node_modules', 'test']
    },
    // 有助于调试
    reporters: ['verbose'],
    // 在 CI 中按顺序运行测试
    poolOptions: {
      threads: {
        singleThread: process.env.CI === 'true'
      }
    }
  }
})
```

## 常见模式

### 模拟模块
```javascript
import { vi } from 'vitest'

vi.mock('@/api/users', () => ({
  fetchUser: vi.fn().mockResolvedValue({ name: 'John' })
}))
```

### 使用假计时器测试
```javascript
import { vi, beforeEach, afterEach } from 'vitest'

beforeEach(() => {
  vi.useFakeTimers()
})

afterEach(() => {
  vi.restoreAllMocks()
})

test('防抖搜索', async () => {
  const wrapper = mount(SearchBox)
  await wrapper.find('input').setValue('vue')

  vi.advanceTimersByTime(300)
  await flushPromises()

  expect(wrapper.emitted('search')).toBeTruthy()
})
```

## 参考
- [Vitest 文档](https://vitest.dev/)
- [Vue.js 测试指南](https://vuejs.org/guide/scaling-up/testing)
- [Vue Test Utils](https://test-utils.vuejs.org/)
