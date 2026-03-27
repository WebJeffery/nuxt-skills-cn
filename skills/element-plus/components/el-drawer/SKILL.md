---
name: "el-drawer"
description: "用于在滑动面板中显示内容抽屉组件。当用户需要创建侧边面板、滑出菜单表单抽屉时调用---

# Element Plus Drawer 组件

有时,Dialog 并不总能满足我们需求。Drawer Dialog 几乎具有相同API,但它引入了不同用户体验
## 何时调用

在以下情况下调用此技能
- 用户需要一个滑出面- 用户想要在抽屉中显示表单
- 用户需要一个侧边导航抽- 用户想要可调整大小抽屉
- 用户询问嵌套抽屉

## 功能特性
- **多种方向**: 左、右、上、下
- **可调整大*: 拖动调整抽屉大小
- **嵌套支持**: 多层抽屉
- **可定制大*: 可配置宽高度
- **有模无模*: 带不带遮罩- **自定义页页脚**: 可自定义部分

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 是否显示 Drawer | `boolean` | false |
| append-to-body | 控制 Drawer 是否插入DocumentBody | `boolean` | false |
| append-to | Drawer 附加到哪个元| `CSSSelector \| HTMLElement` | body |
| lock-scroll | 是否禁用 body 滚动 | `boolean` | true |
| before-close | 如果设置,关闭过程将停| `(done) => void` | |
| close-on-click-modal | 是否可以通过点击遮罩关闭 Drawer | `boolean` | true |
| close-on-press-escape | 是否可以通过ESC 关闭 Drawer | `boolean` | true |
| open-delay | 打开前时间(ms) | `number` | 0 |
| close-delay | 关闭前时间(ms) | `number` | 0 |
| destroy-on-close | 关闭后是否销毁子元素 | `boolean` | false |
| modal | 是否显示遮罩| `boolean` | true |
| modal-penetrable | 遮罩层是否可穿| `boolean` | false |
| direction | Drawer 打开方向 | `'rtl' \| 'ltr' \| 'ttb' \| 'btt'` | rtl |
| resizable | 启用可调整大小功| `boolean` | false |
| show-close | 是否显示关闭按钮 | `boolean` | true |
| size | Drawer 大宽度高 | `number \| string` | 30% |
| title | Drawer 标签| `string` | |
| with-header | 控制页眉部分是否存在标签| `boolean` | true |
| modal-class | 遮罩层额外类名 | `string` | |
| header-class | 页眉包装器自定义类型| `string` | |
| body-class | 主体包装器自定义类型| `string` | |
| footer-class | 页脚包装器自定义类型| `string` | |
| z-index | 设置 z-index | `number` | |
| header-aria-level | 页眉aria-level 属性| `string` | 2 |

### 事件

| Name | Description | Type |
|------|-------------|------|
| open | Drawer 打开动画开始前触发 | `() => void` |
| opened | Drawer 打开动画结束后触| `() => void` |
| close | Drawer 关闭动画开始前触发 | `() => void` |
| closed | Drawer 关闭动画结束后触| `() => void` |
| open-auto-focus | Drawer 打开并聚焦内容后触发 | `() => void` |
| close-auto-focus | Drawer 关闭并聚焦内容后触发 | `() => void` |
| resize-start | 开始调整大小时触发 | `(evt, size) => void` |
| resize | 调整大小时触| `(evt, size) => void` |
| resize-end | 调整大小结束时触| `(evt, size) => void` |

### 插槽

| Name | Description |
|------|-------------|
| default | Drawer 内容|
| header | Drawer 页眉部分 |
| footer | Drawer 页脚部分 |

### 暴露

| Name | Description |
|------|-------------|
| handleClose | 关闭 Drawer,调用 before-close |

## 使用示例

### 基本用法

```vue
<template>
  <el-button @click="drawer = true">Open Drawer</el-button>
  
  <el-drawer v-model="drawer" title="I am the title">
    <span>Hi, there!</span>
  </el-drawer>
</template>

<script setup>
import { ref } from 'vue'

const drawer = ref(false)
</script>
```

### 无标签
```vue
<template>
  <el-button @click="drawer = true">Open Drawer</el-button>
  
  <el-drawer v-model="drawer" :with-header="false">
    <span>Hi, there!</span>
  </el-drawer>
</template>

<script setup>
import { ref } from 'vue'

const drawer = ref(false)
</script>
```

### 自定义内容
```vue
<template>
  <el-button @click="drawer = true">Open Form Drawer</el-button>
  
  <el-drawer v-model="drawer" title="Edit Profile">
    <el-form :model="form" label-width="100px">
      <el-form-item label="Name">
        <el-input v-model="form.name" />
      </el-form-item>
      <el-form-item label="Email">
        <el-input v-model="form.email" />
      </el-form-item>
      <el-form-item label="Bio">
        <el-input v-model="form.bio" type="textarea" />
      </el-form-item>
    </el-form>
    <template #footer>
      <el-button @click="drawer = false">Cancel</el-button>
      <el-button type="primary" @click="saveForm">Save</el-button>
    </template>
  </el-drawer>
</template>

<script setup>
import { ref, reactive } from 'vue'

const drawer = ref(false)
const form = reactive({
  name: '',
  email: '',
  bio: ''
})

const saveForm = () => {
  console.log('Saving:', form)
  drawer.value = false
}
</script>
```

