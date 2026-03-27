---
name: "el-select"
description: "用于单选多选下拉选择组件,支持筛选、远程搜索和分组。当用户需要下拉选择功能时调用---

# Element Plus Select 组件

当选项较多使用下拉菜单来显示和选择所需选项
## 何时调用

在以下情况下调用此技能
- 用户需要实现下拉选择
- 用户想要带标签多- 用户需要远程搜索功- 用户想要对选项进行分组
- 用户需要动态创建新选项
- 用户询问选择器筛
## 功能特性
- **单多*: 支持单选和多选模- **筛*: 本地远程筛- **分组**: 按类别分组选项
- **自定义模*: 自定义选项渲染
- **标签管理**: 折叠标签、自定义标签
- **远程搜索**: 从服务器加载选项
- **创建选项**: 允许创建新选项
- **虚拟滚动**: 大数据集高性能

## API 参考
### Select 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定| `string \| number \| boolean \| object \| array` | |
| multiple | 是否激活多| `boolean` | false |
| options | 选项数据 | `Array<{[key: string]: any}>` | |
| props | 配置选项 | `object` | |
| disabled | 是否禁用 Select | `boolean` | false |
| value-key | 值唯一标识键名 | `string` | value |
| size | Input 尺寸 | `'' \| 'large' \| 'default' \| 'small'` | |
| clearable | 是否可清| `boolean` | false |
| collapse-tags | 是否折叠标签 | `boolean` | false |
| collapse-tags-tooltip | 悬停时显示所有标签| `boolean` | false |
| multiple-limit | 多选模式下最大选项| `number` | 0 |
| placeholder | 占位| `string` | |
| filterable | 是否可筛| `boolean` | false |
| allow-create | 是否允许创建新项 | `boolean` | false |
| filter-method | 自定义筛选方| `(query: string) => void` | |
| remote | 是否从服务器加载选项 | `boolean` | false |
| debounce | 远程搜索时防抖延迟 | `number` | 300 |
| remote-method | 远程搜索方法 | `(query: string) => void` | |
| loading | 是否正在加载 | `boolean` | false |
| loading-text | 加载时显示文本 | `string` | |
| no-match-text | 无匹配数据时文| `string` | |
| no-data-text | 无选项时文本 | `string` | |
| popper-class | 下拉菜单自定义类名 | `string` | '' |
| reserve-keyword | 是否保留关键| `boolean` | true |
| default-first-option | 回车时选择第一个选项 | `boolean` | false |
| teleported | 是否将下拉菜单传送到 body | `boolean` | true |
| persistent | 下拉菜单是否持久 | `boolean` | true |
| automatic-dropdown | 获得焦点时自动显示下拉菜| `boolean` | false |
| fit-input-width | 下拉菜单宽度与输入框相同 | `boolean` | false |
| suffix-icon | 自定义后置图| `string \| Component` | ArrowDown |
| tag-type | 标签类型 | `'' \| 'success' \| 'info' \| 'warning' \| 'danger'` | info |
| max-collapse-tags | 显示最大标签数 | `number` | 1 |
| empty-values | 组件空| `array` | |
| value-on-clear | 清空时返回| `string \| number \| boolean \| Function` | |

### Select 事件

| Name | Description | Type |
|------|-------------|------|
| change | 当选中值改变时触发 | `(value: any) => void` |
| visible-change | 当下拉菜单出消失时触| `(visible: boolean) => void` |
| remove-tag | 当移除标签时触发 | `(tagValue: any) => void` |
| clear | 当点击清空图标时触发 | `() => void` |
| blur | Input 失去焦点时触| `(event: FocusEvent) => void` |
| focus | Input 获得焦点时触| `(event: FocusEvent) => void` |
| popup-scroll | 当下拉菜单滚动时触发 | `({scrollTop, scrollLeft}) => void` |

### Select 插槽

| Name | Description |
|------|-------------|
| default | 选项组件列表 |
| header | 下拉菜单顶部内容|
| footer | 下拉菜单底部内容|
| prefix | Select 前置内容|
| empty | 无选项时内容 |
| tag | 自定义标签内容|
| loading | 自定义加载内容|
| label | 自定义标签内容|

