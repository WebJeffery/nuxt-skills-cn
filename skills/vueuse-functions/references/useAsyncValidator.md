---
category: '@Integrations'
---

# useAsyncValidator

[`async-validator`](https://github.com/yiminghe/async-validator) 的包装器。

## 安装

```bash
npm i async-validator@^4
```

## 用法

```ts
import { useAsyncValidator } from '@vueuse/integrations/useAsyncValidator'
```

## 类型声明

```ts
export type AsyncValidatorError = Error & {
  errors: ValidateError[]
  fields: Record<string, ValidateError[]>
}
export interface UseAsyncValidatorExecuteReturn {
  pass: boolean
  errors: AsyncValidatorError["errors"] | undefined
  errorInfo: AsyncValidatorError | null
  errorFields: AsyncValidatorError["fields"] | undefined
}
export interface UseAsyncValidatorReturn {
  pass: ShallowRef<boolean>
  isFinished: ShallowRef<boolean>
  errors: ComputedRef<AsyncValidatorError["errors"] | undefined>
  errorInfo: ShallowRef<AsyncValidatorError | null>
  errorFields: ComputedRef<AsyncValidatorError["fields"] | undefined>
  execute: () => Promise<UseAsyncValidatorExecuteReturn>
}
export interface UseAsyncValidatorOptions {
  /**
   * @see https://github.com/yiminghe/async-validator#options
   */
  validateOption?: ValidateOption
  /**
   * 验证将在第一次立即触发。
   * 仅当 `manual` 未设置为 true 时有效。
   *
   * @default true
   */
  immediate?: boolean
  /**
   * 如果设置为 true,则不会自动触发验证。
   */
  manual?: boolean
}
/**
 * async-validator 的包装器。
 *
 * @see https://vueuse.org/useAsyncValidator
 * @see https://github.com/yiminghe/async-validator
 */
export declare function useAsyncValidator(
  value: MaybeRefOrGetter<Record<string, any>>,
  rules: MaybeRefOrGetter<Rules>,
  options?: UseAsyncValidatorOptions,
): UseAsyncValidatorReturn & PromiseLike<UseAsyncValidatorReturn>
```
