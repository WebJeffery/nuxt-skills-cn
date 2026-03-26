---
category: Time
---

# useDateFormat

根据传入的令牌字符串获取格式化日期，灵感来自 [dayjs](https://github.com/iamkun/dayjs)。

**所有可用格式的列表（默认为 HH:mm:ss）：**

| 格式 | 输出                   | 描述                             |
| ------ | ------------------------ | --------------------------------------- |
| `Yo`   | 2018th                   | 序数格式化年份                  |
| `YY`   | 18                       | 两位数年份                          |
| `YYYY` | 2018                     | 四位数年份                         |
| `M`    | 1-12                     | 月份，从 1 开始               |
| `Mo`   | 1st, 2nd, ..., 12th      | 月份，序数格式化            |
| `MM`   | 01-12                    | 月份，两位数                     |
| `MMM`  | Jan-Dec                  | 月份缩写名称              |
| `MMMM` | January-December         | 月份全称                     |
| `D`    | 1-31                     | 月份中的日期                    |
| `Do`   | 1st, 2nd, ..., 31st      | 月份中的日期，序数格式化 |
| `DD`   | 01-31                    | 月份中的日期，两位数          |
| `H`    | 0-23                     | 小时                                |
| `Ho`   | 0th, 1st, 2nd, ..., 23rd | 小时，序数格式化             |
| `HH`   | 00-23                    | 小时，两位数                      |
| `h`    | 1-12                     | 小时，12 小时制                 |
| `ho`   | 1st, 2nd, ..., 12th      | 小时，12 小时制，排序         |
| `hh`   | 01-12                    | 小时，12 小时制，两位数       |
| `m`    | 0-59                     | 分钟                              |
| `mo`   | 0th, 1st, ..., 59th      | 分钟，序数格式化           |
| `mm`   | 00-59                    | 分钟，两位数                    |
| `s`    | 0-59                     | 秒                              |
| `so`   | 0th, 1st, ..., 59th      | 秒，序数格式化           |
| `ss`   | 00-59                    | 秒，两位数                    |
| `SSS`  | 000-999                  | 毫秒，三位数               |
| `A`    | AM PM                    | 上下午                            |
| `AA`   | A.M. P.M.                | 上下午，周期                   |
| `a`    | am pm                    | 上下午，小写                 |
| `aa`   | a.m. p.m.                | 上下午，小写和周期     |
| `d`    | 0-6                      | 星期几，星期日为 0   |
| `dd`   | S-S                      | 星期几的最小名称     |
| `ddd`  | Sun-Sat                  | 星期几的短名称   |
| `dddd` | Sunday-Saturday          | 星期几的名称         |
| `z`    | GMT, GMT+1               | 带偏移量的时区                |
| `zz`   | GMT, GMT+1               | 带偏移量的时区                |
| `zzz`  | GMT, GMT+1               | 带偏移量的时区                |
| `zzzz` | GMT, GMT+01:00           | 带偏移量的长时区           |

- 上下午可以通过在 `options` 中定义 `customMeridiem` 来自定义。

## 用法

### 基本

```vue
<script setup lang="ts">
import { useDateFormat, useNow } from '@vueuse/core'

const formatted = useDateFormat(useNow(), 'YYYY-MM-DD HH:mm:ss')
</script>

<template>
  <div>{{ formatted }}</div>
</template>
```

### 使用区域设置

```vue
<script setup lang="ts">
import { useDateFormat, useNow } from '@vueuse/core'

const formatted = useDateFormat(useNow(), 'YYYY-MM-DD (ddd)', { locales: 'en-US' })
</script>

<template>
  <div>{{ formatted }}</div>
</template>
```

### 使用自定义上下午

```vue
<script setup lang="ts">
import { useDateFormat } from '@vueuse/core'

function customMeridiem(hours: number, minutes: number, isLowercase?: boolean, hasPeriod?: boolean) {
  const m = hours > 11 ? (isLowercase ? 'μμ' : 'ΜΜ') : (isLowercase ? 'πμ' : 'ΠΜ')
  return hasPeriod ? m.split('').reduce((acc, current) => acc += `${current}.`, '') : m
}

const am = useDateFormat('2022-01-01 05:05:05', 'hh:mm:ss A', { customMeridiem })
// am.value = '05:05:05 ΠΜ'
const pm = useDateFormat('2022-01-01 17:05:05', 'hh:mm:ss AA', { customMeridiem })
// pm.value = '05:05:05 Μ.Μ.'
</script>
```

## 类型声明

```ts
export type DateLike = Date | number | string | undefined
export interface UseDateFormatOptions {
  /**
   * 用于 dd/ddd/dddd/MMM/MMMM 格式的区域设置
   *
   * [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl#locales_argument).
   */
  locales?: MaybeRefOrGetter<Intl.LocalesArgument>
  /**
   * 自定义函数以重新修改上下午的显示方式
   *
   */
  customMeridiem?: (
    hours: number,
    minutes: number,
    isLowercase?: boolean,
    hasPeriod?: boolean,
  ) => string
}
export declare function formatDate(
  date: Date,
  formatStr: string,
  options?: UseDateFormatOptions,
): string
export declare function normalizeDate(date: DateLike): Date
export type UseDateFormatReturn = ComputedRef<string>
/**
 * 根据传入的令牌字符串获取格式化日期。
 *
 * @see https://vueuse.org/useDateFormat
 * @param date - 要格式化的日期，可以是 `Date` 对象、时间戳或字符串
 * @param formatStr - 用于格式化日期的令牌组合
 * @param options - UseDateFormatOptions
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useDateFormat(
  date: MaybeRefOrGetter<DateLike>,
  formatStr?: MaybeRefOrGetter<string>,
  options?: UseDateFormatOptions,
): UseDateFormatReturn
```
