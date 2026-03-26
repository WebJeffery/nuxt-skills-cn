# 认证表单

可自定义的表单，用于创建登录、注册或密码重置表单。

## 关键属性

- `fields`: 作为具有以下属性的对象数组：

- `name: string`{lang="ts-type"}
- `type: 'checkbox' | 'select' | 'otp' | 'InputHTMLAttributes['type']'`{lang="ts-type"}

每个字段必须包含 `type` 属性，该属性确定输入组件和应用的任何附加属性：`checkbox` 字段使用 [Checkbox](/docs/components/checkbox#props) 属性，`select` 字段使用 [SelectMenu](/docs/components/select-menu#props) 属性，`otp` 字段使用 [PinInput](/docs/components/pin-input#props) 属性，所有其他类型使用 [Input](/docs/components/input#props) 属性。

- `title`: 设置表单的标题。
- `description`: 设置表单的描述。
- `icon`: 设置表单的图标。
- `providers`: 向表单添加提供程序。
- `separator`: 自定义提供程序和字段之间的 [Separator](/docs/components/separator)。
- `submit`: 更改表单的提交按钮。

## 用法

```vue
<UAuthForm
  <!-- props here -->
/>
```
