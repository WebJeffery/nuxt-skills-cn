# 边界

**实验性功能** - 请参阅 [RFC](https://github.com/vercel/turborepo/discussions/9435)

完整文档：https://turborepo.dev/docs/reference/boundaries

边界通过检测以下内容来强制包隔离：

1. 包目录之外的文件导入
2. 未在 `package.json` dependencies 中声明的包导入

## 使用

```bash
turbo boundaries
```

运行此命令以检查 monorepo 中的工作区违规。

## 标签

标签允许您创建规则，规定哪些包可以相互依赖。

### 向包添加标签

```json
// packages/ui/turbo.json
{
  "tags": ["internal"]
}
```

### 配置标签规则

规则放在根 `turbo.json` 中：

```json
// turbo.json
{
  "boundaries": {
    "tags": {
      "public": {
        "dependencies": {
          "deny": ["internal"]
        }
      }
    }
  }
}
```

这可以防止 `public` 标记的包导入 `internal` 标记的包。

### 规则类型

**允许列表方法**（仅允许特定标签）：

```json
{
  "boundaries": {
    "tags": {
      "public": {
        "dependencies": {
          "allow": ["public"]
        }
      }
    }
  }
}
```

**拒绝列表方法**（阻止特定标签）：

```json
{
  "boundaries": {
    "tags": {
      "public": {
        "dependencies": {
          "deny": ["internal"]
        }
      }
    }
  }
}
```

**限制依赖项**（谁可以导入此包）：

```json
{
  "boundaries": {
    "tags": {
      "private": {
        "dependents": {
          "deny": ["public"]
        }
      }
    }
  }
}
```

### 使用包名

包名可以代替标签使用：

```json
{
  "boundaries": {
    "tags": {
      "private": {
        "dependents": {
          "deny": ["@repo/my-pkg"]
        }
      }
    }
  }
}
```

## 关键点

- 规则传递应用（依赖项的依赖项）
- 有助于在大规模时强制执行架构边界
- 在运行时/构建错误之前捕获违规