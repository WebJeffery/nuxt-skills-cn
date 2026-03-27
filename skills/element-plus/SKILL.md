---
name: element-plus
description: "Element Plus 技能库 - 一个综合性的技能库，用于 AI 智能体理解和利用 Element Plus UI 组件。当用户需要使用 Element Plus 组件、主题、国际化、暗黑模式或设计规范时调用。"
---

# Element Plus 技能库

Element Plus UI 框架的综合技能库，专为 AI 智能体理解和有效使用 Element Plus 组件而设计。

## 调用时机

在以下情况下调用此技能：
- 用户需要实现任何 Element Plus 组件
- 用户询问 Element Plus 配置或设置
- 用户想要自定义主题或或使用暗黑模式
- 用户需要国际化（i18n）支持
- 用户询问设计规范（颜色、边框、字体）
- 用户遇到 Element Plus 组件问题

## 技能库概述

此库包含 **88 个技能**，组织为以下类别：

| 类别 | 数量 | 描述 | 路径模式 |
|----------|-------|-------------|--------------|
| 组件技能 | 77 | 单个组件文档 | `./components/el-{name}/SKILL.md` |
| 设计规范 | 5 | 边框、颜色、布局、字体、概述 | `./element-plus-design-{name}/SKILL.md` |
| 基础技能 | 6 | 快速开始、主题、国际化、暗黑模式、SSR、组件 | `./element-plus-{name}/SKILL.md` |

## 如何定位技能

### 1. 组件技能（77 个技能）

所有组件技能遵循命名约定：`el-{component-name}`，位于 `components/` 目录中。

**路径模式：**
```
./components/el-{component-name}/SKILL.md
```

**示例：**
- Button: `./components/el-button/SKILL.md`
- Form: `./components/el-form/SKILL.md`
- Table: `./components/el-table/SKILL.md`
- Dialog: `./components/el-dialog/SKILL.md`

### 2. 设计规范技能（5 个技能）

设计技能提供 Element Plus 设计系统指导：

| 技能名称 | 路径 | 描述 |
|------------|------|-------------|
| Border | `./element-plus-design-border/SKILL.md` | 边框样式、半径、阴影 |
| Color | `./element-plus-design-color/SKILL.md` | 调色板和语义 |
| Layout | `./element-plus-design-layout/SKILL.md` | 24 列网格系统 |
| Typography | `./element-plus-design-typography/SKILL.md` | 字体约定 |
| Overview | `./element-plus-design-overview/SKILL.md` | 设计系统概述 |

### 3. 基础技能（6 个技能）

基础技能涵盖核心设置和配置：

| 技能名称 | 路径 | 描述 |
|------------|------|-------------|
| Quickstart | `./element-plus-quickstart/SKILL.md` | 安装和设置 |
| Theming | `./element-plus-theming/SKILL.md` | 主题定制 |
| i18n | `./element-plus-i18n/SKILL.md` | 国际化 |
| Dark Mode | `./element-plus-dark-mode/SKILL.md` | 暗黑模式实现 |
| SSR | `./element-plus-ssr/SKILL.md` | 服务端渲染 |
| Components | `./element-plus-components/SKILL.md` | 组件概述索引 |

## 技能调用指南

### 步骤 1：识别用户意图

分析用户请求以确定需要哪个技能类别：

| 用户请求模式 | 技能类别 | 示例技能 |
|---------------------|----------------|---------------|
| "创建一个按钮/表单/表格..." | 组件 | `el-button`、`el-form`、`el-table` |
| "如何设置 Element Plus" | 基础 | `element-plus-quickstart` |
| "自定义主题颜色" | 基础 | `element-plus-theming` |
| "添加多语言支持" | 基础 | `element-plus-i18n` |
| "实现暗黑模式" | 基础 | `element-plus-dark-mode` |
| "有哪些可用颜色？" | 设计 | `element-plus-design-color` |
| "网格系统如何工作？" | 设计 | `element-plus-design-layout` |

### 步骤 2：定位技能文件

使用上述路径模式定位适当技能文件：

```markdown
# 对于组件技能
./components/el-{component-name}/SKILL.md

# 对于设计技能
./element-plus-design-{name}/SKILL.md

# 对于基础技能
./element-plus-{name}/SKILL.md
```

### 步骤 3：读取并应用技能内容

