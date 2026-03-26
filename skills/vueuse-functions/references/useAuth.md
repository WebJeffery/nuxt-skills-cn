---
category: '@Firebase'
---

# useAuth

响应式 [Firebase Auth](https://firebase.google.com/docs/auth) 绑定。它提供了响应式 `user` 和 `isAuthenticated`,因此您可以轻松响应用户身份验证状态的变化。

## 用法

```vue
<script setup lang="ts">
import { useAuth } from '@vueuse/firebase/useAuth'
import { initializeApp } from 'firebase/app'
import { getAuth, GoogleAuthProvider, signInWithPopup } from 'firebase/auth'

const app = initializeApp({ /* config */ })
const auth = getAuth(app)
const { isAuthenticated, user } = useAuth(auth)

const signIn = () => signInWithPopup(auth, new GoogleAuthProvider())
</script>

<template>
  <pre v-if="isAuthenticated">{{ user }}</pre>
  <div v-else>
    <button @click="signIn">
      使用 Google 登录
    </button>
  </div>
</template>
```

## 返回值

| 名称              | 类型                   | 描述                                               |
| ----------------- | ---------------------- | --------------------------------------------------------- |
| `user`            | `Ref<User \| null>`    | 当前的 Firebase 用户,如果未验证则为 `null` |
| `isAuthenticated` | `ComputedRef<boolean>` | 用户当前是否已验证                 |

该组合式函数使用 Firebase 的 `onIdTokenChanged` 监听器,在用户的 ID 令牌更改时(包括登录、登出和令牌刷新事件)自动更新。

## 类型声明

```ts
export interface UseFirebaseAuthOptions {
  isAuthenticated: ComputedRef<boolean>
  user: Ref<User | null>
}
/**
 * 响应式 Firebase Auth 绑定
 *
 * @see https://vueuse.org/useAuth
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useAuth(auth: Auth): {
  isAuthenticated: ComputedRef<boolean>
  user: Ref<
    {
      readonly emailVerified: boolean
      readonly isAnonymous: boolean
      readonly metadata: {
        readonly creationTime?: string | undefined
        readonly lastSignInTime?: string | undefined
      }
      readonly providerData: {
        readonly displayName: string | null
        readonly email: string | null
        readonly phoneNumber: string | null
        readonly photoURL: string | null
        readonly providerId: string
        readonly uid: string
      }[]
      readonly refreshToken: string
      readonly tenantId: string | null
      delete: () => Promise<void>
      getIdToken: (forceRefresh?: boolean) => Promise<string>
      getIdTokenResult: (forceRefresh?: boolean) => Promise<IdTokenResult>
      reload: () => Promise<void>
      toJSON: () => object
      readonly displayName: string | null
      readonly email: string | null
      readonly phoneNumber: string | null
      readonly photoURL: string | null
      readonly providerId: string
      readonly uid: string
    } | null,
    | User
    | {
        readonly emailVerified: boolean
        readonly isAnonymous: boolean
        readonly metadata: {
          readonly creationTime?: string | undefined
          readonly lastSignInTime?: string | undefined
        }
        readonly providerData: {
          readonly displayName: string | null
          readonly email: string | null
          readonly phoneNumber: string | null
          readonly photoURL: string | null
          readonly providerId: string
          readonly uid: string
        }[]
        readonly refreshToken: string
        readonly tenantId: string | null
        delete: () => Promise<void>
        getIdToken: (forceRefresh?: boolean) => Promise<string>
        getIdTokenResult: (forceRefresh?: boolean) => Promise<IdTokenResult>
        reload: () => Promise<void>
        toJSON: () => object
        readonly displayName: string | null
        readonly email: string | null
        readonly phoneNumber: string | null
        readonly photoURL: string | null
        readonly providerId: string
        readonly uid: string
      }
    | null
  >
}
```
