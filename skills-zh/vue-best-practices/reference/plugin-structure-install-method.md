# 插件结构：Install 方法要求

## 规则

Vue 插件必须是一个具有 `install()` 方法的对象，或者是一个作为 install 函数的函数。install 函数接收 app 实例和可选的用户提供的选项。

## 为什么这很重要

1. **API 契约**：Vue 的 `app.use()` 期望特定的接口。不正确的结构会导致静默失败或错误。

2. **选项传递**：install 方法接收用户传递给 `app.use()` 的选项，从而实现插件配置。

3. **App 访问**：install 方法接收 app 实例，提供对 `app.component()`、`app.directive()`、`app.provide()` 等的访问。

## 插件结构

### 带 install 方法的对象（推荐）

```typescript
// plugins/myPlugin.ts
import type { App } from 'vue'

interface PluginOptions {
  prefix?: string
  debug?: boolean
}

const myPlugin = {
  install(app: App, options: PluginOptions = {}) {
    const { prefix = 'my', debug = false } = options

    if (debug) {
      console.log('Installing myPlugin with prefix:', prefix)
    }

    app.provide('myPlugin', { prefix })
  }
}

export default myPlugin

// 使用
app.use(myPlugin, { prefix: 'custom', debug: true })
```

### 函数作为 install（替代方案）

```typescript
// plugins/simplePlugin.ts
import type { App } from 'vue'

function simplePlugin(app: App, options?: { message: string }) {
  app.config.globalProperties.$greet = () => {
    return options?.message ?? 'Hello!'
  }
}

export default simplePlugin

// 使用
app.use(simplePlugin, { message: 'Welcome!' })
```

### 工厂函数模式（最灵活）

```typescript
// plugins/configuredPlugin.ts
import type { App, Plugin } from 'vue'

interface I18nOptions {
  locale: string
  messages: Record<string, Record<string, string>>
  fallbackLocale?: string
}

export function createI18n(options: I18nOptions): Plugin {
  return {
    install(app: App) {
      // 选项被捕获在闭包中 - 无需通过 app.use() 传递
      const { locale, messages, fallbackLocale = 'en' } = options

      const translate = (key: string): string => {
        return messages[locale]?.[key]
          ?? messages[fallbackLocale]?.[key]
          ?? key
      }

      app.provide('i18n', { translate, locale })
    }
  }
}

// 使用 - 选项传递给工厂函数，而不是 app.use()
const i18n = createI18n({
  locale: 'fr',
  messages: {
    en: { hello: 'Hello' },
    fr: { hello: 'Bonjour' }
  }
})

app.use(i18n)  // 不需要第二个参数
```

## 常见插件功能

```typescript
const fullFeaturedPlugin = {
  install(app: App, options: PluginOptions) {
    // 1. 注册全局组件
    app.component('MyButton', MyButtonComponent)
    app.component('MyInput', MyInputComponent)

    // 2. 注册全局指令
    app.directive('focus', focusDirective)

    // 3. 提供可注入的值（推荐）
    app.provide('pluginConfig', options)

    // 4. 添加全局属性（谨慎使用）
    app.config.globalProperties.$myHelper = helperFunction

    // 5. 添加全局混入（尽可能避免）
    app.mixin({
      created() {
        // 为每个组件运行
      }
    })

    // 6. 自定义错误处理
    app.config.errorHandler = (err, vm, info) => {
      // 处理错误
    }
  }
}
```

## TypeScript：Plugin 类型

使用 `Plugin` 类型进行正确的类型定义：

```typescript
import type { App, Plugin } from 'vue'

// 带选项类型参数
interface MyOptions {
  apiKey: string
}

const myPlugin: Plugin<[MyOptions]> = {
  install(app: App, options: MyOptions) {
    // options 被类型化为 MyOptions
  }
}

// 不带选项
const simplePlugin: Plugin = {
  install(app: App) {
    // 不期望有选项
  }
}
```

## 常见错误

### 缺少 install 方法

```typescript
// 错误 - 这只是一个对象，不是插件
const notAPlugin = {
  doSomething() { /* ... */ }
}
app.use(notAPlugin)  // 错误或静默失败

// 正确
const actualPlugin = {
  install(app) {
    app.provide('service', { doSomething() { /* ... */ } })
  }
}
```

### 忘记使用 app 参数

```typescript
// 错误 - 什么都不做
const uselessPlugin = {
  install(app, options) {
    const service = createService(options)
    // 忘记向 app 注册任何东西！
  }
}

// 正确
const usefulPlugin = {
  install(app, options) {
    const service = createService(options)
    app.provide('service', service)  // 实际使其可用
  }
}
```

## 参考

- [Vue.js 插件文档](https://vuejs.org/guide/reusability/plugins.html)
- [Vue.js 应用程序 API](https://vuejs.org/api/application.html)
