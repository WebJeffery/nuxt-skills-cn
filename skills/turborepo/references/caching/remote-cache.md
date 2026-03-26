# 远程缓存

在你的团队和 CI 管道之间共享缓存工件。

## 优势

- 团队成员从彼此的工作中获得缓存命中
- CI 从本地开发中获得缓存命中（反之亦然）
- 第一次构建后 CI 运行速度显著加快
- 不再有"在我的机器上可以工作"的重新构建

## Vercel 远程缓存

在 Vercel 上部署时免费、零配置。对于本地开发和其他 CI：

### 本地开发设置

```bash
# 使用 Vercel 身份验证
npx turbo login

# 将仓库链接到你的 Vercel 团队
npx turbo link
```

这会创建 `.turbo/config.json`，其中包含你的团队信息（默认被 gitignore）。

### CI 设置

设置这些环境变量：

```bash
TURBO_TOKEN=<your-token>
TURBO_TEAM=<your-team-slug>
```

从 Vercel 仪表板 → 设置 → 令牌获取你的令牌。

**GitHub Actions 示例：**

```yaml
- name: Build
  run: npx turbo build
  env:
    TURBO_TOKEN: ${{ secrets.TURBO_TOKEN }}
    TURBO_TEAM: ${{ vars.TURBO_TEAM }}
```

## turbo.json 中的配置

```json
{
  "remoteCache": {
    "enabled": true,
    "signature": false
  }
}
```

选项：

- `enabled`：切换远程缓存（默认：身份验证时为 true）
- `signature`：需要工件签名（默认：false）

## 工件签名

验证缓存工件未被篡改：

```bash
# 设置密钥（在所有环境中使用相同的密钥）
export TURBO_REMOTE_CACHE_SIGNATURE_KEY="your-secret-key"
```

在配置中启用：

```json
{
  "remoteCache": {
    "signature": true
  }
}
```

只有签名匹配时才能恢复已签名的工件。

## 自托管选项

用于运行自己的缓存服务器的社区实现：

- **turbo-remote-cache** (Node.js) - 支持 S3、GCS、Azure
- **turborepo-remote-cache** (Go) - 轻量级、S3 兼容
- **ducktape** (Rust) - 高性能选项

使用环境变量配置：

```bash
TURBO_API=https://your-cache-server.com
TURBO_TOKEN=your-auth-token
TURBO_TEAM=your-team
```

## 缓存行为控制

```bash
# 为运行禁用远程缓存
turbo build --remote-cache-read-only  # 读取但不写入
turbo build --no-cache                # 完全跳过缓存

# 环境变量替代方案
TURBO_REMOTE_ONLY=true  # 仅使用远程，跳过本地
```

## 调试远程缓存

```bash
# 详细输出显示缓存操作
turbo build --verbosity=2

# 检查是否配置了远程缓存
turbo config
```

查找：

- 输出中的"Remote caching enabled"
- 运行期间的上传/下载消息
- 带有远程缓存指示器的"cache hit, replaying output"