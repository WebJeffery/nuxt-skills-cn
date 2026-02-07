---
title: 在渲染函数中正确实现 v-model
impact: MEDIUM
impactDescription: 不正确的 v-model 实现会破坏与组件的双向绑定
type: best-practice
tags: [vue3, render-function, v-model, two-way-binding]
---

# 在渲染函数中正确实现 v-model

**影响：中** - 在渲染函数中对组件使用 `v-model` 时，你必须手动处理 `modelValue` prop 和 `update:modelValue` 事件。缺少任一部分都会破坏双向绑定。

在模板中，`v-model` 是语法糖，扩展为 `modelValue` prop 和 `update:modelValue` 事件监听器。在渲染函数中，你必须手动实现此扩展。

## 任务清单

- [ ] 将 `modelValue` 作为 prop 传递以获取绑定值
- [ ] 传递 `onUpdate:modelValue` 处理程序以接收更新
- [ ] 对于命名的 v-models，使用相应的 prop 和事件名称
- [ ] 在子组件中使用 emit 触发更新

**错误：**
```javascript
import { h } from 'vue'
import CustomInput from './CustomInput.vue'

export default {
  setup() {
    const text = ref('')

    return () => h(CustomInput, {
      // 错误：缺少更新处理程序
      modelValue: text.value
    })
  }
}
```

```javascript
import { h } from 'vue'
import CustomInput from './CustomInput.vue'

export default {
  setup() {
    const text = ref('')

    return () => h(CustomInput, {
      // 错误：错误的事件名称格式
      modelValue: text.value,
      onModelValueUpdate: (val) => text.value = val
    })
  }
}
```

**正确：**
```javascript
import { h, ref } from 'vue'
import CustomInput from './CustomInput.vue'

export default {
  setup() {
    const text = ref('')

    return () => h(CustomInput, {
      // 正确：modelValue prop + onUpdate:modelValue 处理程序
      modelValue: text.value,
      'onUpdate:modelValue': (value) => text.value = value
    })
  }
}
```

## 原生输入元素

对于原生输入，使用 `value` 和 `onInput`：

```javascript
import { h, ref } from 'vue'

export default {
  setup() {
    const text = ref('')

    return () => h('input', {
      value: text.value,
      onInput: (e) => text.value = e.target.value
    })
  }
}
```

## 命名的 v-model（多个 v-model）

```javascript
import { h, ref } from 'vue'
import UserForm from './UserForm.vue'

export default {
  setup() {
    const firstName = ref('')
    const lastName = ref('')

    return () => h(UserForm, {
      // v-model:firstName
      firstName: firstName.value,
      'onUpdate:firstName': (val) => firstName.value = val,

      // v-model:lastName
      lastName: lastName.value,
      'onUpdate:lastName': (val) => lastName.value = val
    })
  }
}
```

## 带修饰符的 v-model

在子组件中处理修饰符：

```javascript
import { h, ref } from 'vue'
import CustomInput from './CustomInput.vue'

// 父组件 - 传递修饰符
export default {
  setup() {
    const text = ref('')

    return () => h(CustomInput, {
      modelValue: text.value,
      'onUpdate:modelValue': (val) => text.value = val,
      modelModifiers: { trim: true, capitalize: true }
    })
  }
}

// 子组件 - 处理修饰符
export default {
  props: ['modelValue', 'modelModifiers'],
  emits: ['update:modelValue'],
  setup(props, { emit }) {
    const handleInput = (e) => {
      let value = e.target.value

      if (props.modelModifiers?.trim) {
        value = value.trim()
      }
      if (props.modelModifiers?.capitalize) {
        value = value.charAt(0).toUpperCase() + value.slice(1)
      }

      emit('update:modelValue', value)
    }

    return () => h('input', {
      value: props.modelValue,
      onInput: handleInput
    })
  }
}
```

## JSX 语法

```jsx
import { ref } from 'vue'
import CustomInput from './CustomInput.vue'

export default {
  setup() {
    const text = ref('')
    const count = ref(0)

    return () => (
      <div>
        {/* 自定义组件上的 v-model */}
        <CustomInput
          modelValue={text.value}
          onUpdate:modelValue={(val) => text.value = val}
        />

        {/* 原生输入上的 v-model */}
        <input
          value={text.value}
          onInput={(e) => text.value = e.target.value}
        />

        {/* 命名的 v-model */}
        <Counter
          count={count.value}
          onUpdate:count={(val) => count.value = val}
        />
      </div>
    )
  }
}
```

## 创建与 v-model 兼容的组件

```javascript
import { h } from 'vue'

export default {
  props: {
    modelValue: String
  },
  emits: ['update:modelValue'],
  setup(props, { emit }) {
    return () => h('input', {
      value: props.modelValue,
      onInput: (e) => emit('update:modelValue', e.target.value)
    })
  }
}
```

## 参考
- [Vue.js Render Functions - v-model](https://vuejs.org/guide/extras/render-function.html#v-model)
- [Vue.js Component v-model](https://vuejs.org/guide/components/v-model.html)