### Select 暴露

| Name | Description | Type |
|------|-------------|------|
| focus | Input 获得焦点 | `() => void` |
| blur | Input 失去焦点并隐藏下拉菜| `() => void` |
| selectedLabel | 获取选中标签| `ComputedRef<string \| string[]>` |

### Option 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| value | 选项| `string \| number \| boolean \| object` | |
| label | 选项标签| `string \| number` | |
| disabled | 是否禁用选项 | `boolean` | false |

### OptionGroup 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| label | 分组件名| `string` | |
| disabled | 是否禁用所有选项 | `boolean` | false |

## 使用示例

### 基本选择
```vue
<template>
  <el-select v-model="value" placeholder="Select">
    <el-option
      v-for="item in options"
      :key="item.value"
      :label="item.label"
      :value="item.value"
    />
  </el-select>
</template>

<script setup>
import { ref } from 'vue'

const value = ref('')
const options = [
  { value: 'option1', label: 'Option 1' },
  { value: 'option2', label: 'Option 2' },
  { value: 'option3', label: 'Option 3' }
]
</script>
```

### 使用 Options 属性
```vue
<template>
  <el-select v-model="value" :options="options" />
</template>

<script setup>
import { ref } from 'vue'

const value = ref('')
const options = [
  { value: 'option1', label: 'Option 1' },
  { value: 'option2', label: 'Option 2' }
]
</script>
```

### 禁用选择
```vue
<template>
  <el-select v-model="value" disabled placeholder="Disabled">
    <el-option label="Option 1" value="1" />
  </el-select>
</template>
```

### 可清空选择
```vue
<template>
  <el-select v-model="value" clearable placeholder="Clearable">
    <el-option label="Option 1" value="1" />
    <el-option label="Option 2" value="2" />
  </el-select>
</template>
```

### 多
```vue
<template>
  <el-select v-model="value" multiple placeholder="Select multiple">
    <el-option
      v-for="item in options"
      :key="item.value"
      :label="item.label"
      :value="item.value"
    />
  </el-select>
</template>

<script setup>
import { ref } from 'vue'

const value = ref([])
const options = [
  { value: '1', label: 'Option 1' },
  { value: '2', label: 'Option 2' },
  { value: '3', label: 'Option 3' }
]
</script>
```

### 折叠标签

```vue
<template>
  <el-select 
    v-model="value" 
    multiple 
    collapse-tags 
    collapse-tags-tooltip
    placeholder="Select"
  >
    <el-option
      v-for="item in options"
      :key="item.value"
      :label="item.label"
      :value="item.value"
    />
  </el-select>
</template>
```

### 自定义模
```vue
<template>
  <el-select v-model="value" placeholder="Select">
    <el-option
      v-for="item in options"
      :key="item.value"
      :label="item.label"
      :value="item.value"
    >
      <span style="float: left">{{ item.label }}</span>
      <span style="float: right; color: var(--el-text-color-secondary)">
        {{ item.value }}
      </span>
    </el-option>
  </el-select>
</template>
```

### 选项分组

```vue
<template>
  <el-select v-model="value" placeholder="Select">
    <el-option-group
      v-for="group in options"
      :key="group.label"
      :label="group.label"
    >
      <el-option
        v-for="item in group.options"
        :key="item.value"
        :label="item.label"
        :value="item.value"
      />
    </el-option-group>
  </el-select>
</template>

<script setup>
import { ref } from 'vue'

const value = ref('')
const options = [
  {
    label: 'Popular Cities',
    options: [
      { value: 'Shanghai', label: 'Shanghai' },
      { value: 'Beijing', label: 'Beijing' }
    ]
  },
  {
    label: 'City Name',
    options: [
      { value: 'Chengdu', label: 'Chengdu' },
      { value: 'Shenzhen', label: 'Shenzhen' }
    ]
  }
]
</script>
```

### 可筛选选择
```vue
<template>
  <el-select v-model="value" filterable placeholder="Select">
    <el-option
      v-for="item in options"
      :key="item.value"
      :label="item.label"
      :value="item.value"
    />
  </el-select>
</template>
```

### 远程搜索

