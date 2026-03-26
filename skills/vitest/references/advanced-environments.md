---
name: test-environments
description: 配置环境,如 jsdom、happy-dom 用于浏览器 API
---

# 测试环境

## 可用环境

- `node`(默认) - Node.js 环境
- `jsdom` - 具有浏览器 API 的类浏览器环境
- `happy-dom` - jsdom 的更快替代品
- `edge-runtime` - Vercel Edge Runtime

## 配置

```ts
// vitest.config.ts
defineConfig({
  test: {
    environment: 'jsdom',
    
    // 环境特定选项
    environmentOptions: {
      jsdom: {
        url: 'http://localhost',
      },
    },
  },
})
```

## 安装环境包

```bash
# jsdom
npm i -D jsdom

# happy-dom(更快,API 更少)
npm i -D happy-dom
```

## 每文件环境

在文件顶部使用魔术注释:

```ts
// @vitest-environment jsdom

import { expect, test } from 'vitest'

test('DOM test', () => {
  const div = document.createElement('div')
  expect(div).toBeInstanceOf(HTMLDivElement)
})
```

## jsdom 环境

完整的浏览器环境模拟:

```ts
// @vitest-environment jsdom

test('DOM manipulation', () => {
  document.body.innerHTML = '<div id="app"></div>'
  
  const app = document.getElementById('app')
  app.textContent = 'Hello'
  
  expect(app.textContent).toBe('Hello')
})

test('window APIs', () => {
  expect(window.location.href).toBeDefined()
  expect(localStorage).toBeDefined()
})
```

### jsdom 选项

```ts
defineConfig({
  test: {
    environmentOptions: {
      jsdom: {
        url: 'http://localhost:3000',
        html: '<!DOCTYPE html><html><body></body></html>',
        userAgent: 'custom-agent',
        resources: 'usable',
      },
    },
  },
})
```

## happy-dom 环境

更快但 API 更少:

```ts
// @vitest-environment happy-dom

test('basic DOM', () => {
  const el = document.createElement('div')
  el.className = 'test'
  expect(el.className).toBe('test')
})
```

## 每项目多个环境

使用项目进行不同环境:

```ts
defineConfig({
  test: {
    projects: [
      {
        test: {
          name: 'unit',
          include: ['tests/unit/**/*.test.ts'],
          environment: 'node',
        },
      },
      {
        test: {
          name: 'dom',
          include: ['tests/dom/**/*.test.ts'],
          environment: 'jsdom',
        },
      },
    ],
  },
})
```

## 自定义环境

创建自定义环境包:

```ts
// vitest-environment-custom/index.ts
import type { Environment } from 'vitest/runtime'

export default <Environment>{
  name: 'custom',
  viteEnvironment: 'ssr', // 或 'client'
  
  setup() {
    // 设置全局状态
    globalThis.myGlobal = 'value'
    
    return {
      teardown() {
        delete globalThis.myGlobal
      },
    }
  },
}
```

使用:

```ts
defineConfig({
  test: {
    environment: 'custom',
  },
})
```

## 使用 VM 的环境

用于完全隔离:

```ts
export default <Environment>{
  name: 'isolated',
  viteEnvironment: 'ssr',
  
  async setupVM() {
    const vm = await import('node:vm')
    const context = vm.createContext()
    
    return {
      getVmContext() {
        return context
      },
      teardown() {},
    }
  },
  
  setup() {
    return { teardown() {} }
  },
}
```

## 浏览器模式(与环境分离)

对于真正的浏览器测试,使用 Vitest 浏览器模式:

```ts
defineConfig({
  test: {
    browser: {
      enabled: true,
      name: 'chromium', // 或 'firefox'、'webkit'
      provider: 'playwright',
    },
  },
})
```

## CSS 和资源

在 jsdom/happy-dom 中,配置 CSS 处理:

```ts
defineConfig({
  test: {
    css: true, // 处理 CSS
    
    // 或使用选项
    css: {
      include: /\.module\.css$/,
      modules: {
        classNameStrategy: 'non-scoped',
      },
    },
  },
})
```

## 修复外部依赖

如果外部依赖因 CSS/资源错误而失败:

```ts
defineConfig({
  test: {
    server: {
      deps: {
        inline: ['problematic-package'],
      },
    },
  },
})
```

## 关键点

- 默认是 `node` - 没有浏览器 API
- 使用 `jsdom` 进行完整的浏览器模拟
- 使用 `happy-dom` 进行具有基本 DOM 的更快测试
- 通过 `// @vitest-environment` 注释进行每文件环境
- 使用项目进行多个环境配置
- 浏览器模式用于真正的浏览器测试,而不是环境

<!-- 
Source references:
- https://vitest.dev/guide/environment.html
-->
