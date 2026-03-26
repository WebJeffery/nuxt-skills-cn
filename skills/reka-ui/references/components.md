# 组件

> 自动生成。运行 `npx tsx skills/reka-ui/scripts/generate-components.ts` 以更新。

## 表单

| 组件          | 描述                                 | 文件                         |
| ------------- | ------------------------------------ | ---------------------------- |
| **checkbox**  | 带有不确定状态的选择控件             | `components/checkbox.md`     |
| **combobox**  | 可搜索的下拉菜单，带有过滤功能       | `components/combobox.md`     |
| **editable**  | 带有预览/编辑模式的内联文本编辑      | `components/editable.md`     |
| **label**     | 可访问的表单标签                     | `components/label.md`        |
| **listbox**   | 可访问的列表选择                     | `components/listbox.md`      |
| **numberField** | 带有增减按钮的数字输入             | `components/number-field.md` |
| **pinInput**  | 多字符代码输入（OTP）                | `components/pin-input.md`    |
| **radioGroup** | 互斥选择                            | `components/radio-group.md`  |
| **select**    | 带有分组的下拉选择                   | `components/select.md`       |
| **slider**    | 范围输入控件                         | `components/slider.md`       |
| **switch**    | 在两个状态之间切换                   | `components/switch.md`       |
| **tagsInput** | 多个标签输入和管理                   | `components/tags-input.md`   |
| **toggle**    | 单一状态按钮切换                     | `components/toggle.md`       |
| **toggleGroup** | 具有组行为的多个切换               | `components/toggle-group.md` |

## 日期

| 组件                | 描述                       | 文件                              |
| ------------------- | -------------------------- | --------------------------------- |
| **calendar**        | 日期选择网格（alpha）      | `components/calendar.md`          |
| **dateField**       | 日期输入字段（alpha）      | `components/date-field.md`        |
| **datePicker**      | 带日历的日期选择器（alpha）| `components/date-picker.md`       |
| **dateRangeField**  | 日期范围输入（alpha）      | `components/date-range-field.md`  |
| **dateRangePicker** | 日期范围选择器（alpha）    | `components/date-range-picker.md` |
| **rangeCalendar**   | 日期范围日历（alpha）      | `components/range-calendar.md`    |
| **timeField**       | 时间输入字段（alpha）      | `components/time-field.md`        |

## 披露

| 组件          | 描述                  | 文件                        |
| ------------- | --------------------- | --------------------------- |
| **accordion** | 可折叠内容部分        | `components/accordion.md`   |
| **collapsible** | 单个可折叠面板        | `components/collapsible.md` |

## 覆盖层

| 组件          | 描述                   | 文件                         |
| ------------- | ---------------------- | ---------------------------- |
| **alertDialog** | 需要操作的模态对话框   | `components/alert-dialog.md` |
| **dialog**    | 模态对话框             | `components/dialog.md`       |
| **hoverCard** | 悬停时显示的卡片       | `components/hover-card.md`   |
| **popover**   | 浮动内容面板           | `components/popover.md`      |
| **tooltip**   | 信息性悬停提示         | `components/tooltip.md`      |
| **toast**     | 临时通知               | `components/toast.md`        |

## 菜单

| 组件              | 描述                  | 文件                            |
| ----------------- | --------------------- | ------------------------------- |
| **contextMenu**   | 右键上下文菜单        | `components/context-menu.md`    |
| **dropdownMenu**  | 下拉操作菜单          | `components/dropdown-menu.md`   |
| **menubar**       | 水平菜单栏            | `components/menubar.md`         |
| **navigationMenu** | 站点导航菜单          | `components/navigation-menu.md` |

## 数据

| 组件         | 描述                     | 文件                        |
| ------------ | ------------------------ | --------------------------- |
| **avatar**   | 带有回退的用户图像       | `components/avatar.md`      |
| **pagination** | 页面导航                 | `components/pagination.md`  |
| **progress** | 进度指示器               | `components/progress.md`    |
| **scrollArea** | 自定义滚动条容器        | `components/scroll-area.md` |
| **separator** | 视觉分隔符               | `components/separator.md`   |
| **splitter** | 可调整大小的拆分面板     | `components/splitter.md`    |
| **stepper**  | 多步骤进度指示器         | `components/stepper.md`     |
| **tabs**     | 选项卡内容面板           | `components/tabs.md`        |
| **tree**     | 层次结构树视图           | `components/tree.md`        |

## 布局

| 组件          | 描述                  | 文件                         |
| ------------- | --------------------- | ---------------------------- |
| **aspectRatio** | 保持宽高比            | `components/aspect-ratio.md` |
| **toolbar**   | 带有按钮/切换的工具栏 | `components/toolbar.md`      |

## 实用工具

| 组件              | 描述                  | 文件                            |
| ----------------- | --------------------- | ------------------------------- |
| **configProvider** | 全局配置上下文        | `components/config-provider.md` |
| **focusScope**    | 焦点陷阱容器          | `components/focus-scope.md`     |
| **presence**      | 动画存在控制          | `components/presence.md`        |
| **primitive**     | 基础元素包装器        | `components/primitive.md`       |
| **visuallyHidden** | 仅屏幕阅读器可见内容  | `components/visually-hidden.md` |

## 可组合项

| 可组合项              | 描述                                                            |
| --------------------- | --------------------------------------------------------------- |
| `useEmitAsProps`      | 将 emit 函数转换为 props 以传递给子组件                         |
| `useFilter`           | 根据搜索查询过滤项目，支持自定义匹配                           |
| `useForwardProps`     | 向前传递 props 给子组件，同时过滤掉已处理的 props              |
| `useForwardPropsEmits` | 组合 useForwardProps 和 useEmitAsProps                         |
| `useForwardExpose`    | 向前暴露子组件的方法/引用                                       |
| `useId`               | 为可访问性属性生成唯一 ID                                       |
| `useDateFormatter`    | 带有区域设置支持的日期格式化                                   |
| `useDirection`        | 获取/设置文本方向（ltr/rtl）                                   |
| `useLocale`           | 获取/设置国际化区域设置                                         |