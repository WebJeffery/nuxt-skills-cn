---
title: 在插件中优先使用 provide/inject 而不是全局属性
impact: MEDIUM
impactDescription: globalProperties 在 setup() 中不起作用；provide/inject 更适合组合式 API
type: best-practice
tags: [vue3, plugins, provide-inject, global-properties, composition-api]
---

# 在插件中优先使用 provide/inject 而不是全局属性

## 规则

创建 Vue 插件时，优先使用 `app.provide()` 使插件功能可用于组件，而不是将属性附加到 `app.config.globalProperties`。

## 为什么这很重要

1. **globalProperties 在 setup() 中不起作用**：附加到 `globalProperties` 的属性只能通过 Options API 中的 `this` 访问。它们在组合式 API 的 `setup()` 函数中不可用。

2. **类型安全**：`provide/inject` 与 TypeScript 更好地集成，并且需要更少的类型扩充样板代码。

3. **可测试性**：与全局属性相比，注入的依赖项在测试中更容易模拟。

4. **代码清晰度**：显式的 `inject()` 调用使依赖项可见，而全局属性可能看起来"神奇"。

5. **作用域**：`provide/inject` 遵循 Vue 的组件层次结构，使得更容易为应用的不同部分提供不同的值。

## 不良实践

```typescript
// plugins/i18n.ts
export default {
  install(app, options) {
    // 附加到 globalProperties - 仅适用于 Options API
    app.config.globalProperties.$translate = (key: string) => {
      return key.split('.').reduce((o, i) => o?.[i], options)
    }
  }
}

// 在组件中 - TypeScript 需要类型扩充
// 也不在 <script setup> 中工作
export default {
  mounted() {
    console.log(this.$translate('greeting.hello'))
  }
}
```

## 良好实践

```typescript
// plugins/i18n.ts
import type { InjectionKey, App } from 'vue'

export interface I18nOptions {
  [key: string]: string | I18nOptions
}

export interface I18n {
  translate: (key: string) => string
  options: I18nOptions
}

export const i18nKey: InjectionKey<I18n> = Symbol('i18n')

export default {
  install(app: App, options: I18nOptions) {
    const translate = (key: string): string => {
      return key.split('.').reduce((o, i) => o?.[i], options) as string ?? key
    }

    // 使用 provide 以兼容组合式 API
    app.provide(i18nKey, { translate, options })
  }
}

// 在组件中 - 在 setup() 中工作并具有完整的类型安全
<script setup lang="ts">
import { inject } from 'vue'
import { i18nKey } from '@/plugins/i18n'

const i18n = inject(i18nKey)
console.log(i18n?.translate('greeting.hello'))
</script>
```

## 混合方法

如果你必须支持两个 API（例如，为了向后兼容），同时提供两者：

```typescript
export default {
  install(app: App, options: I18nOptions) {
    const i18n = {
      translate: (key: string) => { /* ... */ }
    }

    // 用于组合式 API
    app.provide(i18nKey, i18n)

    // 用于 Options API（谨慎使用）
    app.config.globalProperties.$i18n = i18n
  }
}
```

## TypeScript 类型扩充（如果使用 globalProperties）

如果你必须使用 globalProperties，你需要适当的类型扩充：

```typescript
// types/vue.d.ts
export {}

declare module 'vue' {
  interface ComponentCustomProperties {
    $translate: (key: string) => string
  }
}
```

**重要**：文件必须包含 `export {}` 或另一个顶级导出/导入。没有它，扩充将覆盖类型而不是扩充它们。

## 参考
- [Vue.js 插件文档](https://vuejs.org/guide/reusability/plugins.html)
- [Vue.js Provide/Inject](https://vuejs.org/guide/components/provide-inject.html)
- [Options API 的 TypeScript](https://vuejs.org/guide/typescript/options-api.html)
