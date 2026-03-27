---
name: "el-container"
description: "用于搭建基本页面结构容器组件包括页眉、侧边栏、主内容和页脚部分。当用户需要创建页面布局时调用---

# Element Plus 容器组件

用于搭建页面基本结构容器组件
## 何时调用

在以下情况下调用此技能
- 用户需要创建页面布局
- 用户想要实现页眉/页脚/侧边主内容部- 用户需要创建管理仪表板布局
- 用户询问响应式布局
- 用户需要嵌套容器布局

## 特
- **基于 Flex 布局**: 使用 CSS Flexbox 进行布局
- **自动方向**: 根据子元素自动确定方- **嵌套支持**: 支持多级嵌套
- **可自定义尺寸**: 可配置页眉高度、侧边栏宽度
- **响应*: 与响应式设计配合使用

## 组件

### Container (`el-container`)
包装容器。当`<el-header>` `<el-footer>` 嵌套所有子元素将垂直排列。否则水平排列
### Header (`el-header`)
页眉容器
### Aside (`el-aside`)
侧边部分容器(通常是侧边导
### Main (`el-main`)
主部分容器
### Footer (`el-footer`)
页脚容器
## API 参考
### Container 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| direction | 子元素布局方向 | `'horizontal' \| 'vertical'` | `el-header` `el-footer` 嵌套时为垂直;否则为水|

### Container 插槽

| Name | Description | Subtags |
|------|-------------|---------|
| default | 自定义默认内容| Container / Header / Aside / Main / Footer |

### Header 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| height | 页眉高度 | `string` | 60px |

### Header 插槽

| Name | Description |
|------|-------------|
| default | 自定义默认内容|

### Aside 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| width | 侧边部分宽度 | `string` | 300px |

### Aside 插槽

| Name | Description |
|------|-------------|
| default | 自定义默认内容|

### Main 插槽

| Name | Description |
|------|-------------|
| default | 自定义默认内容|

### Footer 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| height | 页脚高度 | `string` | 60px |

### Footer 插槽

| Name | Description |
|------|-------------|
| default | 自定义默认内容|

## 使用示例

### 基本布局(页眉 + 主内容

```vue
<template>
  <el-container>
    <el-header>Header</el-header>
    <el-main>Main</el-main>
  </el-container>
</template>

<style scoped>
.el-header,
.el-main {
  background-color: #b3c0d1;
  color: #333;
  text-align: center;
  line-height: 60px;
}

.el-main {
  background-color: #e9eef3;
  line-height: 160px;
}
</style>
```

### 页眉 + 主内容+ 页脚

```vue
<template>
  <el-container>
    <el-header>Header</el-header>
    <el-main>Main</el-main>
    <el-footer>Footer</el-footer>
  </el-container>
</template>
```

### 侧边+ 主内容
```vue
<template>
  <el-container>
    <el-aside width="200px">Aside</el-aside>
    <el-main>Main</el-main>
  </el-container>
</template>

<style scoped>
.el-aside {
  background-color: #d3dce6;
  color: #333;
  text-align: center;
  line-height: 200px;
}

.el-main {
  background-color: #e9eef3;
  color: #333;
  text-align: center;
  line-height: 160px;
}
</style>
```

### 页眉 + 侧边+ 主内容
```vue
<template>
  <el-container>
    <el-header>Header</el-header>
    <el-container>
      <el-aside width="200px">Aside</el-aside>
      <el-main>Main</el-main>
    </el-container>
  </el-container>
</template>
```

### 页眉 + 侧边+ 主内容+ 页脚

```vue
<template>
  <el-container>
    <el-header>Header</el-header>
    <el-container>
      <el-aside width="200px">Aside</el-aside>
      <el-container>
        <el-main>Main</el-main>
        <el-footer>Footer</el-footer>
      </el-container>
    </el-container>
  </el-container>
</template>
```

### 侧边+ 页眉 + 主内容
```vue
<template>
  <el-container>
    <el-aside width="200px">Aside</el-aside>
    <el-container>
      <el-header>Header</el-header>
      <el-main>Main</el-main>
    </el-container>
  </el-container>
</template>
```

### 侧边+ 页眉 + 主内容+ 页脚

```vue
<template>
  <el-container>
    <el-aside width="200px">Aside</el-aside>
    <el-container>
      <el-header>Header</el-header>
      <el-main>Main</el-main>
      <el-footer>Footer</el-footer>
    </el-container>
  </el-container>
</template>
```

### 完整仪表板布局

```vue
<template>
  <el-container style="height: 100vh">
    <el-aside width="200px">
      <el-menu
        :default-active="activeIndex"
        router
      >
        <el-menu-item index="/dashboard">
          <el-icon><HomeFilled /></el-icon>
          <span>Dashboard</span>
        </el-menu-item>
        <el-menu-item index="/users">
          <el-icon><User /></el-icon>
          <span>Users</span>
        </el-menu-item>
        <el-menu-item index="/settings">
          <el-icon><Setting /></el-icon>
          <span>Settings</span>
        </el-menu-item>
      </el-menu>
    </el-aside>
    
    <el-container>
      <el-header>
        <div class="header-content">
          <span>Admin Dashboard</span>
          <el-dropdown>
            <span class="user-info">
              <el-avatar :size="32" />
              <span>Admin</span>
            </span>
            <template #dropdown>
              <el-dropdown-menu>
                <el-dropdown-item>Profile</el-dropdown-item>
                <el-dropdown-item divided>Logout</el-dropdown-item>
              </el-dropdown-menu>
            </template>
          </el-dropdown>
        </div>
      </el-header>
      
      <el-main>
        <router-view />
      </el-main>
      
      <el-footer>
        © 2024 Your Company
      </el-footer>
    </el-container>
  </el-container>
</template>

<script setup>
import { ref } from 'vue'
import { HomeFilled, User, Setting } from '@element-plus/icons-vue'

const activeIndex = ref('/dashboard')
</script>

<style scoped>
.el-header {
  background-color: #fff;
  border-bottom: 1px solid #eee;
  display: flex;
  align-items: center;
}

.header-content {
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.el-aside {
  background-color: #304156;
}

.el-menu {
  border-right: none;
}

.el-footer {
  background-color: #f5f5f5;
  text-align: center;
}
</style>
```

## 常见问题

### 1. 容器未填充屏
在容器上设置高度:

```vue
<el-container style="height: 100vh">
  <!-- content -->
</el-container>
```

### 2. 侧边栏宽度未更改

使用 `width` 属性

```vue
<el-aside width="250px">Aside</el-aside>
```

### 3. 内容溢出

添加溢出样式:

```vue
<el-main style="overflow: auto">
  <!-- content -->
</el-main>
```

## 最佳实践
1. **使用语义化结*: 遵循页眉-主内容页脚模式
2. **设置容器高度**: 始终为整页布局设置高度
3. **响应式设*: 使用 CSS 媒体查询进行响应式布局
4. **嵌套容器**: 使用嵌套容器进行复杂布局
5. **与菜单结*: 将侧边栏与菜单结合用于导