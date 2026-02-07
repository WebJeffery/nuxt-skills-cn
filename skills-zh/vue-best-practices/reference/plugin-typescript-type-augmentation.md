# 插件的正确 TypeScript 类型扩展

## 规则

当创建添加全局属性的 Vue 插件时，你必须正确扩展 TypeScript 类型。扩展文件必须包含至少一个顶级 `import` 或 `export` 语句，才能被视为模块。

## 为什么这很重要

1. **没有模块语法，类型会被覆盖**：如果你的扩展文件不是模块，它将覆盖 Vue 的类型而不是扩展它们，从而破坏整个应用程序的类型检查。

2. **类型安全**：正确的扩展为插件提供的属性启用自动完成和类型检查。

3. **IDE 支持**：开发人员为全局属性（如 `this.$translate`）获得适当的 IntelliSense。

4. **错误预防**：在编译时而不是运行时捕获拼写错误和不正确的用法。

## 关键规则：需要模块语法

```typescript
// 错误 - 这会覆盖 Vue 类型而不是扩展它们！
// types/vue.d.ts
declare module 'vue' {
  interface ComponentCustomProperties {
    $translate: (key: string) => string
  }
}

// 正确 - export {} 使其成为模块，因此它扩展类型
// types/vue.d.ts
export {}  // 这一行很关键！

declare module 'vue' {
  interface ComponentCustomProperties {
    $translate: (key: string) => string
  }
}
```

## 完整的插件类型扩展示例

```typescript
// plugins/i18n.ts
import type { App, InjectionKey } from 'vue'

export interface I18nOptions {
  locale: string
  messages: Record<string, Record<string, string>>
}

export interface I18nInstance {
  translate: (key: string) => string
  locale: string
}

export const i18nInjectionKey: InjectionKey<I18nInstance> = Symbol('i18n')

export function createI18n(options: I18nOptions) {
  const i18n: I18nInstance = {
    translate(key: string) {
      return options.messages[options.locale]?.[key] ?? key
    },
    locale: options.locale
  }

  return {
    install(app: App) {
      // 用于 Composition API
      app.provide(i18nInjectionKey, i18n)

      // 用于 Options API / 模板
      app.config.globalProperties.$t = i18n.translate
      app.config.globalProperties.$i18n = i18n
    }
  }
}

// types/i18n.d.ts（或在同一文件中的 export 之后）
export {}

declare module 'vue' {
  interface ComponentCustomProperties {
    $t: (key: string) => string
    $i18n: I18nInstance
  }
}
```

## 替代方案：扩展 @vue/runtime-core

某些插件扩展 `@vue/runtime-core` 而不是 `vue`：

```typescript
// types/global.d.ts
export {}

declare module '@vue/runtime-core' {
  interface ComponentCustomProperties {
    $myPlugin: MyPluginInstance
  }
}
```

两种方法都有效，但在应用程序代码中 `'vue'` 更常见。

## 确保 tsconfig.json 包含声明文件

```json
{
  "compilerOptions": {
    // ...
  },
  "include": [
    "src/**/*.ts",
    "src/**/*.vue",
    "types/**/*.d.ts"  // 包含你的声明文件
  ]
}
```

## 对于库作者：package.json Types 字段

如果将插件作为包发布：

```json
{
  "name": "my-vue-plugin",
  "types": "./dist/types/index.d.ts",
  "exports": {
    ".": {
      "types": "./dist/types/index.d.ts",
      "import": "./dist/index.mjs"
    }
  }
}
```

## 常见错误和解决方案

### 错误：类型上不存在属性 '$xyz'

1. 检查你的 `.d.ts` 文件是否有 `export {}` 或 import 语句
2. 验证文件是否包含在 `tsconfig.json` 中
3. 重启你的 TypeScript 语言服务器（VS Code：Cmd+Shift+P > "Restart TS Server"）

### 错误：类型在某些组件中有效但在其他组件中无效

这通常发生在使用 Vetur 而不是 Volar 时。如果你使用 Vue 3，请切换到 Volar（Vue - Official 扩展）。

### Options API 中有错误但 Composition API 中没有

`this` 上的全局属性需要正确扩展 `ComponentCustomProperties`。Composition API 使用 `inject()`，它是单独类型化的。

## 参考

- [Vue.js TypeScript 与 Options API](https://vuejs.org/guide/typescript/options-api.html)
- [TypeScript 模块扩展](https://www.typescriptlang.org/docs/handbook/declaration-merging.html#module-augmentation)
- [Vue.js 插件文档](https://vuejs.org/guide/reusability/plugins.html)
