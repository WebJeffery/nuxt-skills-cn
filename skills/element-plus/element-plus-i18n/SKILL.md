---
name: "element-plus-i18n"
description: "为 Element Plus 组件配置国际化和本地化。当用户需要更改语言、添加自定义翻译或配置语言环境设置时调用。"
---

# Element Plus 国际化

本技能提供了在 Element Plus 应用程序中配置国际化（i18n）全面指导。

## 何时调用

在以下情况下调用此技能：
- 用户想要更改默认语言
- 用户需要配置多种语言
- 用户询问日期/时间本地化
- 用户想要添加自定义翻译
- 用户需要动态切换语言

## 默认语言

Element Plus 组件默认使用**英语**。要使用其他语言，你需要配置语言环境。

## 配置方法

### 1. 全局配置

在注入 Element Plus 时配置语言环境：

```ts
import { createApp } from 'vue'
import ElementPlus from 'element-plus'
import zhCn from 'element-plus/es/locale/lang/zh-cn'
import App from './App.vue'

const app = createApp(App)
app.use(ElementPlus, {
  locale: zhCn,
})
```

### 2. ConfigProvider 组件

使用 `el-config-provider` 进行动态语言环境切换：

```vue
<template>
  <el-config-provider :locale="locale">
    <app />
  </el-config-provider>
</template>

<script setup>
import { ref } from 'vue'
import { ElConfigProvider } from 'element-plus'
import zhCn from 'element-plus/es/locale/lang/zh-cn'
import en from 'element-plus/es/locale/lang/en'

const locale = ref(zhCn)

const switchLanguage = (lang) => {
  locale.value = lang === 'zh' ? zhCn : en
}
</script>
```

### 3. CDN 使用

对于基于 CDN 项目：

```html
<script src="//unpkg.com/element-plus/dist/locale/zh-cn"></script>
<script>
  app.use(ElementPlus, {
    locale: ElementPlusLocaleZhCn,
  })
</script>
```

## 日期和时间本地化