```vue
<template>
  <el-select
    v-model="value"
    filterable
    remote
    reserve-keyword
    placeholder="Search"
    :remote-method="remoteMethod"
    :loading="loading"
  >
    <el-option
      v-for="item in options"
      :key="item.value"
      :label="item.label"
      :value="item.value"
    />
  </el-select>
</template>

<script setup>
import { ref } from 'vue'

const value = ref('')
const loading = ref(false)
const options = ref([])

const remoteMethod = async (query) => {
  if (query) {
    loading.value = true
    try {
      // 模拟 API 调用
      const results = await searchAPI(query)
      options.value = results
    } finally {
      loading.value = false
    }
  } else {
    options.value = []
  }
}
</script>
```

### 创建新选项

```vue
<template>
  <el-select
    v-model="value"
    filterable
    allow-create
    default-first-option
    placeholder="Select or create"
  >
    <el-option
      v-for="item in options"
      :key="item.value"
      :label="item.label"
      :value="item.value"
    />
  </el-select>
</template>
```

### 对象
```vue
<template>
  <el-select v-model="value" value-key="id" placeholder="Select">
    <el-option
      v-for="item in options"
      :key="item.id"
      :label="item.name"
      :value="item"
    />
  </el-select>
</template>

<script setup>
import { ref } from 'vue'

const value = ref(null)
const options = [
  { id: 1, name: 'Option 1' },
  { id: 2, name: 'Option 2' }
]
</script>
```

### 自定义页页脚

```vue
<template>
  <el-select v-model="value" placeholder="Select">
    <template #header>
      <el-input v-model="search" placeholder="Search..." />
    </template>
    <el-option label="Option 1" value="1" />
    <el-option label="Option 2" value="2" />
    <template #footer>
      <el-button type="primary" size="small">Add New</el-button>
    </template>
  </el-select>
</template>
```

## 常见问题

### 1. 行内表单中宽度折叠

设置特定宽度:

```vue
<el-form inline>
  <el-form-item>
    <el-select v-model="value" style="width: 200px" />
  </el-form-item>
</el-form>
```

### 2. 对象值不更新

使用 `value-key` 属性

```vue
<el-select v-model="value" value-key="id">
  <el-option :value="{ id: 1, name: 'Test' }" />
</el-select>
```

### 3. 清空值问
配置 `empty-values` `value-on-clear`:

```vue
<el-select 
  v-model="value" 
  :empty-values="[null, undefined]" 
  :value-on-clear="null"
>
```

## 组件交互

### 与表单验证配合
```vue
<template>
  <el-form :model="form" :rules="rules">
    <el-form-item label="Category" prop="category">
      <el-select v-model="form.category" placeholder="Select category">
        <el-option label="Category 1" value="1" />
        <el-option label="Category 2" value="2" />
      </el-select>
    </el-form-item>
  </el-form>
</template>
```

### 级联选择
```vue
<template>
  <el-select v-model="country" @change="handleCountryChange">
    <el-option label="USA" value="usa" />
    <el-option label="China" value="china" />
  </el-select>
  
  <el-select v-model="city" :disabled="!country">
    <el-option
      v-for="city in cities"
      :key="city.value"
      :label="city.label"
      :value="city.value"
    />
  </el-select>
</template>

<script setup>
import { ref, watch } from 'vue'

const country = ref('')
const city = ref('')
const cities = ref([])

const cityOptions = {
  usa: [{ label: 'New York', value: 'ny' }, { label: 'LA', value: 'la' }],
  china: [{ label: 'Beijing', value: 'bj' }, { label: 'Shanghai', value: 'sh' }]
}

watch(country, (val) => {
  city.value = ''
  cities.value = cityOptions[val] || []
})
</script>
```

## 最佳实践
1. **使用 options 属性*: 对于简单情使用 `options` 属性而不`el-option`
2. **v-for 添加 key**: 始终为选项添加唯一key
3. **为对象使value-key**: 当值为对象设置 `value-key`
4. **远程搜索防抖**: 对远程搜索使`debounce`
5. **更改时清*: 当父级更改时清空依赖选择6. **加载状态*: 在远程搜索期间显示加载状态