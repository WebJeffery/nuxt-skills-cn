---
title: 将指令钩子参数视为只读
impact: MEDIUM
impactDescription: 修改指令参数会导致不可预测的行为并破坏 Vue 的内部状态
type: gotcha
tags: [vue3, directives, hooks, read-only, dataset]
---

# 将指令钩子参数视为只读

**影响：中等** - 除了 `el` 之外，您应该将所有指令钩子参数（`binding`、`vnode`、`prevVnode`）视为只读，并且永远不要修改它们。修改这些对象可能会导致不可预测的行为并干扰 Vue 的内部工作。

如果您需要在钩子之间共享信息，请使用元素的 `dataset` 属性或 WeakMap。

## 任务清单

- [ ] 永远不要修改 `binding`、`vnode` 或 `prevVnode` 参数
- [ ] 使用 `el.dataset` 在钩子之间共享原始数据
- [ ] 使用 WeakMap 存储需要在钩子之间持久化的复杂数据
- [ ] 仅直接修改 `el`（DOM 元素）

**错误示例：**
```javascript
// 错误：修改 binding 对象
const vBadDirective = {
  mounted(el, binding) {
    // 不要这样做 - 修改 binding
    binding.value = 'modified' // 错误！
    binding.customData = 'stored' // 错误！
    binding.modifiers.custom = true // 错误！
  },
  updated(el, binding) {
    // 这些修改可能会丢失或导致错误
    console.log(binding.customData) // undefined 或错误
  }
}

// 错误：修改 vnode
const vAnotherBadDirective = {
  mounted(el, binding, vnode) {
    // 不要这样做
    vnode.myData = 'stored' // 错误！
    vnode.props.modified = true // 错误！
  }
}
```

**正确示例：**
```javascript
// 正确：使用 el.dataset 存储简单数据
const vWithDataset = {
  mounted(el, binding) {
    // 在元素的 dataset 上存储数据
    el.dataset.originalValue = binding.value
    el.dataset.mountedAt = Date.now().toString()
  },
  updated(el, binding) {
    // 访问之前存储的数据
    console.log('原始值:', el.dataset.originalValue)
    console.log('当前值:', binding.value)
    console.log('挂载时间:', el.dataset.mountedAt)
  },
  unmounted(el) {
    // 如果需要，清理 dataset
    delete el.dataset.originalValue
    delete el.dataset.mountedAt
  }
}

// 正确：使用 WeakMap 存储复杂数据
const directiveState = new WeakMap()

const vWithWeakMap = {
  mounted(el, binding) {
    // 存储复杂状态
    directiveState.set(el, {
      originalValue: binding.value,
      config: binding.arg,
      mountedAt: Date.now(),
      callbacks: [],
      observers: []
    })
  },
  updated(el, binding) {
    const state = directiveState.get(el)
    if (state) {
      console.log('原始值:', state.originalValue)
      console.log('当前值:', binding.value)
      // 可以安全地修改状态对象
      state.updateCount = (state.updateCount || 0) + 1
    }
  },
  unmounted(el) {
    // WeakMap 在元素被垃圾回收时自动清理
    // 但显式清理对于观察器/监听器是好的做法
    const state = directiveState.get(el)
    if (state) {
      state.observers.forEach(obs => obs.disconnect())
      directiveState.delete(el)
    }
  }
}
```

## 使用元素属性

```javascript
// 正确：使用带下划线前缀约定的元素属性
const vTooltip = {
  mounted(el, binding) {
    // 使用下划线前缀存储在元素上以避免冲突
    el._tooltipInstance = createTooltip(el, binding.value)
    el._tooltipConfig = { ...binding.modifiers }
  },
  updated(el, binding) {
    // 访问和更新存储的实例
    if (el._tooltipInstance) {
      el._tooltipInstance.update(binding.value)
    }
  },
  unmounted(el) {
    // 清理
    if (el._tooltipInstance) {
      el._tooltipInstance.destroy()
      delete el._tooltipInstance
      delete el._tooltipConfig
    }
  }
}
```

## 您可以修改的内容

您被允许修改 `el`（DOM 元素）本身：

```javascript
const vHighlight = {
  mounted(el, binding) {
    // 正确：直接修改 el 是允许的
    el.style.backgroundColor = binding.value
    el.classList.add('highlighted')
    el.setAttribute('data-highlighted', 'true')
    el.textContent = '修改后的内容'
  },
  updated(el, binding) {
    // 正确：当 binding 更改时更新 el
    el.style.backgroundColor = binding.value
  }
}
```

## Binding 对象属性（只读引用）

`binding` 对象包含：
- `value` - 传递给指令的当前值（只读）
- `oldValue` - 之前的值（仅在 beforeUpdate/updated 中）（只读）
- `arg` - 传递的参数（例如 `v-dir:arg`）（只读）
- `modifiers` - 修饰符对象（例如 `v-dir.foo.bar`）（只读）
- `instance` - 组件实例（只读）
- `dir` - 指令定义对象（只读）

```javascript
const vExample = {
  mounted(el, binding) {
    // 读取这些属性，不要修改它们
    console.log(binding.value)      // 读取：可以
    console.log(binding.arg)        // 读取：可以
    console.log(binding.modifiers)  // 读取：可以
    console.log(binding.instance)   // 读取：可以

    // 存储您需要的内容以备后用
    el.dataset.directiveArg = binding.arg || ''
    el.dataset.hasModifierFoo = binding.modifiers.foo ? 'true' : 'false'
  }
}
```

## 参考
- [Vue.js 自定义指令 - 钩子参数](https://vuejs.org/guide/reusability/custom-directives#hook-arguments)
- [MDN - HTMLElement.dataset](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dataset)
