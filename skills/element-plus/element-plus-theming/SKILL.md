---
name: "element-plus-theming"
description: "帮助使用 SCSS 变量和 CSS 变量自定义义 Element Plus 主题。当用户需要更改主题颜色、覆盖样式或创建自定义主题时调用。"
---

# Element Plus 主题定制

本技能提供了使用 SCSS 变量和 CSS 变量自定义义 Element Plus 主题全面指导。

## 何时调用

在以下情况下调用此技能：
- 用户想要更改主要主题颜色
- 用户需要自定义组件样式
- 用户询问 SCSS 变量覆盖
- 用户想要使用 CSS 变量进行主题定制
- 用户需要创建自定义主题

## 主题定制方法

Element Plus 提供了两种主要主题定制方法：

### 1. SCSS 变量覆盖

最适合大规模主题更改和构建时定制。

#### 可用变量

在以下位置查找所有 SCSS 变量：
- [`packages/theme-chalk/src/common/var.scss`](https://github.com/element-plus/element-plus/blob/dev/packages/theme-chalk/src/common/var.scss)

主要变量类别：
- `$colors`：调色板（primary、success、warning、danger、info）
- `$font-size`：字体大小
- `$border-radius`：边框半径值
- `$box-shadow`：阴影样式
- 组件特定变量（例如 `$button`、`$input`、`$table`）

#### 覆盖 SCSS 变量

创建自定义 SCSS 文件（例如 `styles/element/index.scss`）：

```scss
/* 覆盖你需要内容 */
@forward 'element-plus/theme-chalk/src/common/var.scss' with (
  $colors: (
    'primary': (
      'base': green,
    ),
  )
);

/* 如果需要，导入所有样式 */
/* @use "element-plus/theme-chalk/src/index.scss" as *; */
```

**重要提示**：使用 `@use` 而不是 `@import`（Sass 团队最终将移除 `@import`）

#### 导入自定义主题

```ts
import { createApp } from 'vue'
import './styles/element/index.scss'
import ElementPlus from 'element-plus'
import App from './App.vue'

const app = createApp(App)
app.use(ElementPlus)
```

#### 按需导入：Vite 配置

```ts
import path from 'path'
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import ElementPlus from 'unplugin-element-plus/vite'

export default defineConfig({
  resolve: {
    alias: {
      '~/': `${path.resolve(__dirname, 'src')}/`,
    },
  },
  css: {
    preprocessorOptions: {
      scss: {
        additionalData: `@use "~/styles/element/index.scss" as *;`,
      },
    },
  },
  plugins: [
    vue(),
    ElementPlus({
      useSource: true,
    }),
  ],
})
```

#### Webpack 配置

```js
import ElementPlus from 'unplugin-element-plus/webpack'

export default defineConfig({
  css: {
    loaderOptions: {
      scss: {
        additionalData: `@use "~/styles/element/index.scss" as *;`,
      },
    },
  },
  plugins: [
    ElementPlus({
      useSource: true,
    }),
  ],
})
```

### 2. CSS 变量覆盖

最适合运行时主题切换和动态定制。

#### 全局 CSS 变量

在 `:root` 自定义类中覆盖变量：

```css
:root {
  --el-color-primary: green;
  --el-color-primary-light-3: #79bbff;
  --el-color-primary-light-5: #a0cfff;
  --el-color-primary-light-7: #c6e2ff;
  --el-color-primary-light-8: #d9ecff;
  --el-color-primary-light-9: #ecf5ff;
  --el-color-primary-dark-2: #337ecc;
}
```

#### 组件特定变量

自定义单个组件：

```html
<el-tag style="--el-tag-bg-color: red">标签</el-tag>
```

者使用类以获得更好性能：

```css
.custom-tag {
  --el-tag-bg-color: red;
}
```

```html
<el-tag class="custom-tag">标签</el-tag>
```

#### 动态 CSS 变量控制

```ts
// 获取元素
const el = document.documentElement
// 者使用特定元素
// const el = document.getElementById('xxx')

// 获取 CSS 变量
const primaryColor = getComputedStyle(el).getPropertyValue('--el-color-primary')

// 设置 CSS 变量
el.style.setProperty('--el-color-primary', 'red')
```

#### 使用 VueUse

为了更优雅方法，使用 [useCssVar](https://vueuse.org/core/usecssvar/)：

```ts
import { useCssVar } from '@vueuse/core'

const el = ref<HTMLElement | null>(null)
const color = useCssVar('--el-color-primary', el)

// 更改颜色
color.value = 'green'
```

## 颜色系统

### 主要颜色

```scss
$colors: (
  'primary': (
    'base': #409eff,
  ),
  'success': (
    'base': #67c23a,
  ),
  'warning': (
    'base': #e6a23c,
  ),
  'danger': (
    'base': #f56c6c,
  ),
  'error': (
    'base': #f56c6c,
  ),
  'info': (
    'base': #909399,
  ),
);
```

### CSS 变量命名

Element Plus 自动从 SCSS 生成 CSS 变量：

| SCSS 变量 | CSS 变量 |
|---------------|--------------|
| `$colors.primary.base` | `--el-color-primary` |
| `$colors.primary.light-3` | `--el-color-primary-light-3` |
| `$colors.primary.light-5` | `--el-color-primary-light-5` |
| `$colors.primary.light-7` | `--el-color-primary-light-7` |
| `$colors.primary.light-8` | `--el-color-primary-light-8` |
| `$colors.primary.light-9` | `--el-color-primary-light-9` |
| `$colors.primary.dark-2` | `--el-color-primary-dark-2` |

## 最佳实践

### 1. 分离 SCSS 文件

将自定义变量与 Element Plus 变量分开：

```
styles/
├── element/
│   └── index.scss    # Element Plus 变量覆盖
└── custom/
    └── variables.scss # 你自定义变量
```

这可以防止因重新编译大型 SCSS 文件而导致热更新缓慢。

### 2. 导入顺序

在 Element Plus 样式之前导入自定义主题文件：

```ts
import './styles/element/index.scss'  // 自定义主题在前
import ElementPlus from 'element-plus'
```

### 3. 使用 CSS 变量进行运行时更改

对于运行时主题切换，优先使用 CSS 变量而不是 SCSS：

```vue
<script setup>
import { ref } from 'vue'

const isDark = ref(false)

const toggleTheme = () => {
  isDark.value = !isDark.value
  document.documentElement.style.setProperty(
    '--el-color-primary',
    isDark.value ? '#409eff' : '#67c23a'
  )
}
</script>
```

### 4. 性能优化

为了更好性能，使用类而不是 `:root`：

```css
/* 更好性能 */
.custom-theme {
  --el-color-primary: green;
}
```

## 示例项目

- 完整导入：[element-plus-vite-starter](https://github.com/element-plus/element-plus-vite-starter)
- 按需导入：[unplugin-element-plus/examples/vite](https://github.com/element-plus/unplugin-element-plus)

## 输入参数

使用此技能时，提供：
- **主题方法**：SCSS 变量 CSS 变量
- **构建工具**：Vite、Webpack 其他
- **导入方法**：完整导入按需导入
- **定制范围**：全局主题组件特定

## 输出格式

此技能提供：
1. SCSS 变量覆盖示例
2. CSS 变量覆盖示例
3. 构建工具配置
4. 最佳实践和性能提示

## 使用限制

- SCSS 方法需要构建时编译
- CSS 变量在 IE11 中不支持
- 一些复杂定制可能需要两种方法
- 大型 SCSS 文件热模块替换可能很慢
