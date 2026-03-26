---
name: vitest-cli
description: 命令行界面命令和选项
---

# 命令行界面

## 命令

### `vitest`

在监视模式(开发)或运行模式(CI)中启动 Vitest:

```bash
vitest                    # 开发中的监视模式,CI 中的运行模式
vitest foobar             # 运行路径中包含 "foobar" 的测试
vitest basic/foo.test.ts:10  # 通过文件和行号运行特定测试
```

### `vitest run`

在没有监视模式的情况下运行测试一次:

```bash
vitest run
vitest run --coverage
```

### `vitest watch`

显式启动监视模式:

```bash
vitest watch
```

### `vitest related`

运行导入特定文件的测试(与 lint-staged 一起使用):

```bash
vitest related src/index.ts src/utils.ts --run
```

### `vitest bench`

仅运行基准测试:

```bash
vitest bench
```

### `vitest list`

列出所有匹配的测试而不运行它们:

```bash
vitest list                    # 列出测试名称
vitest list --json             # 输出为 JSON
vitest list --filesOnly        # 仅列出测试文件
```

### `vitest init`

初始化项目设置:

```bash
vitest init browser            # 设置浏览器测试
```

## 常用选项

```bash
# 配置
--config <path>           # 配置文件的路径
--project <name>          # 运行特定项目

# 过滤
--testNamePattern, -t     # 运行匹配模式的测试
--changed                 # 运行更改文件的测试
--changed HEAD~1          # 最后一次提交更改的测试

# 报告器
--reporter <name>         # default, verbose, dot, json, html
--reporter=html --outputFile=report.html

# 覆盖率
--coverage                # 启用覆盖率
--coverage.provider v8    # 使用 v8 提供程序
--coverage.reporter text,html

# 执行
--shard <index>/<count>   # 跨机器拆分测试
--bail <n>                # n 次失败后停止
--retry <n>               # 重试失败的测试 n 次
--sequence.shuffle        # 随机化测试顺序

# 监视模式
--no-watch                # 禁用监视模式
--standalone              # 在不运行测试的情况下启动

# 环境
--environment <env>       # jsdom, happy-dom, node
--globals                 # 启用全局 API

# 调试
--inspect                 # 启用 Node 检查器
--inspect-brk             # 启动时中断

# 输出
--silent                  # 抑制控制台输出
--no-color                # 禁用颜色
```

## Package.json 脚本

```json
{
  "scripts": {
    "test": "vitest",
    "test:run": "vitest run",
    "test:ui": "vitest --ui",
    "coverage": "vitest run --coverage"
  }
}
```

## CI 分片

跨多台机器拆分测试:

```bash
# 机器 1
vitest run --shard=1/3 --reporter=blob

# 机器 2
vitest run --shard=2/3 --reporter=blob

# 机器 3
vitest run --shard=3/3 --reporter=blob

# 合并报告
vitest --merge-reports --reporter=junit
```

## 监视模式键盘快捷键

在监视模式下,按:
- `a` - 运行所有测试
- `f` - 仅运行失败的测试
- `u` - 更新快照
- `p` - 按文件名模式过滤
- `t` - 按测试名模式过滤
- `q` - 退出

## 关键点

- 监视模式在开发中是默认的,在 CI 中是运行模式(当设置了 `process.env.CI` 时)
- 使用 `--run` 标志确保单次运行(对 lint-staged 很重要)
- camelCase (`--testTimeout`) 和 kebab-case (`--test-timeout`) 都有效
- 布尔选项可以使用 `--no-` 前缀否定

<!-- 
Source references:
- https://vitest.dev/guide/cli.html
-->
