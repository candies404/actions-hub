# Docker 镜像自动标记与通知

## 功能特点

- 自动拉取 [vaultwarden/server:latest](https://github.com/dani-garcia/vaultwarden) 镜像
- 使用私人 Docker Hub 账户重新标记镜像
- 推送标记后的镜像到私人仓库
- 通过 WPush 发送操作完成通知
- 支持手动触发 ~~定时执行（每天北京时间 8:00）~~

## 配置说明

### 必需的 Secrets

在 GitHub 仓库的 Settings -> Secrets and variables -> Actions 中设置以下密钥：

- `DOCKERHUB_USERNAME`: Docker Hub 用户名
- `DOCKERHUB_TOKEN`: Docker Hub 访问令牌
- `WPUSH_KEY`: WPush 通知密钥

### 使用方法

1. Fork 本仓库
2. 配置必需的 Secrets
3. 启用 GitHub Actions
4. 等待自动执行或手动触发工作流

### 手动触发

在 Actions 页面选择 "Vaultwarden 镜像同步" 工作流，点击 "Run workflow" 即可手动触发。

