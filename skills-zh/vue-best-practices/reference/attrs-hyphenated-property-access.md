# 在 $attrs 中访问带连字符的属性

## 规则

透传属性在 JavaScript 中保留其原始大小写。带连字符的属性名称（如 `data-testid` 或 `aria-label`）必须使用括号表示法访问。事件监听器作为 camelCase 函数公开（例如，`@click` 变为 `$attrs.onClick`）。

## 为什么这很重要

- JavaScript 标识符不能包含连字符
- 对带连字符的名称使用点表示法会导致语法错误或未定义的值
- 事件监听器命名遵循与属性命名不同的约定
- 以编程方式处理 attrs 时"未定义"错误的常见来源

## 坏代码

```vue
<script setup>
import { useAttrs } from 'vue'

const attrs = useAttrs()

// 错误: 语法错误 - 连字符被解释为减号
console.log(attrs.data-testid)  // 错误！

// 错误: 这访问不同的属性
console.log(attrs.dataTestid)   // undefined（camelCase 对 attrs 不起作用）

// 错误: 期望带连字符的事件名称
console.log(attrs['on-click'])  // undefined
console.log(attrs['@click'])    // undefined
</script>
```

## 好代码

```vue
<script setup>
import { useAttrs } from 'vue'

const attrs = useAttrs()

// 正确: 对带连字符的属性使用括号表示法
console.log(attrs['data-testid'])    // "my-button"
console.log(attrs['aria-label'])     // "提交表单"
console.log(attrs['foo-bar'])        // "baz"

// 正确: 事件监听器使用带 'on' 前缀的 camelCase
console.log(attrs.onClick)           // function
console.log(attrs.onCustomEvent)     // function（来自 @custom-event）
console.log(attrs.onMouseEnter)      // function（来自 @mouseenter 或 @mouse-enter）
</script>
```

## 属性 vs 事件命名参考

| 父级用法 | $attrs 访问 |
|--------------|---------------|
| `class="foo"` | `attrs.class` |
| `data-id="123"` | `attrs['data-id']` |
| `aria-label="..."` | `attrs['aria-label']` |
| `foo-bar="baz"` | `attrs['foo-bar']` |
| `@click="fn"` | `attrs.onClick` |
| `@custom-event="fn"` | `attrs.onCustomEvent` |
| `@update:modelValue="fn"` | `attrs['onUpdate:modelValue']` |

## 常见模式

### 检查特定属性

```vue
<script setup>
import { useAttrs, computed } from 'vue'

const attrs = useAttrs()

// 检查 data 属性是否存在
const hasTestId = computed(() => 'data-testid' in attrs)

// 获取带有默认值的 aria 属性
const ariaLabel = computed(() => attrs['aria-label'] ?? '默认标签')
</script>
```

### 按类型过滤属性

```vue
<script setup>
import { useAttrs, computed } from 'vue'

const attrs = useAttrs()

// 将事件监听器与其他属性分开
const { listeners, otherAttrs } = computed(() => {
  const listeners = {}
  const otherAttrs = {}

  for (const [key, value] of Object.entries(attrs)) {
    if (key.startsWith('on') && typeof value === 'function') {
      listeners[key] = value
    } else {
      otherAttrs[key] = value
    }
  }

  return { listeners, otherAttrs }
}).value
</script>
```

### 提取 data 属性

```vue
<script setup>
import { useAttrs, computed } from 'vue'

const attrs = useAttrs()

// 获取所有 data-* 属性
const dataAttrs = computed(() => {
  const result = {}
  for (const [key, value] of Object.entries(attrs)) {
    if (key.startsWith('data-')) {
      result[key] = value
    }
  }
  return result
})
</script>

<template>
  <div v-bind="dataAttrs">
    <!-- 仅绑定 data 属性 -->
  </div>
</template>
```

### 转发特定事件

```vue
<script setup>
import { useAttrs } from 'vue'

defineOptions({
  inheritAttrs: false
})

const attrs = useAttrs()

// 使用自定义逻辑调用父级的点击处理程序
function handleClick(event) {
  console.log('首先内部处理')

  // 如果处理程序存在，则转发给父级
  if (attrs.onClick) {
    attrs.onClick(event)
  }
}
</script>

<template>
  <button @click="handleClick">
    <slot />
  </button>
</template>
```

## TypeScript 考虑

```vue
<script setup lang="ts">
import { useAttrs } from 'vue'

const attrs = useAttrs()

// attrs 类型为 Record<string, unknown>
// 您可能需要为特定用法进行类型转换

const testId = attrs['data-testid'] as string | undefined
const onClick = attrs.onClick as ((e: MouseEvent) => void) | undefined
</script>
```

## 参考

- [透传属性 - 在 JavaScript 中访问](https://vuejs.org/guide/components/attrs.html#accessing-fallthrough-attributes-in-javascript)
- [Vue 3 $attrs 文档](https://vuejs.org/api/component-instance.html#attrs)
