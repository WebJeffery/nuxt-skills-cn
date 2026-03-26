---
name: preset-tagify
description: 将工具类用作 HTML 标签名称
---

# 预设 Tagify

将 CSS 工具类直接用作 HTML 标签名称。

## 安装

```ts
import { defineConfig, presetTagify } from 'unocss'

export default defineConfig({
  presets: [
    presetTagify(),
  ],
})
```

## 基本用法

代替：

```html
<span class="text-red">red text</span>
<div class="flex">flexbox</div>
<span class="i-line-md-emoji-grin"></span>
```

直接使用标签名称：

```html
<text-red>red text</text-red>
<flex>flexbox</flex>
<i-line-md-emoji-grin />
```

工作完全相同！

## 使用前缀

```ts
presetTagify({
  prefix: 'un-'
})
```

```html
<!-- 匹配 -->
<un-flex></un-flex>

<!-- 不匹配 -->
<flex></flex>
```

## 额外属性

将 CSS 属性添加到匹配的标签：

```ts
presetTagify({
  // 为图标添加 display: inline-block
  extraProperties: matched => matched.startsWith('i-')
    ? { display: 'inline-block' }
    : {},
})
```

或应用于所有：

```ts
presetTagify({
  extraProperties: { display: 'block' }
})
```

## 选项

```ts
presetTagify({
  // 标签前缀
  prefix: '',
  
  // 排除的标签（不会被处理）
  excludedTags: ['b', /^h\d+$/, 'table'],
  
  // 额外的 CSS 属性
  extraProperties: {},
  
  // 启用默认提取器
  defaultExtractor: true,
})
```

## 排除的标签

默认情况下，这些标签被排除：
- `b`（粗体）
- `h1` 到 `h6`（标题）
- `table`

添加您自己的：

```ts
presetTagify({
  excludedTags: [
    'b',
    /^h\d+$/,
    'table',
    'article',  // 添加自定义排除
    /^my-/,     // 排除以 'my-' 开头的标签
  ],
})
```

## 使用场景

- 快速原型设计
- 简单页面的更清晰的 HTML
- 图标嵌入：`<i-carbon-sun />`
- 语义化样式：`<flex-center>`、`<text-red>`

## 限制

- 自定义元素名称必须包含连字符（HTML 规范）
- 某些框架可能不支持所有自定义元素
- 没有连字符的工具类需要前缀选项

<!--
源参考：
- https://unocss.dev/presets/tagify
-->
