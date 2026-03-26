---
name: unocss-safelist-blocklist
description: 强制包含或排除特定工具类
---

# 安全列表和阻止列表

控制哪些工具类始终被包含或排除。

## 安全列表

始终包含的工具类，无论是否检测到：

```ts
export default defineConfig({
  safelist: [
    'p-1', 'p-2', 'p-3',
    // 动态生成
    ...Array.from({ length: 4 }, (_, i) => `p-${i + 1}`),
  ],
})
```

### 函数形式

```ts
safelist: [
  'p-1',
  () => ['m-1', 'm-2'],
  (context) => {
    const colors = Object.keys(context.theme.colors || {})
    return colors.map(c => `bg-${c}-500`)
  },
]
```

### 常见用例

```ts
safelist: [
  // 来自 CMS 的动态颜色
  () => ['primary', 'secondary'].flatMap(c => [
    `bg-${c}`, `text-${c}`, `border-${c}`,
  ]),
  
  // 组件变体
  () => {
    const variants = ['primary', 'danger']
    const sizes = ['sm', 'md', 'lg']
    return variants.flatMap(v => sizes.map(s => `btn-${v}-${s}`))
  },
]
```

## 阻止列表

从不生成的工具类：

```ts
blocklist: [
  'p-1',           // 精确匹配
  /^p-[2-4]$/,     // 正则表达式
]
```

### 带消息

```ts
blocklist: [
  ['bg-red-500', { message: '使用 bg-red-600 代替' }],
  [/^text-xs$/, { message: '使用 text-sm 以提高可访问性' }],
]
```

## 安全列表 vs 阻止列表

| 功能 | 安全列表 | 阻止列表 |
|---------|----------|-----------|
| 目的 | 始终包含 | 始终排除 |
| 字符串 | ✅ | ✅ |
| 正则表达式 | ❌ | ✅ |
| 函数 | ✅ | ❌ |

**注意：** 如果工具类同时在两者中，阻止列表优先。

## 最佳实践

优先使用静态映射而不是安全列表：

```ts
// 更好：UnoCSS 自动提取
const sizes = {
  sm: 'text-sm p-2',
  md: 'text-base p-4',
}

// 避免：大型安全列表
safelist: ['text-sm', 'text-base', 'p-2', 'p-4']
```

<!--
源参考：
- https://unocss.dev/config/safelist
- https://unocss.dev/guide/extracting
-->
