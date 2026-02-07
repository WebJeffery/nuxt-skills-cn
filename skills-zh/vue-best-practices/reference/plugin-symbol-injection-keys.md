# 在插件中使用 Symbol 注入键

## 规则

在 Vue 插件中使用 `provide/inject` 时，使用 Symbol 键（TypeScript 中最好使用 `InjectionKey<T>`）而不是字符串键，以防止命名冲突并确保类型安全。

## 为什么这很重要

1. **冲突预防**：字符串键如 `'i18n'` 或 `'api'` 容易在多个插件或应用程序的不同部分之间发生冲突。

2. **类型安全**：TypeScript 的 `InjectionKey<T>` 在使用 `inject()` 时提供自动类型推断。

3. **重构安全**：Symbol 是唯一的，因此重命名是安全且明确的。

4. **调试**：Symbol 可以具有描述性名称用于调试，同时保持唯一性。

## 不良实践

```typescript
// plugin.ts
export default {
  install(app) {
    // 字符串键 - 可能与其他插件冲突！
    app.provide('http', axios)
    app.provide('config', appConfig)
  }
}

// component.vue
const http = inject('http')  // 类型是 unknown
const config = inject('config')  // 类型是 unknown

// 另一个插件意外使用相同的键
otherPlugin.install = (app) => {
  app.provide('http', differentHttpClient)  // 冲突！覆盖第一个
}
```

## 良好实践

```typescript
// plugins/keys.ts
import type { InjectionKey } from 'vue'
import type { AxiosInstance } from 'axios'

export interface AppConfig {
  apiUrl: string
  timeout: number
}

// 类型化的注入键 - 唯一且类型安全
export const httpKey: InjectionKey<AxiosInstance> = Symbol('http')
export const configKey: InjectionKey<AppConfig> = Symbol('appConfig')

// plugin.ts
import { httpKey, configKey } from './keys'

export default {
  install(app) {
    app.provide(httpKey, axios)
    app.provide(configKey, { apiUrl: '/api', timeout: 5000 })
  }
}

// component.vue
<script setup lang="ts">
import { inject } from 'vue'
import { httpKey, configKey } from '@/plugins/keys'

// 完全类型化！没有 'unknown' 类型
const http = inject(httpKey)  // 类型：AxiosInstance | undefined
const config = inject(configKey)  // 类型：AppConfig | undefined
</script>
```

## 使用类型安全的默认值

```typescript
// 使用 InjectionKey，默认值会进行类型检查
const config = inject(configKey, {
  apiUrl: '/default-api',
  timeout: 3000
})
// 类型：AppConfig（不是 undefined，因为提供了默认值）

// 如果默认值不匹配，会出现类型错误！
const config = inject(configKey, {
  apiUrl: '/api'
  // 缺少 'timeout' - TypeScript 错误！
})
```

## 组织注入键

对于大型应用程序，按域组织键：

```typescript
// injection-keys/index.ts
export * from './auth'
export * from './i18n'
export * from './http'

// injection-keys/auth.ts
import type { InjectionKey } from 'vue'

export interface AuthService {
  login: (credentials: Credentials) => Promise<User>
  logout: () => Promise<void>
  currentUser: Ref<User | null>
}

export const authKey: InjectionKey<AuthService> = Symbol('auth')

// injection-keys/i18n.ts
export interface I18n {
  t: (key: string, params?: Record<string, string>) => string
  locale: Ref<string>
}

export const i18nKey: InjectionKey<I18n> = Symbol('i18n')
```

## 创建 useInject 辅助函数

为了更清晰的组件代码，创建类型化的组合式函数：

```typescript
// composables/useAuth.ts
import { inject } from 'vue'
import { authKey, type AuthService } from '@/injection-keys'

export function useAuth(): AuthService {
  const auth = inject(authKey)
  if (!auth) {
    throw new Error('Auth plugin not installed. Did you forget app.use(authPlugin)?')
  }
  return auth
}

// component.vue
<script setup>
import { useAuth } from '@/composables/useAuth'

const auth = useAuth()  // 类型：AuthService（不是 undefined）
await auth.login(credentials)
</script>
```

## 何时可以接受字符串键

1. **内部插件使用**：如果 provide 和 inject 都在同一个插件文件中
2. **简单应用程序**：没有冲突风险的非常小的应用程序
3. **动态键**：当键名必须在运行时计算时

即使如此，也要考虑使用命名空间字符串：

```typescript
// 比纯 'config' 更好
app.provide('myPlugin:config', config)
```

## 参考

- [Vue.js 使用 Symbol 键的 Provide/Inject](https://vuejs.org/guide/components/provide-inject.html#working-with-symbol-keys)
- [Vue.js Inject 的 TypeScript 支持](https://vuejs.org/guide/typescript/composition-api.html#typing-provide-inject)
