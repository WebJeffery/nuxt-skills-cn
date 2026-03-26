# 切换

单状态按钮切换

**部分：** `Toggle`

## Toggle

### 属性

| 属性           | 类型                 | 默认值    |
| -------------- | -------------------- | ---------- |
| `as`           | `AsTag \| Component` | `"button"` |
| `asChild`      | `boolean`            | -          |
| `defaultValue` | `boolean`            | -          |
| `disabled`     | `boolean`            | `false`    |
| `pressed`      | `boolean`            | -          |

### 事件

| 事件            | 载荷            |
| ---------------- | ------------------ |
| `update:pressed` | `[value: boolean]` |

### 插槽

| 插槽      | 类型      |
| --------- | --------- |
| `pressed` | `boolean` |
