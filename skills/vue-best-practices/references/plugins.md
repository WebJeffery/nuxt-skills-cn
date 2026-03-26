---
title: 插件最佳实践
impact: MEDIUM
impactDescription: 插件提供全局功能,但滥用会导致命名冲突、难以调试和紧耦合
type: best-practice
tags: [vue3, plugins, app, global, composition]
---

# 插件最佳实践

**影响: MEDIUM** - 插件为 Vue 应用添加全局级功能。谨慎使用它们,避免命名冲突,并偏好 composables 而不是全局属性。

## 任务列表

- 仅在需要全局功能时使用插件
- 避免在插件中添加全局属性
- 使用 provide/inject 进行依赖注入
- 保持插件专注且可测试
- 为插件提供 TypeScript 类型
- 在插件中处理 SSR 兼容性

## 插件结构

**正确示例:**
```javascript
// plugins/myPlugin.js
export default {
  install(app, options) {
    app.provide('myPlugin', {
      doSomething() {
        // 实现
      }
    })
  }
}
```

**错误示例:**
```javascript
// 不要添加全局属性
export default {
  install(app, options) {
    app.config.globalProperties.$myPlugin = {
      doSomething() {
        // 实现
      }
    }
  }
}
```

## 使用 Provide/Inject

**正确:**
```javascript
// 插件
export default {
  install(app) {
    const api = {
      doSomething() {}
    }
    app.provide('myPlugin', api)
  }
}

// 组件中使用
const myPlugin = inject('myPlugin')
```

## TypeScript 支持

```typescript
// plugins/myPlugin.ts
import type { App } from 'vue'

export interface MyPluginOptions {
  apiKey: string
}

export interface MyPluginApi {
  doSomething(): void
}

export default {
  install(app: App, options: MyPluginOptions) {
    const api: MyPluginApi = {
      doSomething() {}
    }
    app.provide('myPlugin', api)
  }
}

// 声明模块类型
declare module 'vue' {
  export interface ComponentCustomProperties {
    $myPlugin: MyPluginApi
  }
}
```

## SSR 兼容性

```javascript
export default {
  install(app) {
    if (import.meta.env.SSR) {
      // SSR 特定逻辑
      return
    }
    
    // 客户端特定逻辑
  }
}
```

## 最佳实践

1. **保持插件专注:**
   - 每个插件只做一件事
   - 避免插件之间的依赖
   - 提供清晰的 API

2. **避免全局污染:**
   - 使用 provide/inject 而不是全局属性
   - 避免修改 Vue 原型
   - 使用前缀避免命名冲突

3. **文档化插件:**
   - 提供清晰的安装说明
   - 说明插件的功能和限制
   - 提供使用示例

4. **考虑替代方案:**
   - 对于可重用逻辑,使用 composables
   - 对于全局样式,使用 CSS
   - 对于全局状态,使用状态管理库
