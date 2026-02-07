---
title: Props 在组件实例创建之前验证
impact: MEDIUM
impactDescription: data 和 computed 等实例属性在 prop default/validator 函数中不可用
type: gotcha
tags: [vue3, props, validation, lifecycle, default-values]
---

# Props 在组件实例创建之前验证

**影响：中** - Prop 验证和默认值函数在组件实例创建之前执行。这意味着 `this`、`data`、`computed`、注入和其他实例属性在 `default` 或 `validator` 函数中不可用。

这个时机捕获了期望在 prop 验证逻辑中使用组件状态的开发者。

## 任务清单

- [ ] 永远不要在 prop default/validator 函数中引用 `this` 或实例属性
- [ ] 对仅使用函数参数的对象/数组默认值使用工厂函数
- [ ] 对于依赖于其他 props 的验证，在验证器中使用第二个 `props` 参数
- [ ] 如果需要实例访问，将复杂验证逻辑移动到监视器或生命周期钩子

**错误：**
```vue
<script>
export default {
  data() {
    return {
      validOptions: ['a', 'b', 'c'],
      defaultMessage: '你好'
    }
  },
  props: {
    option: {
      type: String,
      // 错误：prop 验证期间 'this' 未定义
      validator(value) {
        return this.validOptions.includes(value)  // TypeError!
      }
    },
    message: {
      type: String,
      // 错误：无法访问 data 属性
      default() {
        return this.defaultMessage  // TypeError!
      }
    },
    config: {
      type: Object,
      // 错误：无法访问 computed 属性
      default() {
        return this.computedDefaults  // TypeError!
      }
    }
  }
}
</script>
```

**正确：**
```vue
<script>
// 在组件外部定义验证数据
const VALID_OPTIONS = ['a', 'b', 'c']
const DEFAULT_MESSAGE = '你好'

export default {
  props: {
    option: {
      type: String,
      // 正确：使用外部常量
      validator(value) {
        return VALID_OPTIONS.includes(value)
      }
    },
    message: {
      type: String,
      // 正确：使用外部常量或内联值
      default: DEFAULT_MESSAGE
    },
    config: {
      type: Object,
      // 正确：没有实例依赖的工厂函数
      default() {
        return { theme: 'light', size: 'medium' }
      }
    }
  }
}
</script>
```

## 在验证器中使用 `props` 参数

验证器接收完整的 props 对象作为第二个参数，用于跨 prop 验证：

```vue
<script setup>
const props = defineProps({
  min: {
    type: Number,
    default: 0
  },
  max: {
    type: Number,
    default: 100
  },
  value: {
    type: Number,
    required: true,
    // 正确：通过第二个参数访问其他 props
    validator(value, props) {
      return value >= props.min && value <= props.max
    }
  }
})
</script>
```

## 在默认函数中使用 rawProps

默认工厂函数接收 `rawProps` 以访问其他 prop 值：

```vue
<script setup>
defineProps({
  size: {
    type: String,
    default: 'medium'
  },
  padding: {
    type: Number,
    // 通过 rawProps 参数访问其他 prop 值
    default(rawProps) {
      // 使用 size 确定默认填充
      return rawProps.size === 'large' ? 20 : 10
    }
  }
})
</script>
```

## 挂载后验证模式

对于需要实例访问的验证，使用生命周期钩子：

```vue
<script setup>
import { onMounted, inject, warn } from 'vue'

const props = defineProps({
  theme: String
})

const availableThemes = inject('availableThemes', [])

// 需要注入值的验证
onMounted(() => {
  if (props.theme && !availableThemes.includes(props.theme)) {
    console.warn(`无效的主题 "${props.theme}"。可用: ${availableThemes.join(', ')}`)
  }
})
</script>
```

## 参考
- [Vue.js Props - Prop 验证](https://vuejs.org/guide/components/props.html#prop-validation)
