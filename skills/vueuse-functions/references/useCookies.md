---
category: '@Integrations'
---

# useCookies

[`universal-cookie`](https://www.npmjs.com/package/universal-cookie) 的包装器。

::: tip
当与 Nuxt 3 一起使用时，此函数将**不会**自动导入，以支持 Nuxt 内置的 [`useCookie()`](https://v3.nuxtjs.org/api/composables/use-cookie)。如果您想使用 VueUse 中的函数，请使用显式导入。
:::

## 安装

```bash
npm i universal-cookie@^7
```

## 用法

### 常见用法

```vue
<script setup lang="ts">
import { useCookies } from '@vueuse/integrations/useCookies'

const cookies = useCookies(['locale'])
</script>

<template>
  <div>
    <strong>locale</strong>: {{ cookies.get('locale') }}
    <hr>
    <pre>{{ cookies.getAll() }}</pre>
    <button @click="cookies.set('locale', 'ru-RU')">
      Russian
    </button>
    <button @click="cookies.set('locale', 'en-US')">
      English
    </button>
  </div>
</template>
```

## 选项

使用 vue composition-api 访问和修改 cookies。

> 默认情况下，您应该在 `setup()` 中使用它，但此函数也可以在任何其他地方使用。

```ts
import { useCookies } from '@vueuse/integrations/useCookies'
// ---cut---
const {
  get,
  getAll,
  set,
  remove,
  addChangeListener,
  removeChangeListener
} = useCookies(['cookie-name'], {
  doNotParse: false,
  autoUpdateDependencies: false
})
```

### `dependencies` (可选)

让您可以选择指定组件依赖的 cookie 名称或应该触发重新渲染的 cookie 名称。如果未指定，它将在每次 cookie 更改时渲染。

### `options` (可选)

- `doNotParse` (boolean = false): 无论什么情况，都不将 cookie 转换为对象。**作为默认值传递给 `get`/`getAll` 方法。**
- `autoUpdateDependencies` (boolean = false): 自动添加曾经提供给 `get` 方法的 cookie 名称。如果为 **true**，则您无需关心提供的 `dependencies`。

### `cookies` (可选)

让您提供 `universal-cookie` 实例（默认创建新实例）

> 关于 [universal-cookie api docs](https://www.npmjs.com/package/universal-cookie#api---cookies-class) 中可用方法的信息

## `createCookies([req])`

使用请求创建 `universal-cookie` 实例（默认为 window.document.cookie）并返回带有提供的 universal-cookie 实例的 `useCookies` 函数

- req (object): Node 的 [http.IncomingMessage](https://nodejs.org/api/http.html#http_class_http_incomingmessage) 请求对象

## 类型声明

```ts
/**
 * 创建一个新的 {@link useCookies} 函数
 * @param req - 传入的 http 请求（用于 SSR）
 * @see https://github.com/reactivestack/cookies/tree/master/packages/universal-cookie universal-cookie
 * @description 使用请求（默认为 window.document.cookie）创建 universal-cookie 实例并返回带有提供的 universal-cookie 实例的 {@link useCookies} 函数
 */
export declare function createCookies(req?: IncomingMessage): (
  dependencies?: string[] | null,
  {
    doNotParse,
    autoUpdateDependencies,
  }?: {
    doNotParse?: boolean | undefined
    autoUpdateDependencies?: boolean | undefined
  },
) => {
  /**
   * 按名称响应式获取 cookie。如果 **autoUpdateDependencies = true**，它将更新监视的依赖项
   */
  get: <T = any>(name: string, options?: CookieGetOptions | undefined) => T
  /**
   * 响应式获取所有 cookies
   */
  getAll: <T = any>(options?: CookieGetOptions | undefined) => T
  set: (
    name: string,
    value: any,
    options?: CookieSetOptions | undefined,
  ) => void
  remove: (name: string, options?: CookieSetOptions | undefined) => void
  addChangeListener: (callback: CookieChangeListener) => void
  removeChangeListener: (callback: CookieChangeListener) => void
}
/**
 * 使用 cookies 的响应式方法（如果您使用 SSR，请改用 {@link createCookies} 方法）
 * @param dependencies - 监视的 cookie 名称数组。如果不想监视 cookies 更改，请传递空数组。
 * @param options
 * @param options.doNotParse - 不要尝试将值解析为 JSON
 * @param options.autoUpdateDependencies - 自动更新监视的依赖项
 * @param cookies - universal-cookie 实例
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useCookies(
  dependencies?: string[] | null,
  {
    doNotParse,
    autoUpdateDependencies,
  }?: {
    doNotParse?: boolean | undefined
    autoUpdateDependencies?: boolean | undefined
  },
  cookies?: Cookie,
): {
  /**
   * 按名称响应式获取 cookie。如果 **autoUpdateDependencies = true**，它将更新监视的依赖项
   */
  get: <T = any>(name: string, options?: CookieGetOptions | undefined) => T
  /**
   * 响应式获取所有 cookies
   */
  getAll: <T = any>(options?: CookieGetOptions | undefined) => T
  set: (
    name: string,
    value: any,
    options?: CookieSetOptions | undefined,
  ) => void
  remove: (name: string, options?: CookieSetOptions | undefined) => void
  addChangeListener: (callback: CookieChangeListener) => void
  removeChangeListener: (callback: CookieChangeListener) => void
}
```
