# Element Plus 技能 - 智能体文档

本文档提供了 Element Plus 技能库中所有可用智能体（技能）的详细信息。

## 📋 智能体索引

| 类别 | 技能数量 | 描述 |
|----------|--------------|-------------|
| [基础组件](#基础组件智能体) | 14 | 基础交互的 UI 元素 |
| [表单组件](#表单组件智能体) | 24 | 表单输入和数据收集 |
| [数据展示](#数据展示智能体) | 17 | 数据可视化和展示 |
| [导航](#导航智能体) | 8 | 导航和路由组件 |
| [反馈](#反馈智能体) | 12 | 用户反馈和通知 |
| [布局](#布局智能体) | 4 | 页面布局和结构 |
| [工具](#工具智能体) | 5 | 工具和辅助组件 |
| [设计规范](#设计规范智能体) | 5 | 设计系统规范 |
| [基础](#基础智能体) | 6 | 核心设置和配置 |

---

## 基础组件智能体

### el-affix

**描述**：将元素固定到特定的可见区域，用于粘性导航。

**使用场景**：
- 粘性导航头部
- 滚动时的固定工具栏
- 持久操作按钮
- 浮动侧边栏

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| offset | number | 0 | 距离顶部/底部的偏移距离 |
| position | string | 'top' | 位置：'top' 或 'bottom' |
| target | string | - | 目标容器的 CSS 选择器 |
| z-index | number | 100 | 固定元素的 z-index |

**示例**：
```vue
<el-affix :offset="120">
  <el-button type="primary">固定按钮</el-button>
</el-affix>
```

---

### el-alert

**描述**：在页面上显示重要的警告消息。

**使用场景**：
- 系统通知
- 状态消息
- 警告显示
- 重要信息

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| title | string | - | 警告标题（必填） |
| type | string | 'info' | 类型：primary, success, warning, info, error |
| description | string | - | 描述文本 |
| closable | boolean | true | 是否可关闭警告 |
| show-icon | boolean | false | 是否显示类型图标 |

**示例**：
```vue
<el-alert title="成功" type="success" description="操作已完成" show-icon />
```

---

### el-anchor

**描述**：锚点导航，用于快速页面区块导航。

**使用场景**：
- 目录
- 文档导航
- 区块锚点
- 快速页面导航

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| container | string/HTMLElement | - | 滚动容器 |
| offset | number | 0 | 滚动偏移量 |
| direction | string | 'vertical' | 方向：'vertical' 或 'horizontal' |

**示例**：
```vue
<el-anchor>
  <el-anchor-link href="#section1" title="区块 1" />
  <el-anchor-link href="#section2" title="区块 2" />
</el-anchor>
```

---

### el-avatar

**描述**：使用图像、图标或字符显示用户头像。

**使用场景**：
- 用户头像图片
- 团队成员显示
- 实体表示
- 头像组

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| src | string | - | 图像源 URL |
| icon | string/Component | - | 图标组件 |
| size | number/string | - | 大小：数字或 'large'/'default'/'small' |
| shape | string | - | 形状：'circle' 或 'square' |

**示例**：
```vue
<el-avatar :size="50" src="avatar.jpg" />
<el-avatar :icon="UserFilled" />
```

---

### el-backtop

**描述**：返回顶部按钮，用于长页面导航。

**使用场景**：
- 长页面导航
- 快速滚动到顶部
- 改善用户体验

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| target | string | - | 触发滚动的目标 |
| visibility-height | number | 200 | 显示按钮的最小高度 |
| right | number | 40 | 右侧距离 |
| bottom | number | 40 | 底部距离 |

**示例**：
```vue
<el-backtop :bottom="100" />
```

---

### el-badge

**描述**：在按钮和图标上显示数字或状态标记。

**使用场景**：
- 通知计数
- 状态指示器
- 未读消息标记
- 关注标记

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| value | string/number | - | 显示值 |
| max | number | 99 | 最大值 |
| is-dot | boolean | false | 显示为圆点 |
| type | string | 'danger' | 标记类型 |

**示例**：
```vue
<el-badge :value="12">
  <el-button>评论</el-button>
</el-badge>
```

---

### el-breadcrumb

**描述**：显示页面位置，便于导航。

**使用场景**：
- 页面导航路径
- 类别层次结构
- 文档结构

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| separator | string | '/' | 分隔符 |
| separator-icon | string/Component | - | 分隔符图标 |

**示例**：
```vue
<el-breadcrumb separator="/">
  <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
  <el-breadcrumb-item>页面</el-breadcrumb-item>
</el-breadcrumb>
```

---

### el-button

**描述**：具有各种类型、大小和样式的基础按钮。

**使用场景**：
- 表单提交
- 操作触发器
- 导航控制
- 交互元素

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| type | string | 'default' | 类型：primary, success, warning, danger, info |
| size | string | - | 大小：large, default, small |
| plain | boolean | false | 朴素样式 |
| round | boolean | false | 圆角 |
| disabled | boolean | false | 禁用状态 |
| loading | boolean | false | 加载状态 |

**示例**：
```vue
<el-button type="primary" @click="handleClick">主要按钮</el-button>
<el-button type="success" plain>朴素成功</el-button>
```

---

### el-card

**描述**：将信息集成在卡片容器中。

**使用场景**：
- 仪表板卡片
- 内容区块
- 产品展示
- 信息分组

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| header | string | - | 卡片头部标题 |
| shadow | string | 'always' | 阴影：always, hover, never |
| body-style | object | - | 主体 CSS 样式 |

**示例**：
```vue
<el-card shadow="hover">
  <template #header>卡片标题</template>
  卡片内容
</el-card>
```

---

### el-carousel

**描述**：在有限空间内循环显示图像或文本。

**使用场景**：
- 图像轮播
- 内容轮播
- 旋转横幅
- 产品展示

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| height | string | - | 轮播高度 |
| autoplay | boolean | true | 自动循环 |
| interval | number | 3000 | 间隔时间（毫秒） |
| type | string | - | 类型：'card' |
| indicator-position | string | - | 指示器位置 |

**示例**：
```vue
<el-carousel height="200px">
  <el-carousel-item v-for="item in 4" :key="item">
    <h3>{{ item }}</h3>
  </el-carousel-item>
</el-carousel>
```

---

### el-collapse

**描述**：将内容存储在可展开的面板中。

**使用场景**：
- FAQ 区块
- 手风琴面板
- 可折叠内容区域

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | array | - | 激活的面板名称 |
| accordion | boolean | false | 手风琴模式 |

**示例**：
```vue
<el-collapse v-model="activeNames">
  <el-collapse-item title="面板 1" name="1">内容</el-collapse-item>
</el-collapse>
```

---

### el-divider

**描述**：在内容之间创建分隔线。

**使用场景**：
- 内容分隔
- 区块分隔符
- 视觉分组

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| direction | string | 'horizontal' | 方向：horizontal, vertical |
| border-style | string | 'solid' | 边框样式 |
| content-position | string | 'center' | 内容位置 |

**示例**：
```vue
<el-divider />
<el-divider content-position="left">左侧</el-divider>
```

---

### el-icon

**描述**：具有可自定义大小和颜色的 SVG 图标。

**使用场景**：
- UI 图标
- 按钮图标
- 状态指示器

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| size | number/string | - | 图标大小 |
| color | string | - | 图标颜色 |

**示例**：
```vue
<el-icon :size="30" color="#409EFC">
  <Edit />
</el-icon>
```

---

### el-link

**描述**：具有各种样式的文本超链接。

**使用场景**：
- 文本导航
- 外部链接
- 内联链接

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| type | string | 'default' | 类型：primary, success, warning, danger, info |
| href | string | - | 链接 URL |
| target | string | '_self' | 目标：_blank, _self 等 |
| underline | string | 'hover' | 下划线行为 |

**示例**：
```vue
<el-link type="primary" href="https://example.com" target="_blank">链接</el-link>
```

---

### el-text

**描述**：支持截断的样式化文本显示。

**使用场景**：
- 样式化文本显示
- 截断文本
- 语义化文本类型

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| type | string | - | 类型：primary, success, warning, danger, info |
| size | string | 'default' | 大小：large, default, small |
| truncated | boolean | false | 启用截断 |
| line-clamp | number | - | 最大行数 |

**示例**：
```vue
<el-text type="primary">主要文本</el-text>
<el-text truncated>将被截断的长文本...</el-text>
```

---

## 表单组件智能体

### el-autocomplete

**描述**：基于用户输入的输入建议。

**使用场景**：
- 搜索建议
- 表单自动完成
- 地址输入

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | string | - | 绑定值 |
| fetch-suggestions | function | - | 获取建议的方法 |
| placeholder | string | - | 占位符文本 |
| debounce | number | 300 | 防抖延迟（毫秒） |

**示例**：
```vue
<el-autocomplete
  v-model="state"
  :fetch-suggestions="querySearch"
  placeholder="搜索..."
/>
```

---

### el-cascader

**描述**：分层选项选择。

**使用场景**：
- 区域选择
- 类别层次结构
- 组织结构

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | array | - | 选定的值 |
| options | array | - | 数据选项 |
| props | object | - | 配置选项 |
| filterable | boolean | false | 启用搜索 |

**示例**：
```vue
<el-cascader v-model="value" :options="options" />
```

---

### el-checkbox

**描述**：多选选择。

**使用场景**：
- 多选
- 全选功能
- 切换状态

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | array | - | 选定的值 |
| value | any | - | 复选框值 |
| disabled | boolean | false | 禁用状态 |
| indeterminate | boolean | false | 半选状态 |

**示例**：
```vue
<el-checkbox-group v-model="checkList">
  <el-checkbox value="选项 A">选项 A</el-checkbox>
  <el-checkbox value="选项 B">选项 B</el-checkbox>
</el-checkbox-group>
```

---

### el-color-picker

**描述**：支持多种格式的颜色选择。

**使用场景**：
- 主题自定义
- 颜色选择
- 设计工具

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | string | - | 选定的颜色 |
| show-alpha | boolean | false | 显示透明度滑块 |
| predefine | array | - | 预定义颜色 |
| color-format | string | 'hex' | 颜色格式 |

**示例**：
```vue
<el-color-picker v-model="color" show-alpha />
```

---

### el-date-picker

**描述**：支持各种类型的日期选择。

**使用场景**：
- 日期输入
- 日期范围选择
- 日程安排

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | string/array | - | 选定的日期 |
| type | string | 'date' | 类型：date, daterange, datetime 等 |
| format | string | 'YYYY-MM-DD' | 显示格式 |
| value-format | string | - | 值格式 |
| shortcuts | array | - | 快速选择选项 |

**示例**：
```vue
<el-date-picker
  v-model="date"
  type="daterange"
  start-placeholder="开始日期"
  end-placeholder="结束日期"
/>
```

---

### el-form

**描述**：表单管理和验证。

**使用场景**：
- 数据收集
- 表单验证
- 表单提交

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| model | object | - | 表单数据模型 |
| rules | object | - | 验证规则 |
| label-width | string | - | 标签宽度 |
| label-position | string | 'right' | 标签位置 |

**示例**：
```vue
<el-form :model="form" :rules="rules" ref="formRef">
  <el-form-item label="姓名" prop="name">
    <el-input v-model="form.name" />
  </el-form-item>
</el-form>
```

---

### el-input

**描述**：具有各种配置的文本输入。

**使用场景**：
- 文本输入
- 密码输入
- 文本域

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | string | - | 绑定值 |
| type | string | 'text' | 类型：text, password, textarea |
| placeholder | string | - | 占位符文本 |
| disabled | boolean | false | 禁用状态 |
| clearable | boolean | false | 显示清除按钮 |
| maxlength | number | - | 最大长度 |

**示例**：
```vue
<el-input v-model="input" placeholder="输入文本" clearable />
<el-input type="textarea" v-model="textarea" :rows="4" />
```

---

### el-input-number

**描述**：具有递增控制的数字输入。

**使用场景**：
- 数量输入
- 数字设置
- 范围限制数字

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | number | 0 | 绑定值 |
| min | number | -Infinity | 最小值 |
| max | number | Infinity | 最大值 |
| step | number | 1 | 步长 |
| precision | number | 0 | 小数精度 |

**示例**：
```vue
<el-input-number v-model="num" :min="1" :max="10" />
```

---

### el-radio

**描述**：从选项中进行单选。

**使用场景**：
- 单选
- 单选按钮组
- 设置选择

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | any | - | 选定的值 |
| value | any | - | 单选按钮值 |
| disabled | boolean | false | 禁用状态 |
| border | boolean | false | 显示边框 |

**示例**：
```vue
<el-radio-group v-model="radio">
  <el-radio value="1">选项 A</el-radio>
  <el-radio value="2">选项 B</el-radio>
</el-radio-group>
```

---

### el-rate

**描述**：星级评分功能。

**使用场景**：
- 用户评分
- 产品评论
- 反馈评分

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | number | 0 | 评分值 |
| max | number | 5 | 最大星数 |
| allow-half | boolean | false | 允许半星 |
| show-text | boolean | false | 显示文本 |
| clearable | boolean | false | 可重置为 0 |

**示例**：
```vue
<el-rate v-model="rating" allow-half show-text />
```

---

### el-select

**描述**：下拉选择组件。

**使用场景**：
- 单选
- 多选
- 可搜索下拉菜单

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | any/array | - | 选定的值 |
| multiple | boolean | false | 多选 |
| filterable | boolean | false | 启用搜索 |
| clearable | boolean | false | 显示清除按钮 |
| placeholder | string | 'Select' | 占位符文本 |

**示例**：
```vue
<el-select v-model="value" placeholder="选择">
  <el-option label="选项 A" value="a" />
  <el-option label="选项 B" value="b" />
</el-select>
```

---

### el-slider

**描述**：支持拖动的数字范围选择。

**使用场景**：
- 音量控制
- 价格范围筛选
- 进度指示器

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | number/array | 0 | 滑块值 |
| min | number | 0 | 最小值 |
| max | number | 100 | 最大值 |
| step | number | 1 | 步长 |
| range | boolean | false | 范围模式 |
| show-input | boolean | false | 显示输入框 |

**示例**：
```vue
<el-slider v-model="value" :min="0" :max="100" />
<el-slider v-model="range" range :max="1000" />
```

---

### el-switch

**描述**：在两个对立状态之间切换。

**使用场景**：
- 启用/禁用功能
- 切换设置
- 开/关开关

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | boolean | false | 开关状态 |
| disabled | boolean | false | 禁用状态 |
| loading | boolean | false | 加载状态 |
| active-text | string | - | 开启时的文本 |
| inactive-text | string | - | 关闭时的文本 |

**示例**：
```vue
<el-switch v-model="value" active-text="开" inactive-text="关" />
```

---

### el-time-picker

**描述**：支持各种格式的时间选择。

**使用场景**：
- 时间输入
- 时间范围选择
- 日程安排

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | string/array | - | 选定的时间 |
| is-range | boolean | false | 范围模式 |
| format | string | 'HH:mm:ss' | 显示格式 |
| arrow-control | boolean | false | 使用箭头按钮 |

**示例**：
```vue
<el-time-picker v-model="time" placeholder="选择时间" />
```

---

### el-transfer

**描述**：双列列表选择。

**使用场景**：
- 权限管理
- 从大数据集中多选
- 项目分类

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | array | - | 选定的项目 |
| data | array | - | 源数据 |
| filterable | boolean | false | 启用搜索 |
| titles | array | - | 面板标题 |

**示例**：
```vue
<el-transfer v-model="value" :data="data" />
```

---

### el-tree-select

**描述**：基于树的下拉选择。

**使用场景**：
- 组织结构选择
- 类别树选择
- 分层数据选择

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | any | - | 选定的值 |
| data | array | - | 树数据 |
| multiple | boolean | false | 多选 |
| check-strictly | boolean | false | 父子不关联 |
| filterable | boolean | false | 启用搜索 |

**示例**：
```vue
<el-tree-select v-model="value" :data="treeData" />
```

---

### el-upload

**描述**：支持拖放的文件上传。

**使用场景**：
- 文件上传
- 图像上传
- 头像上传

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| action | string | - | 上传 URL |
| v-model:file-list | array | - | 文件列表 |
| multiple | boolean | false | 多个文件 |
| drag | boolean | false | 拖放 |
| accept | string | - | 接受的文件类型 |
| limit | number | - | 最大文件数 |

**示例**：
```vue
<el-upload action="/upload" :file-list="fileList" drag>
  <el-button type="primary">上传</el-button>
</el-upload>
```

---

## 数据展示智能体

### el-calendar

**描述**：显示事件的日期。

**使用场景**：
- 日历视图
- 事件日程安排
- 日期选择

**示例**：
```vue
<el-calendar v-model="date" />
```

---

### el-descriptions

**描述**：列表形式的多个字段。

**使用场景**：
- 产品详情
- 用户资料
- 信息摘要

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| border | boolean | false | 是否显示边框 |
| column | number | 3 | 列数 |
| size | string | - | 尺寸 |
| title | string | - | 标题文本 |
| extra | string | - | 额外内容 |

**示例**：
```vue
<el-descriptions title="用户信息" :column="3" border>
  <el-descriptions-item label="用户名">张三</el-descriptions-item>
  <el-descriptions-item label="手机号">13800138000</el-descriptions-item>
  <el-descriptions-item label="居住地">北京市</el-descriptions-item>
</el-descriptions>
```

---

### el-empty

**描述**：空状态占位符。

**使用场景**：
- 无数据状态
- 搜索无结果
- 空列表

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| image | string | - | 图片 URL |
| description | string | - | 描述文本 |
| image-size | number | - | 图片大小 |

**示例**：
```vue
<el-empty description="暂无数据" />
```

---

### el-image

**描述**：带预览的图像显示。

**使用场景**：
- 图片展示
- 图片预览
- 图片画廊

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| src | string | - | 图片源 |
| fit | string | - | 填充方式 |
| lazy | boolean | false | 懒加载 |
| preview-src-list | array | - | 预览图片列表 |

**示例**：
```vue
<el-image :src="url" :preview-src-list="srcList" />
```

---

### el-pagination

**描述**：分页组件。

**使用场景**：
- 数据分页
- 列表分页
- 表格分页

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model:current-page | number | 1 | 当前页 |
| page-size | number | 10 | 每页条数 |
| total | number | - | 总条数 |
| layout | string | - | 布局 |
| background | boolean | false | 背景色 |

**示例**：
```vue
<el-pagination v-model:current-page="currentPage" :page-size="20" :total="1000" />
```

---

### el-progress

**描述**：进度条显示。

**使用场景**：
- 加载进度
- 上传进度
- 任务进度

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| percentage | number | 0 | 百分比 |
| type | string | 'line' | 类型：line, circle, dashboard |
| stroke-width | number | - | 宽度 |
| status | string | - | 状态：success, exception, warning |

**示例**：
```vue
<el-progress :percentage="50" />
<el-progress type="circle" :percentage="80" />
```

---

### el-result

**描述**：结果页面。

**使用场景**：
- 操作成功
- 操作失败
- 网络错误

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| icon | string | - | 图标 |
| title | string | - | 标题 |
| sub-title | string | - | 副标题 |

**示例**：
```vue
<el-result icon="success" title="操作成功" sub-title="请继续下一步操作">
  <template #extra>
    <el-button type="primary" @click="goHome">返回首页</el-button>
  </template>
</el-result>
```

---

### el-skeleton

**描述**：加载骨架屏。

**使用场景**：
- 数据加载中
- 内容占位
- 提升用户体验

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| rows | number | - | 行数 |
| animated | boolean | false | 动画效果 |
| loading | boolean | true | 加载状态 |
| throttle | number | - | 延迟时间 |

**示例**：
```vue
<el-skeleton :rows="3" animated />
```

---

### el-table

**描述**：数据表格。

**使用场景**：
- 数据展示
- 数据列表
- 数据管理

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| data | array | - | 表格数据 |
| border | boolean | false | 边框 |
| stripe | boolean | false | 斑马纹 |
| height | string/number | - | 高度 |
| max-height | string/number | - | 最大高度 |

**示例**：
```vue
<el-table :data="tableData" border>
  <el-table-column prop="date" label="日期" />
  <el-table-column prop="name" label="姓名" />
</el-table>
```

---

### el-table-v2

**描述**：虚拟化表格，适用于大数据量。

**使用场景**：
- 大数据量展示
- 高性能表格
- 虚拟滚动

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| data | array | - | 表格数据 |
| columns | array | - | 列配置 |
| height | number | - | 固定高度 |
| width | number | - | 固定宽度 |

**示例**：
```vue
<el-table-v2 :columns="columns" :data="data" :height="400" />
```

---

### el-tag

**描述**：标签和标记。

**使用场景**：
- 分类标签
- 状态标记
- 属性标签

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| type | string | - | 类型：success, info, warning, danger |
| effect | string | 'light' | 主题：dark, light, plain |
| closable | boolean | false | 可关闭 |
| round | boolean | false | 圆角 |

**示例**：
```vue
<el-tag>标签一</el-tag>
<el-tag type="success">标签二</el-tag>
```

---

### el-timeline

**描述**：时间轴。

**使用场景**：
- 时间线展示
- 流程追踪
- 历史记录

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| reverse | boolean | false | 倒序 |
| placement | string | 'bottom' | 位置：top, bottom |

**示例**：
```vue
<el-timeline>
  <el-timeline-item timestamp="2024-01-01">事件一</el-timeline-item>
  <el-timeline-item timestamp="2024-01-02">事件二</el-timeline-item>
</el-timeline>
```

---

### el-tree

**描述**：树形控件。

**使用场景**：
- 文件目录
- 组织架构
- 分类树

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| data | array | - | 树数据 |
| props | object | - | 配置选项 |
| node-key | string | - | 唯一标识 |
| default-expand-all | boolean | false | 默认展开所有 |
| highlight-current | boolean | false | 高亮当前节点 |

**示例**：
```vue
<el-tree :data="data" :props="defaultProps" />
```

---

### el-tree-v2

**描述**：虚拟化树，适用于大数据量。

**使用场景**：
- 大数据量树
- 高性能树
- 虚拟滚动树

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| data | array | - | 树数据 |
| height | number | - | 固定高度 |
| item-size | number | 26 | 节点高度 |

**示例**：
```vue
<el-tree-v2 :data="data" :height="400" />
```

---

### el-statistic

**描述**：数值统计。

**使用场景**：
- 数据统计
- 数值展示
- 指标展示

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| value | number | - | 数值 |
| prefix | string | - | 前缀 |
| suffix | string | - | 后缀 |
| precision | number | - | 精度 |

**示例**：
```vue
<el-statistic title="活跃用户" :value="1128" />
```

---

### el-segmented

**描述**：分段控制器。

**使用场景**：
- 视图切换
- 选项切换
- 分组控制

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | any | - | 绑定值 |
| options | array | - | 选项数组 |
| block | boolean | false | 块级显示 |

**示例**：
```vue
<el-segmented v-model="current" :options="options" />
```

---

## 导航智能体

### el-dropdown

**描述**：下拉菜单。

**使用场景**：
- 菜单操作
- 更多选项
- 操作列表

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| trigger | string | 'hover' | 触发方式：hover, click |
| placement | string | 'bottom' | 位置 |
| hide-on-click | boolean | true | 点击后隐藏 |

**示例**：
```vue
<el-dropdown>
  <span class="el-dropdown-link">下拉菜单</span>
  <template #dropdown>
    <el-dropdown-menu>
      <el-dropdown-item>选项一</el-dropdown-item>
      <el-dropdown-item>选项二</el-dropdown-item>
    </el-dropdown-menu>
  </template>
</el-dropdown>
```

---

### el-menu

**描述**：导航菜单。

**使用场景**：
- 网站导航
- 侧边栏菜单
- 顶部菜单

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| default-active | string | - | 当前激活菜单 |
| mode | string | 'vertical' | 模式：vertical, horizontal |
| collapse | boolean | false | 折叠 |
| unique-opened | boolean | false | 只展开一个子菜单 |

**示例**：
```vue
<el-menu default-active="2">
  <el-menu-item index="1">处理中心</el-menu-item>
  <el-menu-item index="2">消息中心</el-menu-item>
</el-menu>
```

---

### el-page-header

**描述**：页面头部。

**使用场景**：
- 页面标题
- 返回导航
- 面包屑

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| title | string | - | 标题 |
| content | string | - | 内容 |
| icon | string/Component | - | 图标 |

**示例**：
```vue
<el-page-header title="页面标题" @back="goBack">
  <template #content>内容区域</template>
</el-page-header>
```

---

### el-steps

**描述**：步骤条。

**使用场景**：
- 流程步骤
- 向导引导
- 进度指示

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model:active | number | 0 | 当前步骤 |
| align-center | boolean | false | 居中对齐 |
| finish-status | string | 'success' | 完成状态 |
| process-status | string | 'process' | 进行状态 |

**示例**：
```vue
<el-steps :active="1" finish-status="success">
  <el-step title="步骤 1" />
  <el-step title="步骤 2" />
  <el-step title="步骤 3" />
</el-steps>
```

---

### el-tabs

**描述**：标签页。

**使用场景**：
- 内容切换
- 选项卡
- 多视图展示

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | string | - | 当前激活标签 |
| type | string | - | 类型：card, border-card |
| tab-position | string | 'top' | 位置：top, right, bottom, left |
| stretch | boolean | false | 标签宽度自动撑开 |

**示例**：
```vue
<el-tabs v-model="activeName">
  <el-tab-pane label="用户管理" name="first">用户管理</el-tab-pane>
  <el-tab-pane label="配置管理" name="second">配置管理</el-tab-pane>
</el-tabs>
```

---

## 反馈智能体

### el-dialog

**描述**：模态对话框。

**使用场景**：
- 确认操作
- 表单弹窗
- 信息展示

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | boolean | false | 是否显示 |
| title | string | - | 标题 |
| width | string | '50%' | 宽度 |
| fullscreen | boolean | false | 全屏 |
| top | string | - | 顶部距离 |
| modal | boolean | true | 是否需要遮罩层 |
| close-on-click-modal | boolean | true | 点击遮罩层关闭 |

**示例**：
```vue
<el-dialog v-model="dialogVisible" title="提示" width="30%">
  <span>这是一段信息</span>
  <template #footer>
    <el-button @click="dialogVisible = false">取消</el-button>
    <el-button type="primary" @click="dialogVisible = false">确定</el-button>
  </template>
</el-dialog>
```

---

### el-drawer

**描述**：抽屉面板。

**使用场景**：
- 侧边栏
- 详情展示
- 表单编辑

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | boolean | false | 是否显示 |
| title | string | - | 标题 |
| size | string | - | 尺寸 |
| direction | string | 'rtl' | 方向：rtl, ltr, ttb, btt |
| modal | boolean | true | 是否需要遮罩层 |

**示例**：
```vue
<el-drawer v-model="drawer" title="我是标题">
  <span>我来啦!</span>
</el-drawer>
```

---

### el-loading

**描述**：加载遮罩。

**使用场景**：
- 数据加载
- 异步操作
- 页面加载

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| fullscreen | boolean | false | 全屏加载 |
| lock | boolean | false | 锁定屏幕 |
| text | string | - | 加载文字 |
| spinner | string | - | 自定义加载图标 |
| background | string | - | 背景色 |

**示例**：
```vue
<el-loading :fullscreen="true" text="加载中..." />
```

---

### el-message

**描述**：消息提示。

**使用场景**：
- 操作反馈
- 提示信息
- 警告通知

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| message | string | - | 消息文字 |
| type | string | 'info' | 类型：success, warning, info, error |
| duration | number | 3000 | 显示时间 |
| show-close | boolean | false | 显示关闭按钮 |
| center | boolean | false | 文字居中 |

**示例**：
```vue
<el-button @click="open">打开消息提示</el-button>

<script>
const open = () => {
  ElMessage.success('这是一条成功消息')
}
</script>
```

---

### el-message-box

**描述**：消息框。

**使用场景**：
- 确认操作
- 警告提示
- 信息确认

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| title | string | - | 标题 |
| message | string | - | 消息内容 |
| type | string | - | 类型：success, warning, info, error |
| showCancelButton | boolean | false | 显示取消按钮 |
| confirmButtonText | string | '确定' | 确定按钮文字 |
| cancelButtonText | string | '取消' | 取消按钮文字 |

**示例**：
```vue
<el-button @click="openMsgBox">打开消息框</el-button>

<script>
const openMsgBox = () => {
  ElMessageBox.confirm('此操作将永久删除该文件, 是否继续?', '提示', {
    confirmButtonText: '确定',
    cancelButtonText: '取消',
    type: 'warning'
  })
}
</script>
```

---

### el-notification

**描述**：通知。

**使用场景**：
- 系统通知
- 消息提醒
- 重要通知

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| title | string | - | 标题 |
| message | string | - | 消息内容 |
| type | string | - | 类型：success, warning, info, error |
| position | string | 'top-right' | 位置 |
| duration | number | 4500 | 显示时间 |

**示例**：
```vue
<el-button @click="openNotify">打开通知</el-button>

<script>
const openNotify = () => {
  ElNotification({
    title: '成功',
    message: '这是一条成功的通知消息',
    type: 'success'
  })
}
</script>
```

---

### el-popconfirm

**描述**：气泡确认框。

**使用场景**：
- 删除确认
- 操作确认
- 二次确认

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| title | string | - | 标题 |
| confirm-button-text | string | '确定' | 确认按钮文字 |
| cancel-button-text | string | '取消' | 取消按钮文字 |
| icon | string/Component | - | 图标 |
| icon-color | string | - | 图标颜色 |

**示例**：
```vue
<el-popconfirm title="确定删除吗?" @confirm="delete">
  <el-button>删除</el-button>
</el-popconfirm>
```

---

### el-popover

**描述**：气泡卡片。

**使用场景**：
- 详细信息
- 操作菜单
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| content | string | - | 内容 |
| placement | string | 'bottom' | 位置 |
| trigger | string | 'click' | 触发方式：click, hover, focus, manual |
| width | string/number | - | 宽度 |

**示例**：
```vue
<el-popover content="这是一段内容" trigger="hover">
  <template #reference>
    <el-button>hover 激活</el-button>
  </template>
</el-popover>
```

---

### el-result

**描述**：结果页面。

**使用场景**：
- 操作成功
- 操作失败
- 网络错误

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| icon | string | - | 图标 |
| title | string | - | 标题 |
| sub-title | string | - | 副标题 |

**示例**：
```vue
<el-result icon="success" title="操作成功" sub-title="请继续下一步操作">
  <template #extra>
    <el-button type="primary" @click="goHome">返回首页</el-button>
  </template>
</el-result>
```

---

### el-tooltip

**描述**：文字提示。

**使用场景**：
- 提示信息
- 帮助说明
- 补充说明

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| content | string | - | 显示的内容 |
| placement | string | 'bottom' | 位置 |
| effect | string | 'dark' | 主题：dark, light |
| disabled | boolean | false | 是否禁用 |

**示例**：
```vue
<el-tooltip content="这是提示文字" placement="top">
  <el-button>上边</el-button>
</el-tooltip>
```

---

### el-tour

**描述**：产品导览。

**使用场景**：
- 新手引导
- 功能介绍
- 产品演示

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | number | 0 | 当前步骤 |
| target | string/HTMLElement | - | 目标元素 |
| title | string | - | 标题 |
| description | string | - | 描述 |
| placement | string | 'bottom' | 位置 |

**示例**：
```vue
<el-tour v-model="currentStep" :steps="steps" />
```

---

## 布局智能体

### el-container

**描述**：布局容器。

**使用场景**：
- 页面布局
- 整体结构
- 响应式布局

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| direction | string | - | 子元素排列方向：horizontal, vertical |

**示例**：
```vue
<el-container>
  <el-header>Header</el-header>
  <el-main>Main</el-main>
  <el-footer>Footer</el-footer>
</el-container>
```

---

### el-space

**描述**：间距组件。

**使用场景**：
- 元素间距
- 布局间距
- 对齐元素

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| size | string/number | 'small' | 间距大小 |
| direction | string | 'horizontal' | 方向：horizontal, vertical |
| wrap | boolean | false | 是否自动换行 |
| fill | boolean | false | 是否填充剩余空间 |

**示例**：
```vue
<el-space wrap>
  <el-button>按钮一</el-button>
  <el-button>按钮二</el-button>
  <el-button>按钮三</el-button>
</el-space>
```

---

### el-splitter

**描述**：分割面板。

**使用场景**：
- 可调整大小的面板
- 分屏显示
- 布局分割

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-model | number | - | 分割比例 |
| direction | string | 'horizontal' | 方向：horizontal, vertical |
| min-percent | number | 10 | 最小百分比 |
| max-percent | number | 90 | 最大百分比 |

**示例**：
```vue
<el-splitter v-model="splitterValue" direction="horizontal">
  <template #left>左侧内容</template>
  <template #right>右侧内容</template>
</el-splitter>
```

---

## 工具智能体

### el-config-provider

**描述**：全局配置。

**使用场景**：
- 全局配置
- 主题设置
- 国际化设置

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| locale | object | - | 语言包 |
| size | string | - | 组件尺寸 |
| button | object | - | 按钮配置 |
| namespace | string | - | 命名空间 |

**示例**：
```vue
<el-config-provider :locale="locale" :size="size">
  <app />
</el-config-provider>
```

---

### el-infinite-scroll

**描述**：无限滚动。

**使用场景**：
- 无限加载
- 滚动加载
- 列表加载

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| v-infinite-scroll | function | - | 加载方法 |
| infinite-scroll-disabled | boolean | false | 是否禁用 |
| infinite-scroll-distance | number | 0 | 触发距离 |

**示例**：
```vue
<ul v-infinite-scroll="load" infinite-scroll-disabled="disabled">
  <li v-for="i in count" :key="i">{{ i }}</li>
</ul>
```

---

### el-scrollbar

**描述**：自定义滚动条。

**使用场景**：
- 自定义滚动条
- 隐藏滚动条
- 美化滚动条

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| height | string/number | - | 高度 |
| max-height | string/number | - | 最大高度 |
| native | boolean | false | 使用原生滚动条 |
| wrap-style | object | - | 包裹层样式 |
| view-style | object | - | 视图层样式 |
| view-class | string | - | 视图层类名 |

**示例**：
```vue
<el-scrollbar height="400px">
  <p>内容...</p>
</el-scrollbar>
```

---

### el-watermark

**描述**：水印。

**使用场景**：
- 页面水印
- 图片水印
- 防止盗用

**参数**：
| 参数 | 类型 | 默认值 | 描述 |
|-----------|------|---------|-------------|
| content | string | - | 水印文字 |
| font | object | - | 字体配置 |
| gap | array | [100, 100] | 间距 |
| offset | array | - | 偏移量 |
| rotate | number | - | 旋转角度 |

**示例**：
```vue
<el-watermark content="Element Plus">
  <div>内容区域</div>
</el-watermark>
```

---

## 设计规范智能体

### element-plus-design-border

**描述**：边框样式、半径、阴影规范。

**使用场景**：
- 了解边框规范
- 应用一致的边框样式
- 使用圆角和阴影

**路径**：`./element-plus-design-border/SKILL.md`

---

### element-plus-design-color

**描述**：调色板和语义颜色规范。

**使用场景**：
- 了解颜色系统
- 正确应用品牌颜色
- 使用语义颜色

**路径**：`./element-plus-design-color/SKILL.md`

---

### element-plus-design-layout

**描述**：24 列网格系统规范。

**使用场景**：
- 创建响应式布局
- 构建页面结构
- 使用网格系统

**路径**：`./element-plus-design-layout/SKILL.md`

---

### element-plus-design-typography

**描述**：字体约定规范。

**使用场景**：
- 了解字体约定
- 应用一致的文本样式
- 设置 font-family

**路径**：`./element-plus-design-typography/SKILL.md`

---

### element-plus-design-overview

**描述**：设计系统概述。

**使用场景**：
- 了解设计系统
- 快速参考组件
- 探索组件库

**路径**：`./element-plus-design-overview/SKILL.md`

---

## 基础智能体

### element-plus-quickstart

**描述**：安装和设置指南。

**使用场景**：
- 安装 Element Plus
- 配置项目
- 快速开始

**路径**：`./element-plus-quickstart/SKILL.md`

---

### element-plus-theming

**描述**：主题定制指南。

**使用场景**：
- 自定义主题
- 修改颜色
- CSS 变量覆盖

**路径**：`./element-plus-theming/SKILL.md`

---

### element-plus-i18n

**描述**：国际化指南。

**使用场景**：
- 多语言支持
- 语言切换
- 本地化配置

**路径**：`./element-plus-i18n/SKILL.md`

---

### element-plus-dark-mode

**描述**：暗黑模式实现。

**使用场景**：
- 实现暗黑模式
- 主题切换
- 深色主题

**路径**：`./element-plus-dark-mode/SKILL.md`

---

### element-plus-ssr

**描述**：服务端渲染配置。

**使用场景**：
- SSR 配置
- 服务端渲染
- Nuxt.js 集成

**路径**：`./element-plus-ssr/SKILL.md`

---

### element-plus-components

**描述**：组件概述索引。

**使用场景**：
- 浏览所有组件
- 组件分类
- 快速查找

**路径**：`./element-plus-components/SKILL.md`

---

## 📚 相关资源

- [Element Plus 官方文档](https://element-plus.org/zh-CN/)
- [Vue 3 官方文档](https://cn.vuejs.org/)
- [Element Plus GitHub](https://github.com/element-plus/element-plus)
- [Element Plus 图标](https://element-plus.org/zh-CN/component/icon.html)
