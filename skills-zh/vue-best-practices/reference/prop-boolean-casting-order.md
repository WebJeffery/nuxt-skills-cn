---
title: Boolean Prop 类型顺序影响类型转换行为
impact: MEDIUM
impactDescription: 错误的类型顺序导致 boolean props 被解析为空字符串而不是 true
type: gotcha
tags: [vue3, props, boolean, type-casting, validation]
---

# Boolean Prop 类型顺序影响类型转换行为

**影响：中** - 当一个 prop 接受多种类型，包括 `Boolean` 和 `String` 时，类型数组中的顺序决定了仅属性语法（例如 `<Component disabled />`）如何被解析。不正确的顺序可能导致 `""`（空字符串）而不是 `true`。

Vue 应用特殊的 boolean 类型转换规则，但 String 出现在 Boolean 之前会禁用此转换。

## 任务清单

- [ ] 在需要 boolean 类型转换时，将 `Boolean` 放在类型数组中的 `String` 之前
- [ ] 测试仅属性语法（`<Component disabled />`）以验证预期行为
- [ ] 如果 prop 确实是 boolean，考虑仅使用 `Boolean` 类型
- [ ] 如果 String 和 Boolean 都是有意的，请记录预期用法

**错误：**
```vue
<script setup>
// 错误：String 在 Boolean 之前禁用 boolean 类型转换
defineProps({
  disabled: [String, Boolean]  // disabled="" 被解析为空字符串 ""
})
</script>

<!-- 在父模板中 -->
<MyComponent disabled />  <!-- props.disabled === "" (空字符串，不是 true!) -->
```

```vue
<script setup>
defineProps({
  // 有问题：顺序很重要，可能导致混淆
  loading: [String, Boolean],  // <Component loading /> 给出 ""
  active: [Boolean, String]    // <Component active /> 给出 true
})
</script>
```

**正确：**
```vue
<script setup>
// 正确：Boolean 在 String 之前启用 boolean 类型转换
defineProps({
  disabled: [Boolean, String]  // <Component disabled /> 被解析为 true
})
</script>

<!-- 所有这些都按预期工作 -->
<MyComponent disabled />           <!-- props.disabled === true -->
<MyComponent :disabled="true" />   <!-- props.disabled === true -->
<MyComponent :disabled="false" />  <!-- props.disabled === false -->
<MyComponent disabled="custom" />  <!-- props.disabled === "custom" -->
```

```vue
<script setup>
// 最佳：如果不需要 String 值，仅使用 Boolean
defineProps({
  disabled: Boolean  // 意图清晰，无歧义
})
</script>
```

## Boolean 类型转换规则

| Prop 声明 | 模板用法 | 结果值 |
|-----------------|----------------|-----------------|
| `Boolean` | `<C disabled />` | `true` |
| `Boolean` | `<C />` (不存在) | `false` |
| `[Boolean, String]` | `<C disabled />` | `true` |
| `[String, Boolean]` | `<C disabled />` | `""` (空字符串) |
| `[Boolean, Number]` | `<C disabled />` | `true` |
| `[Number, Boolean]` | `<C disabled />` | `true` |

注意：String 类型是特殊的 - 它是唯一在放在第一位时覆盖 Boolean 类型转换的类型。

## 何时使用多种类型

```vue
<script setup>
// 用例：Prop 可以是 boolean 切换 OR 字符串配置
defineProps({
  // 动画可以是 true/false OR 时间字符串，如 "fast"、"slow"
  animate: {
    type: [Boolean, String],  // Boolean 在前面以进行类型转换
    default: false
  }
})
</script>

<!-- 用法示例 -->
<Dialog animate />                <!-- true - 使用默认动画 -->
<Dialog :animate="false" />       <!-- false - 无动画 -->
<Dialog animate="slow" />         <!-- "slow" - 自定义时间 -->
```

## 参考
- [Vue.js Props - Boolean Casting](https://vuejs.org/guide/components/props.html#boolean-casting)
