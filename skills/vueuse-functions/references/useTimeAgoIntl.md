---
category: Time
---

# useTimeAgoIntl

支持 i18n 的响应式时间前缀。当时间更改时自动更新时间前缀字符串。由 `Intl.RelativeTimeFormat` 提供支持。

## 用法

```js
import { useTimeAgoIntl } from '@vueuse/core'

const timeAgoIntl = useTimeAgoIntl(new Date(2021, 0, 1))
```

## 非响应式用法

如果您不需要响应式，可以使用 `formatTimeAgo` 函数来获取格式化的字符串而不是 Ref。

```js
import { formatTimeAgoIntl } from '@vueuse/core'

const timeAgoIntl = formatTimeAgoIntl(new Date(2021, 0, 1)) // string
```

## 类型声明

```ts
type Locale = Intl.UnicodeBCP47LocaleIdentifier | Intl.Locale
export interface FormatTimeAgoIntlOptions {
  /**
   * 用于格式化的区域设置
   *
   * @default undefined
   * @see https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/RelativeTimeFormat/RelativeTimeFormat#locales
   */
  locale?: Locale
  /**
   * @see https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/RelativeTimeFormat/RelativeTimeFormat#options
   */
  relativeTimeFormatOptions?: Intl.RelativeTimeFormatOptions
  /**
   * 是否在部分之间插入空格。
   *
   * 如果提供了 `joinParts`，则忽略。
   *
   * @default true
   */
  insertSpace?: boolean
  /**
   * 自定义函数来连接由 `Intl.RelativeTimeFormat.formatToParts` 返回的部分。
   *
   * 如果提供，将使用此函数而不是默认的连接逻辑。
   */
  joinParts?: (
    parts: Intl.RelativeTimeFormatPart[],
    locale?: Intl.UnicodeBCP47LocaleIdentifier | Intl.Locale,
  ) => string
  /**
   * 自定义单位
   */
  units?: TimeAgoUnit[]
}
export interface UseTimeAgoIntlOptions<Controls extends boolean>
  extends FormatTimeAgoIntlOptions, ConfigurableScheduler {
  /**
   * 暴露更多控件和原始 `parts` 结果。
   *
   * @default false
   */
  controls?: Controls
  /**
   * 更新间隔（以毫秒为单位），设置 0 以禁用自动更新
   *
   * @deprecated 请改用 `scheduler` 选项
   * @default 30_000
   */
  updateInterval?: number
}
type UseTimeAgoReturn<Controls extends boolean = false> = Controls extends true
  ? {
      timeAgoIntl: ComputedRef<string>
      parts: ComputedRef<Intl.RelativeTimeFormatPart[]>
    } & Pausable
  : ComputedRef<string>
export interface TimeAgoUnit {
  name: Intl.RelativeTimeFormatUnit
  ms: number
}
/**
 * `Intl.RelativeTimeFormat` 的响应式包装器。
 */
export declare function useTimeAgoIntl(
  time: MaybeRefOrGetter<Date | number | string>,
  options?: UseTimeAgoIntlOptions<false>,
): UseTimeAgoReturn<false>
export declare function useTimeAgoIntl(
  time: MaybeRefOrGetter<Date | number | string>,
  options: UseTimeAgoIntlOptions<true>,
): UseTimeAgoReturn<true>
/**
 * useTimeAgoIntl 的非响应式版本
 */
export declare function formatTimeAgoIntl(
  from: Date,
  options?: FormatTimeAgoIntlOptions,
  now?: Date | number,
): string
/**
 * 将部分格式化为字符串
 */
export declare function formatTimeAgoIntlParts(
  parts: Intl.RelativeTimeFormatPart[],
  options?: FormatTimeAgoIntlOptions,
): string
```