每个技能文件包含：
- **调用时机**：使用技能特定条件
- **功能**：组件功能和选项
- **API 参考**：属性、事件、插槽、暴露
- **使用示例**：常见模式代码片段
- **最佳实践**：推荐实现指南

## 组件技能索引

### 基础组件（14个）
| 组件 | 技能路径 | 描述 |
|-----------|------------|-------------|
| Affix | `./components/el-affix/SKILL.md` | 粘性定位 |
| Alert | `./components/el-alert/SKILL.md` | 警告消息 |
| Anchor | `./components/el-anchor/SKILL.md` | 锚点导航 |
| Avatar | `./components/el-avatar/SKILL.md` | 用户头像 |
| Backtop | `./components/el-backtop/SKILL.md` | 返回顶部按钮 |
| Badge | `./components/el-badge/SKILL.md` | 徽章和标记 |
| Breadcrumb | `./components/el-breadcrumb/SKILL.md` | 面包屑导航 |
| Button | `./components/el-button/SKILL.md` | 按钮 |
| Card | `./components/el-card/SKILL.md` | 卡片容器 |
| Carousel | `./components/el-carousel/SKILL.md` | 图像轮播 |
| Collapse | `./components/el-collapse/SKILL.md` | 可折叠面板 |
| Divider | `./components/el-divider/SKILL.md` | 分隔符 |
| Icon | `./components/el-icon/SKILL.md` | SVG 图标 |
| Link | `./components/el-link/SKILL.md` | 文本链接 |
| Text | `./components/el-text/SKILL.md` | 样式化文本 |

### 表单组件（24个）
| 组件 | 技能路径 | 描述 |
|-----------|------------|-------------|
| Autocomplete | `./components/el-autocomplete/SKILL.md` | 带建议输入 |
| Cascader | `./components/el-cascader/SKILL.md` | 级联选择 |
| Checkbox | `./components/el-checkbox/SKILL.md` | 复选框 |
| ColorPicker | `./components/el-color-picker/SKILL.md` | 颜色选择器 |
| ColorPicker Panel | `./components/el-color-picker-panel/SKILL.md` | 颜色选择器面板 |
| DatePicker | `./components/el-date-picker/SKILL.md` | 日期选择器 |
| DatePicker Panel | `./components/el-date-picker-panel/SKILL.md` | 日期选择器面板 |
| DateTimePicker | `./components/el-datetime-picker/SKILL.md` | 日期时间选择器 |
| Form | `./components/el-form/SKILL.md` | 表单管理 |
| Input | `./components/el-input/SKILL.md` | 文本输入 |
| InputNumber | `./components/el-input-number/SKILL.md` | 数字输入 |
| InputTag | `./components/el-input-tag/SKILL.md` | 标签输入 |
| Mention | `./components/el-mention/SKILL.md` | 提及输入 |
| Radio | `./components/el-radio/SKILL.md` | 单选按钮 |
| Rate | `./components/el-rate/SKILL.md` | 星级评分 |
| Select | `./components/el-select/SKILL.md` | 下拉选择 |
| Select V2 | `./components/el-select-v2/SKILL.md` | 虚拟化选择 |
| Slider | `./components/el-slider/SKILL.md` | 滑块输入 |
| Switch | `./components/el-switch/SKILL.md` | 切换开关 |
| TimePicker | `./components/el-time-picker/SKILL.md` | 时间选择器 |
| TimeSelect | `./components/el-time-select/SKILL.md` | 时间选择 |
| Transfer | `./components/el-transfer/SKILL.md` | 穿梭框 |
| TreeSelect | `./components/el-tree-select/SKILL.md` | 树选择 |
| Upload | `./components/el-upload/SKILL.md` | 文件上传 |

### 数据展示组件（17个）
| 组件 | 技能路径 | 描述 |
|-----------|------------|-------------|
| Calendar | `./components/el-calendar/SKILL.md` | 日历视图 |
| Descriptions | `./components/el-descriptions/SKILL.md` | 描述列表 |
| Empty | `./components/el-empty/SKILL.md` | 空状态 |
| Image | `./components/el-image/SKILL.md` | 带预览图像 |
| Pagination | `./components/el-pagination/SKILL.md` | 分页 |
| Progress | `./components/el-progress/SKILL.md` | 进度条 |
| Result | `./components/el-result/SKILL.md` | 结果页 |
| Skeleton | `./components/el-skeleton/SKILL.md` | 加载骨架 |
| Table | `./components/el-table/SKILL.md` | 数据表格 |
| Table V2 | `./components/el-table-v2/SKILL.md` | 虚拟化表格 |
| Tag | `./components/el-tag/SKILL.md` | 标签 |
| Timeline | `./components/el-timeline/SKILL.md` | 时间轴 |
| Tree | `./components/el-tree/SKILL.md` | 树视图 |
| Tree V2 | `./components/el-tree-v2/SKILL.md` | 虚拟化树 |
| Statistic | `./components/el-statistic/SKILL.md` | 统计展示 |
| Segmented | `./components/el-segmented/SKILL.md` | 分段控制器 |