Element Plus 在 `DatePicker` 等组件中使用 [Day.js](https://day.js.org/) 进行日期/时间管理。你必须单独导入 Day.js 语言环境：

```ts
import 'dayjs/locale/zh-cn'
```

### DatePicker 完整示例

```vue
<template>
  <el-config-provider :locale="locale">
    <el-date-picker
      v-model="date"
      type="date"
      placeholder="选择日期"
    />
  </el-config-provider>
</template>

<script setup>
import { ref } from 'vue'
import zhCn from 'element-plus/es/locale/lang/zh-cn'
import 'dayjs/locale/zh-cn'

const locale = ref(zhCn)
const date = ref('')
</script>
```

## 支持语言

Element Plus 支持 50 多种语言。以下是部分列表：

| 语言 | 代码 | 导入路径 |
|----------|------|-------------|
| 简体中文 | zh-cn | `element-plus/es/locale/lang/zh-cn` |
| 美式英语 | en | `element-plus/es/locale/lang/en` |
| 繁体中文 | zh-tw | `element-plus/es/locale/lang/zh-tw` |
| 香港中文 | zh-hk | `element-plus/es/locale/lang/zh-hk` |
| 日语 | ja | `element-plus/es/locale/lang/ja` |
| 韩语 | ko | `element-plus/es/locale/lang/ko` |
| 德语 | de | `element-plus/es/locale/lang/de` |
| 法语 | fr | `element-plus/es/locale/lang/fr` |
| 西班牙语 | es | `element-plus/es/locale/lang/es` |
| 葡萄牙语 | pt | `element-plus/es/locale/lang/pt` |
| 俄语 | ru | `element-plus/es/locale/lang/ru` |
| 阿拉伯语 | ar | `element-plus/es/locale/lang/ar` |
| 泰语 | th | `element-plus/es/locale/lang/th` |
| 越南语 | vi | `element-plus/es/locale/lang/vi` |

完整列表可在以下位置找到：[GitHub - Element Plus Locale](https://github.com/element-plus/element-plus/tree/dev/packages/locale/lang)

## 动态语言切换

### 完整示例

```vue
<template>
  <div>
    <el-select v-model="currentLang" @change="changeLanguage">
      <el-option label="简体中文" value="zh-cn" />
      <el-option label="English" value="en" />
      <el-option label="日本語" value="ja" />
    </el-select>

    <el-config-provider :locale="locale">
      <el-date-picker
        v-model="date"
        type="date"
        placeholder="选择日期"
      />
      <el-pagination
        :total="100"
        :page-size="10"
      />
    </el-config-provider>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'
import zhCn from 'element-plus/es/locale/lang/zh-cn'
import en from 'element-plus/es/locale/lang/en'
import ja from 'element-plus/es/locale/lang/ja'

const locales = {
  'zh-cn': zhCn,
  'en': en,
  'ja': ja
}

const currentLang = ref('zh-cn')
const locale = ref(zhCn)
const date = ref('')

const changeLanguage = (lang) => {
  locale.value = locales[lang]
  // 同时更改 Day.js 语言环境
  import(`dayjs/locale/${lang}`)
}
</script>
```

## 添加自定义语言

如果你语言不受支持，可以创建自定义语言环境文件：

```ts
// custom-locale.ts
export default {
  name: 'xx',
  el: {
    colorpicker: {
      confirm: '确认',
      clear: '清除',
    },
    datepicker: {
      now: '此刻',
      today: '今天',
      cancel: '取消',
      clear: '清除',
      confirm: '确定',
      selectDate: '选择日期',
      selectTime: '选择时间',
      startDate: '开始日期',
      startTime: '开始时间',
      endDate: '结束日期',
      endTime: '结束时间',
      prevYear: '前一年',
      nextYear: '后一年',
      prevMonth: '上个月',
      nextMonth: '下个月',
      year: '年',
      month1: '一月',
      month2: '二月',
      month3: '三月',
      month4: '四月',
      month5: '五月',
      month6: '六月',
      month7: '七月',
      month8: '八月',
      month9: '九月',
      month10: '十月',
      month11: '十一月',
      month12: '十二月',
      week: '周',
      weeks: {
        sun: '日',
        mon: '一',
        tue: '二',
        wed: '三',
        thu: '四',
        fri: '五',
        sat: '六',
      },
      months: {
        jan: '一月',
        feb: '二月',
        mar: '三月',
        apr: '四月',
        may: '五月',
        jun: '六月',
        jul: '七月',
        aug: '八月',
        sep: '九月',
        oct: '十月',
        nov: '十一月',
        dec: '十二月',
      },
    },
    select: {
      loading: '加载中',
      noMatch: '无匹配数据',
      noData: '无数据',
      placeholder: '请选择',
    },
    // ... 其他组件翻译
  },
}
```

然后使用它：

```ts
import customLocale from './custom-locale'

app.use(ElementPlus, {
  locale: customLocale,
})
```

## 最佳实践

### 1. 持久化语言偏好

将用户语言偏好存储在 localStorage 中：

```ts
const savedLang = localStorage.getItem('language') || 'en'
const locale = ref(locales[savedLang])

watch(locale, (newLocale) => {
  localStorage.setItem('language', newLocale.name)
})
```

### 2. 与应用程序 i18n 同步

如果使用 vue-i18n，请将 Element Plus 语言环境与应用程序语言环境同步：

```ts
import { createI18n } from 'vue-i18n'
import ElementPlus from 'element-plus'
import zhCn from 'element-plus/es/locale/lang/zh-cn'
import en from 'element-plus/es/locale/lang/en'

const i18n = createI18n({
  locale: 'zh',
  messages: {
    zh: { /* ... */ },
    en: { /* ... */ },
  },
})

const elementLocales = {
  zh: zhCn,
  en: en,
}

app.use(i18n)
app.use(ElementPlus, {
  locale: elementLocales[i18n.global.locale],
})
```

### 3. 懒加载语言环境

为了更好性能，懒加载语言环境：

```ts
const loadLocale = async (lang) => {
  const [dayjsLocale, elementLocale] = await Promise.all([
    import(`dayjs/locale/${lang}`),
    import(`element-plus/es/locale/lang/${lang}`),
  ])
  return elementLocale.default
}
```

## 输入参数

使用此技能时，提供：
- **目标语言**：你想要使用语言代码
- **配置方法**：全局 ConfigProvider
- **动态切换**：是否需要运行时语言切换
- **日期组件**：是否使用 DatePicker 其他日期组件

## 输出格式

此技能提供：
1. 语言环境配置示例
2. 语言切换实现
3. Day.js 集成用于日期组件
4. 自定义语言环境创建指南

## 使用限制

- 必须为日期组件单独导入 Day.js 语言环境
- 一些语言可能翻译不完整
- 自定义语言环境需要遵循 Element Plus 语言环境结构
- CDN 使用需要不同导入语法
