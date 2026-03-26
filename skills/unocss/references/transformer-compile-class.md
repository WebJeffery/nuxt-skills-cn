---
name: transformer-compile-class
description: 将多个类编译为一个哈希类
---

# Transformer Compile Class

将多个工具类编译为单个哈希类以减小 HTML 大小。

## 安装

```ts
import { defineConfig, transformerCompileClass } from 'unocss'

export default defineConfig({
  transformers: [
    transformerCompileClass(),
  ],
})
```

## 用法

添加 `:uno:` 前缀以标记要编译的类：

```html
<!-- 之前 -->
<div class=":uno: text-center sm:text-left">
  <div class=":uno: text-sm font-bold hover:text-red" />
</div>

<!-- 之后 -->
<div class="uno-qlmcrp">
  <div class="uno-0qw2gr" />
</div>
```

## 生成的 CSS

```css
.uno-qlmcrp {
  text-align: center;
}
.uno-0qw2gr {
  font-size: 0.875rem;
  line-height: 1.25rem;
  font-weight: 700;
}
.uno-0qw2gr:hover {
  --un-text-opacity: 1;
  color: rgb(248 113 113 / var(--un-text-opacity));
}
@media (min-width: 640px) {
  .uno-qlmcrp {
    text-align: left;
  }
}
```

## 选项

```ts
transformerCompileClass({
  // 自定义触发字符串（默认：':uno:'）
  trigger: ':uno:',
  
  // 自定义类前缀（默认：'uno-'）
  classPrefix: 'uno-',
  
  // 类名的哈希函数
  hashFn: (str) => /* 自定义哈希 */,
  
  // 保留原始类与编译类一起
  keepOriginal: false,
})
```

## 使用场景

- **更小的 HTML** - 减少重复的类字符串
- **混淆** - 在生产环境中隐藏工具类名称
- **性能** - 更少的类属性需要解析

## ESLint 集成

在整个项目中强制使用编译类：

```json
{
  "rules": {
    "@unocss/enforce-class-compile": "warn"
  }
}
```

此规则：
- 当类属性不以 `:uno:` 开头时发出警告
- 通过添加前缀自动修复

选项：

```json
{
  "rules": {
    "@unocss/enforce-class-compile": ["warn", {
      "prefix": ":uno:",
      "enableFix": true
    }]
  }
}
```

## 与其他转换器结合

```ts
export default defineConfig({
  transformers: [
    transformerVariantGroup(),  // 首先处理变体组
    transformerDirectives(),    // 然后是指令
    transformerCompileClass(),  // 最后编译
  ],
})
```

<!--
源参考：
- https://unocss.dev/transformers/compile-class
-->
