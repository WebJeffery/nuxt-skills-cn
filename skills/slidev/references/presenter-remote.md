---
name: remote-access
description: 在网络或互联网上共享演示文稿
---

# 远程访问

在网络或互联网上共享演示文稿。

## 启用远程访问

```bash
slidev --remote
```

## 密码保护

```bash
slidev --remote=your_password
```

访问演讲者模式需要密码。

## 远程隧道

通过 Cloudflare Quick Tunnels 暴露到互联网：

```bash
slidev --remote --tunnel
```

创建公共 URL 以便在无需服务器设置的情况下进行共享。

## 使用场景

- 从手机/平板电脑控制演示文稿
- 多个演讲者
- 远程演示
- 实时流式传输
- 观众在自己的设备上观看