### 自定义页
```vue
<template>
  <el-drawer v-model="drawer">
    <template #header>
      <div class="custom-header">
        <h4>Custom Header</h4>
        <el-button type="primary" size="small" @click="drawer = false">
          Close
        </el-button>
      </div>
    </template>
    <span>Content</span>
  </el-drawer>
</template>

<script setup>
import { ref } from 'vue'

const drawer = ref(false)
</script>

<style scoped>
.custom-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
```

### 多种方向

```vue
<template>
  <el-button @click="drawer1 = true">From Right</el-button>
  <el-button @click="drawer2 = true">From Left</el-button>
  <el-button @click="drawer3 = true">From Top</el-button>
  <el-button @click="drawer4 = true">From Bottom</el-button>
  
  <el-drawer v-model="drawer1" direction="rtl" title="From Right" />
  <el-drawer v-model="drawer2" direction="ltr" title="From Left" />
  <el-drawer v-model="drawer3" direction="ttb" title="From Top" />
  <el-drawer v-model="drawer4" direction="btt" title="From Bottom" />
</template>

<script setup>
import { ref } from 'vue'

const drawer1 = ref(false)
const drawer2 = ref(false)
const drawer3 = ref(false)
const drawer4 = ref(false)
</script>
```

### 可调整大小 Drawer

```vue
<template>
  <el-button @click="drawer = true">Open Resizable Drawer</el-button>
  
  <el-drawer
    v-model="drawer"
    title="Resizable Drawer"
    resizable
    @resize="handleResize"
  >
    <span>Drag the edge to resize</span>
  </el-drawer>
</template>

<script setup>
import { ref } from 'vue'

const drawer = ref(false)

const handleResize = (evt, size) => {
  console.log('New size:', size)
}
</script>
```

### 嵌套 Drawer

```vue
<template>
  <el-button @click="outerDrawer = true">Open Outer Drawer</el-button>
  
  <el-drawer v-model="outerDrawer" title="Outer Drawer">
    <el-button @click="innerDrawer = true">Open Inner Drawer</el-button>
    
    <el-drawer
      v-model="innerDrawer"
      title="Inner Drawer"
      append-to-body
    >
      <span>This is the inner drawer</span>
    </el-drawer>
  </el-drawer>
</template>

<script setup>
import { ref } from 'vue'

const outerDrawer = ref(false)
const innerDrawer = ref(false)
</script>
```

### 关闭前确
```vue
<template>
  <el-button @click="drawer = true">Open Drawer</el-button>
  
  <el-drawer
    v-model="drawer"
    title="Drawer with Confirmation"
    :before-close="handleClose"
  >
    <span>Are you sure you want to close?</span>
  </el-drawer>
</template>

<script setup>
import { ref } from 'vue'
import { ElMessageBox } from 'element-plus'

const drawer = ref(false)

const handleClose = (done) => {
  ElMessageBox.confirm('Are you sure you want to close?')
    .then(() => {
      done()
    })
    .catch(() => {
      // Cancel close
    })
}
</script>
```

### 无模
```vue
<template>
  <el-button @click="drawer = true">Open No Modal Drawer</el-button>
  
  <el-drawer
    v-model="drawer"
    title="No Modal"
    :modal="false"
  >
    <span>You can interact with the page behind</span>
  </el-drawer>
</template>

<script setup>
import { ref } from 'vue'

const drawer = ref(false)
</script>
```

### 自定义大
```vue
<template>
  <el-button @click="drawer = true">Open 50% Drawer</el-button>
  
  <el-drawer
    v-model="drawer"
    title="Custom Size"
    size="50%"
  >
    <span>50% width drawer</span>
  </el-drawer>
</template>

<script setup>
import { ref } from 'vue'

const drawer = ref(false)
</script>
```

### 关闭时销
```vue
<template>
  <el-button @click="drawer = true">Open Drawer</el-button>
  
  <el-drawer
    v-model="drawer"
    title="Destroy on Close"
    destroy-on-close
  >
    <ChildComponent />
  </el-drawer>
</template>

<script setup>
import { ref } from 'vue'

const drawer = ref(false)
</script>
```

## 常见问题

### 1. Drawer 不关
检`before-close` 函数:

```vue
<script setup>
const handleClose = (done) => {
  // Make sure to call done() to close
  done()
}
</script>
```

### 2. 嵌套 Drawer 定位问题

对嵌套 Drawer 使用 `append-to-body`:

```vue
<el-drawer v-model="outer">
  <el-drawer v-model="inner" append-to-body />
</el-drawer>
```

### 3. 内容不更
使用 `destroy-on-close` 重新渲染内容:

```vue
<el-drawer v-model="drawer" destroy-on-close>
  <!-- Content will be destroyed and recreated -->
</el-drawer>
```

## 最佳实践
1. **使用 append-to-body**: 对于嵌套Drawer,始终使用 `append-to-body`
2. **关闭前确*: 使用 `before-close` 处理未保存更改
3. **适当大*: 根据内容设置大小
4. **关闭时销*: 使用 `destroy-on-close` 提高性能
5. **方向选择**: 根据用例选择方向
