---
title: Props 是只读的 - 永远不要变异 Props
impact: HIGH
impactDescription: 变异 props 会破坏单向数据流并导致不可预测的父子状态同步问题
type: gotcha
tags: [vue3, props, one-way-data-flow, mutation, component-design]
---

# Props 是只读的 - 永远不要变异 Props

**影响：高** - Vue 中的 props 遵循单向数据流：仅从父到子。在子组件中变异 prop 违反了此约定，触发 Vue 警告并导致难以调试的状态同步问题。父组件失去了对传递的数据的控制。

Props 是渲染时来自父组件的"快照"。Vue 的响应式系统在父组件级别跟踪 props - 在子组件中变异不会通知父组件，导致状态不一致。

这对于对象/数组 props 尤其危险，因为 JavaScript 通过引用传递它们，允许在没有赋值的情况下进行变异。

## 任务清单

- [ ] 永远不要为 props 分配新值
- [ ] 永远不要直接变异对象或数组 prop 属性
- [ ] 使用 emit 请求父组件进行更改
- [ ] 如果需要修改基于 prop 的数据，请创建本地副本
- [ ] 对派生值使用计算属性

## 问题

**错误 - 直接原始 prop 变异：**
```vue
<script setup>
const props = defineProps({
  count: Number
})

// 错误：Vue 将警告关于变异 props
function increment() {
  props.count++ // 变异尝试 - 这将失败
}
</script>
```

**错误 - 对象/数组 prop 变异（静默但危险）：**
```vue
<script setup>
const props = defineProps({
  user: Object,
  items: Array
})

// 错误：没有警告，但破坏数据流！
function updateUser() {
  props.user.name = 'New Name' // 变异父组件的对象
}

function addItem() {
  props.items.push({ id:1 }) // 变异父组件的数组
}
</script>
```

这种模式是危险的，因为：
1. 父组件不知道更改
2. 数据可能变得不同步
3. 使调试困难 - 更改来自哪里？
4. 破坏组件约定

## 模式 1：向父组件发出事件

让父组件处理所有数据更改。

**正确：**
```vue
<!-- ChildComponent.vue -->
<script setup>
const props = defineProps({
  count: Number,
  user: Object
})

const emit = defineEmits(['update:count', 'update-user'])

function increment() {
  emit('update:count', props.count + 1)
}

function updateName(newName) {
  emit('update-user', { ...props.user, name: newName })
}
</script>
```

```vue
<!-- ParentComponent.vue -->
<template>
  <ChildComponent
    :count="count"
    :user="user"
    @update:count="count = $event"
    @update-user="user = $event"
  />
</template>
```

## 模式 2：可编辑数据的本地副本（Prop 作为初始值）

当组件需要使用 prop 数据的修改版本时。

**正确：**
```vue
<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  initialValue: String,
  user: Object
})

// 用于编辑的本地副本
const localValue = ref(props.initialValue)

// 对象的深拷贝
const localUser = ref({ ...props.user })

// 当父组件更改 prop 时同步
watch(() => props.initialValue, (newVal) => {
  localValue.value = newVal
})

watch(() => props.user, (newUser) => {
  localUser.value = { ...newUser }
}, { deep: true })
</script>

<template>
  <input v-model="localValue" />
  <input v-model="localUser.name" />
</template>
```

## 模式 3：用于转换的计算属性

当你需要 prop 的派生/转换版本时。

**正确：**
```vue
<script setup>
import { computed } from 'vue'

const props = defineProps({
  text: String,
  items: Array
})

// 派生值 - 不变异 prop
const uppercaseText = computed(() => props.text.toUpperCase())

// 过滤视图 - 不变异 prop
const activeItems = computed(() =>
  props.items.filter(item => item.active)
)
</script>
```

## 模式 4：用于双向绑定的 v-model

对于需要双向绑定的类似表单的组件。

**正确：**
```vue
<!-- CustomInput.vue -->
<script setup>
const props = defineProps({
  modelValue: String
})

const emit = defineEmits(['update:modelValue'])
</script>

<template>
  <input
    :value="modelValue"
    @input="emit('update:modelValue', $event.target.value)"
  />
</template>
```

```vue
<!-- ParentComponent.vue -->
<template>
  <!-- v-model 是 :modelValue + @update:modelValue 的简写 -->
  <CustomInput v-model="text" />
</template>
```

## 常见错误：认为对象变异是安全的

```vue
<script setup>
const props = defineProps({ config: Object })

// 这"有效"但是反模式！
props.config.theme = 'dark' // 没有 Vue 警告，但仍然是错误的
</script>
```

Vue 不警告，因为它无法有效地检测深度变异。但这仍然：
- 破坏单向数据流
- 使组件不可预测
- 导致维护噩梦

**始终将 props 视为它们被深度冻结。**

## 参考
- [Vue.js Props - 单向数据流](https://vuejs.org/guide/components/props.html#one-way-data-flow)
- [Vue.js Props - 变异对象/数组 Props](https://vuejs.org/guide/components/props.html#mutating-object-array-props)
