# CI 优化模式

使用 Turborepo 进行高效 CI/CD 的策略。

## PR 与主分支构建

### PR 构建：仅受影响

仅测试 PR 中更改的内容：

```yaml
- name: Test (PR)
  if: github.event_name == 'pull_request'
  run: turbo run build test --affected
```

### 主分支：完整构建

确保合并时的完整验证：

```yaml
- name: Test (Main)
  if: github.ref == 'refs/heads/main'
  run: turbo run build test
```

## 使用 --filter 的自定义 Git 范围

对于高级场景，将 `--filter` 与 git 引用一起使用：

```bash
# 自特定提交以来的更改
turbo run test --filter="...[abc123]"

# 引用之间的更改
turbo run test --filter="...[main...HEAD]"

# 最后 3 次提交中的更改
turbo run test --filter="...[HEAD~3]"
```

## 缓存策略

### 远程缓存（推荐）

最佳性能 - 在所有 CI 运行和开发者之间共享：

```yaml
env:
  TURBO_TOKEN: ${{ secrets.TURBO_TOKEN }}
  TURBO_TEAM: ${{ vars.TURBO_TEAM }}
```

### actions/cache 后备方案

当远程缓存不可用时：

```yaml
- uses: actions/cache@v4
  with:
    path: .turbo
    key: turbo-${{ runner.os }}-${{ github.sha }}
    restore-keys: |
      turbo-${{ runner.os }}-${{ github.ref }}-
      turbo-${{ runner.os }}-
```

限制：

- 缓存是分支范围的
- PR 从基础分支缓存恢复
- 效率低于远程缓存

## 矩阵构建

跨 Node 版本测试：

```yaml
strategy:
  matrix:
    node: [18, 20, 22]

steps:
  - uses: actions/setup-node@v4
    with:
      node-version: ${{ matrix.node }}

  - run: turbo run test
```

## 跨作业并行化

将任务拆分为单独的作业：

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - run: turbo run lint --affected

  test:
    runs-on: ubuntu-latest
    steps:
      - run: turbo run test --affected

  build:
    runs-on: ubuntu-latest
    needs: [lint, test]
    steps:
      - run: turbo run build
```

### 缓存注意事项

并行化时：

- 每个作业都有单独的缓存写入
- 远程缓存自动处理此问题
- 使用 actions/cache 时，为每个作业使用唯一键以避免冲突

```yaml
- uses: actions/cache@v4
  with:
    path: .turbo
    key: turbo-${{ runner.os }}-${{ github.job }}-${{ github.sha }}
```

## 条件任务

在草稿 PR 上跳过昂贵的任务：

```yaml
- name: E2E Tests
  if: github.event.pull_request.draft == false
  run: turbo run test:e2e --affected
```

或要求标签进行完整测试：

```yaml
- name: Full Test Suite
  if: contains(github.event.pull_request.labels.*.name, 'full-test')
  run: turbo run test
```