### 导航组件（8个）
| 组件 | 技能路径 | 描述 |
|-----------|------------|-------------|
| Dropdown | `./components/el-dropdown/SKILL.md` | 下拉菜单 |
| Menu | `./components/el-menu/SKILL.md` | 导航菜单 |
| PageHeader | `./components/el-page-header/SKILL.md` | 页面头部 |
| Steps | `./components/el-steps/SKILL.md` | 步骤条 |
| Tabs | `./components/el-tabs/SKILL.md` | 标签页 |

### 反馈组件（12个）
| 组件 | 技能路径 | 描述 |
|-----------|------------|-------------|
| Dialog | `./components/el-dialog/SKILL.md` | 模态对话框 |
| Drawer | `./components/el-drawer/SKILL.md` | 抽屉面板 |
| Loading | `./components/el-loading/SKILL.md` | 加载遮罩 |
| Message | `./components/el-message/SKILL.md` | 消息提示 |
| MessageBox | `./components/el-message-box/SKILL.md` | 消息框 |
| Notification | `./components/el-notification/SKILL.md` | 通知 |
| Popconfirm | `./components/el-popconfirm/SKILL.md` | 气泡确认框 |
| Popover | `./components/el-popover/SKILL.md` | 气泡卡片 |
| Tooltip | `./components/el-tooltip/SKILL.md` | 文字提示 |
| Tour | `./components/el-tour/SKILL.md` | 产品导览 |

### 布局组件（4个）
| 组件 | 技能路径 | 描述 |
|-----------|------------|-------------|
| Container | `./components/el-container/SKILL.md` | 布局容器 |
| Space | `./components/el-space/SKILL.md` | 间距 |
| Splitter | `./components/el-splitter/SKILL.md` | 分割面板 |

### 工具组件（5个）
| 组件 | 技能路径 | 描述 |
|-----------|------------|-------------|
| ConfigProvider | `./components/el-config-provider/SKILL.md` | 全局配置 |
| InfiniteScroll | `./components/el-infinite-scroll/SKILL.md` | 无限滚动 |
| Scrollbar | `./components/el-scrollbar/SKILL.md` | 自定义滚动条 |
| Watermark | `./components/el-watermark/SKILL.md` | 水印 |

## 使用示例

### 示例 1：用户询问按钮实现

**用户请求：** "如何创建一个带加载状态主要按钮？"

**智能体操作：**
1. 识别意图：组件技能 - Button
2. 定位技能：`./components/el-button/SKILL.md`
3. 读取技能内容
4. 提供包含 API 参考和代码示例响应

**响应：**
```vue
<template>
  <el-button type="primary" :loading="loading" @click="handleClick">
    {{ loading ? '加载中...' : '提交' }}
  </el-button>
</template>

<script setup>
import { ref } from 'vue'

const loading = ref(false)

const handleClick = async () => {
  loading.value = true
  try {
    await submitData()
  } finally {
    loading.value = false
  }
}
</script>
```

### 示例 2：用户询问表单验证

**用户请求：** "如何创建一个带验证表单？"

**智能体操作：**
1. 识别意图：组件技能 - Form
2. 定位技能：`./components/el-form/SKILL.md`
3. 读取技能内容
4. 提供包含验证示例响应

**响应：**
```vue
<template>
  <el-form :model="form" :rules="rules" ref="formRef">
    <el-form-item label="姓名" prop="name">
      <el-input v-model="form.name" />
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="submitForm">提交</el-button>
    </el-form-item>
  </el-form>
</template>

<script setup>
import { ref, reactive } from 'vue'

const formRef = ref()
const form = reactive({
  name: ''
})

const rules = {
  name: [
    { required: true, message: '请输入姓名', trigger: 'blur' }
  ]
}

const submitForm = async () => {
  await formRef.value.validate()
  // 提交逻辑
}
</script>
```

