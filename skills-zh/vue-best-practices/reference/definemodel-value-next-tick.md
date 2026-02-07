---
title: defineModel 值更改在下一个 tick 后生效
impact: MEDIUM
impactDescription: 设置后立即读取 model.value 返回旧值，而不是新值
type: gotcha
tags: [vue3, v-model, defineModel, reactivity, timing, nextTick]
---

# defineModel 值更改在下一个 tick 后生效

**影响：中等** - 当您为 `defineModel()` ref 分配新值时，更改不会立即生效。在赋值后立即读取 `model.value` 仍然返回之前的值。新值仅在 Vue 的下一个 tick 后可用。

当您需要在更改后立即使用更新后的值执行操作时，这可能会导致错误。

## 任务清单

- [ ] 不要在设置后立即读取 model.value 期望得到新值
- [ ] 直接使用您分配的值，而不是从 model 重新读取
- [ ] 如果必须在赋值后读取更新后的值，请使用 nextTick()
- [ ] 考虑将相关更新一起批处理

**错误 - 期望立即更新值：**
```vue
<script setup>
const model = defineModel<string>()

function updateAndLog() {
  model.value = 'new value'

  // 错误：这仍然记录旧值！
  console.log(model.value)  // 记录之前的值，而不是 'new value'

  // 错误：计算使用过时值
  const length = model.value.length  // 旧值的长度

  // 错误：对过时值的条件检查
  if (model.value === 'new value') {
    // 此块可能不会执行！
    doSomething()
  }
}
</script>
```

**正确 - 直接使用值：**
```vue
<script setup>
const model = defineModel<string>()

function updateAndLog() {
  const newValue = 'new value'
  model.value = newValue

  // 正确：使用您刚刚分配的值
  console.log(newValue)  // 记录 'new value'

  // 正确：从已知值计算
  const length = newValue.length

  // 正确：检查已知值
  if (newValue === 'new value') {
    doSomething()
  }
}
</script>
```

**替代方案 - 使用 nextTick 进行延迟操作：**
```vue
<script setup>
import { nextTick } from 'vue'

const model = defineModel<string>()

async function updateAndProcess() {
  model.value = 'new value'

  // 等待 Vue 应用更新
  await nextTick()

  // 现在 model.value 反映新值
  console.log(model.value)  // 'new value'
  processUpdatedValue(model.value)
}

// 或使用回调样式
function updateWithCallback() {
  model.value = 'new value'

  nextTick(() => {
    // 在这里安全地读取更新后的值
    console.log(model.value)  // 'new value'
  })
}
</script>
```

## 为什么会发生这种情况

`defineModel` 使用 Vue 的内部同步机制（`watchSyncEffect`）与父组件同步。当您赋值给 `model.value` 时：

1. 本地 ref 更新
2. 向父组件发出 `update:modelValue` 事件
3. 父组件更新其 ref
4. Vue 在下一个 tick 同步回子组件

在此周期内，子组件的本地值短暂地与已提交的值不同。

## 模式：需要立即访问的对象更新

```vue
<script setup>
import { nextTick } from 'vue'

const model = defineModel<{ name: string; validated: boolean }>()

async function validateAndUpdate(newName: string) {
  // 构建新对象
  const updated = {
    ...model.value,
    name: newName,
    validated: true
  }

  // 赋值给 model
  model.value = updated

  // 使用 'updated' 进行立即操作，而不是 model.value
  saveToServer(updated)  // 正确：使用本地引用

  // 如果您特别需要 model.value（例如，用于 DOM 同步）：
  await nextTick()
  focusValidatedField()  // 现在可以安全地假设 DOM 已更新
}
</script>
```

## Watch 回调也看到更新后的值

```vue
<script setup>
import { watch } from 'vue'

const model = defineModel<string>()

// Watch 回调接收新值
watch(model, (newValue, oldValue) => {
  // 'newValue' 在这里是可靠的
  console.log('从', oldValue, '更改为', newValue)
})

function update() {
  model.value = 'new value'
  // watch 回调将使用正确的 'new value' 触发
}
</script>
```

## 参考
- [Vue.js 响应式 - nextTick](https://vuejs.org/api/general.html#nexttick)
- [Vue.js 组件 v-model](https://vuejs.org/guide/components/v-model.html)
- [SIMPL Engineering: Vue defineModel Pitfalls](https://engineering.simpl.de/post/vue_definemodel/)