### 示例 3：用户询问主题定制

**用户请求：** "如何自定义 Element Plus 主题颜色？"

**智能体操作：**
1. 识别意图：基础技能 - Theming
2. 定位技能：`./element-plus-theming/SKILL.md`
3. 读取技能内容
4. 提供包含 CSS 变量示例响应

**响应：**
```css
:root {
  --el-color-primary: #409eff;
  --el-color-success: #67c23a;
  --el-color-warning: #e6a23c;
  --el-color-danger: #f56c6c;
  --el-color-info: #909399;
}
```

### 示例 4：用户询问暗黑模式

**用户请求：** "如何在 Element Plus 中实现暗黑模式？"

**智能体操作：**
1. 识别意图：基础技能 - Dark Mode
2. 定位技能：`./element-plus-dark-mode/SKILL.md`
3. 读取技能内容
4. 提供包含实现示例响应

**响应：**
```vue
<template>
  <el-config-provider :locale="locale" :size="size">
    <div :class="{ 'dark': isDark }">
      <App />
    </div>
  </el-config-provider>
</template>

<style>
.dark {
  color-scheme: dark;
}
</style>
```

## 配置要求

### 前置条件

使用此技能库之前，请确保：
1. **Vue 3.3+** 已安装
2. **Element Plus 2.4+** 已安装
3. **Node.js 18+** 用于开发

### 安装

```bash
npm install element-plus
npm install @element-plus/icons-vue
```

### TypeScript 支持

添加到 `tsconfig.json`：

```json
{
  "compilerOptions": {
    "types": ["element-plus/global"]
  }
}
```

## 输入参数

调用技能时，考虑这些参数：

| 参数 | 类型 | 描述 | 必需 |
|-----------|------|-------------|----------|
| component | string | 组件名称（例如，"button"、"form"） | 是 |
| feature | string | 所需特定功能（例如，"validation"、"loading"） | 否 |
| context | object | 额外上下文（框架、TypeScript 使用） | 否 |

## 输出格式

每个技能提供：

1. **API 参考**：完整属性、事件、插槽、暴露
2. **代码示例**：可用代码片段
3. **最佳实践**：推荐实现模式
4. **常见问题**：故障排除提示
5. **组件交互**：如何与其他组件一起使用

## 重要说明

### 1. 技能优先级

当多个技能可能适用时，按此顺序优先：
1. 组件特定技能（最具体）
2. 基础技能（配置）
3. 设计规范技能（指南）

### 2. 交叉引用

许多技能引用相关技能。始终检查：
- 同一类别中相关组件
- 配置基础技能
- 样式指南设计技能

### 3. 版本兼容性

此技能库基于 Element Plus 2.4+。某些功能在早期版本中可能不可用。

### 4. 命名约定

- 组件技能使用 `el-{name}` 格式（匹配 Vue 组件标签）
- 基础技能使用 `element-plus-{name}` 格式
- 设计技能使用 `element-plus-design-{name}` 格式

### 5. 文件结构

```
element-plus-skills/
├── SKILL.md                          # 此文件（主入口）
├── README.md                         # 英文文档
├── README_CN.md                      # 中文文档
├── AGENTS.md                         # 智能体文档
├── components/                       # 77 个组件技能
│   ├── el-button/
│   │   └── SKILL.md
│   ├── el-form/
│   │   └── SKILL.md
│   └── ...
├── element-plus-quickstart/          # 基础技能
├── element-plus-theming/
├── element-plus-i18n/
├── element-plus-dark-mode/
├── element-plus-ssr/
├── element-plus-components/
├── element-plus-design-border/       # 设计技能
├── element-plus-design-color/
├── element-plus-design-layout/
├── element-plus-design-typography/
└── element-plus-design-overview/
```

## 智能体最佳实践

1. **始终从此文件开始**，当用户提及 Element Plus 时
2. **根据用户意图定位特定技能**
3. **在响应之前读取完整技能文件**
4. **从技能文档中提供代码示例**
5. **在适用时引用相关技能**
6. **包含技能内容中最佳实践**
7. **如果与用户上下文相关，提及常见问题**

## 相关资源

- [Element Plus 文档](https://element-plus.org/)
- [Vue 3 文档](https://vuejs.org/)
- [Element Plus GitHub](https://github.com/element-plus/element-plus)
- [Element Plus 图标](https://element-plus.org/zh-CN/component/icon.html)
