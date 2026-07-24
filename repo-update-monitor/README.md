# 仓库更新检查工作流

一个用于自动检查 GitHub 仓库更新并发送通知的 GitHub Actions 工作流。

## 功能特点

- 🔄 自动检查多个仓库的更新状态
- ⏰ 支持定时和手动触发
- 💾 通过 Issue 存储更新记录
- 📊 生成详细的更新报告
- 📱 支持多渠道通知
- ⚠️ 完善的错误处理机制

## 快速开始

### 1. Fork 仓库

启用 `repo-update-monitor.yml`(检查仓库更新)

### 2. 设置环境变量

在 Settings > Secrets and variables > Actions > Variables 中添加：

- `REPOS`：要监控的仓库列表
  ```
  owner1/repo1 owner2/repo2
  ```
  **注意：使用空格分隔多个仓库**

### 3. 设置通知渠道

在 Settings > Secrets and variables > Actions > Secrets 中添加：

- WxPusher（推荐）
  - `WPUSH_KEY`：WxPusher 的 API key

## 工作流说明

### 触发条件

- 🖱️ 手动触发：支持在 Actions 页面手动运行
- ⏱️ 定时触发：可通过取消注释 cron 配置启用

### 更新检测机制

1. **首次运行**
  - 检查所有仓库 24 小时内的更新
  - 记录当前所有仓库的 SHA

2. **后续运行**
  - 对比存储的 SHA 判断更新
  - 自动更新 SHA 记录

### 通知内容示例

```
### 📊 统计信息

总仓库数：3
有更新：1
无更新：1
错误：1

---

##### ✅ 仓库：[owner/repo](https://github.com/owner/repo)

📝 **描述**：仓库描述
⏰ **更新时间**：2024-01-01 08:00:00 CST
📌 **提交信息**：更新说明
⏳ **状态**：30分钟未更新

---
```

### 更新状态判断

- ✅ 有更新（SHA 不同）
- ❌ 无更新（SHA 相同）
- ⚠️ 检查失败（API 错误等）

## 自定义配置

### 启用定时检查

编辑 `repo-update-monitor.yml` 取消注释：

```yaml
on:
  schedule:
    - cron: '0 0 * * *'  # 每天 UTC 0:00（北京时间 8:00）
```

### 通知配置

```yaml
- name: 发送通知
  uses: candies404/Multi-Channel-Notifier@latest
  with:
    hitokoto: 'false'  # 是否显示一言
```

## 工作流程序说明

### 主要步骤s

1. **检出代码**
   ```yaml
   - name: 检出代码
     uses: actions/checkout@v7
   ```

2. **查找或创建 SHA 存储 Issue**
   ```yaml
   - name: 查找或创建存储SHA的Issue
     id: get-issue
     env:
       GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
   ```

3. **读取存储的 SHA**
   ```yaml
   - name: 读取存储的SHA
     id: read-shas
     env:
       GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
   ```

4. **检查更新状态**
   ```yaml
   - name: 检查仓库更新状态
     id: check-updates
     env:
       REPOS: ${{ vars.REPOS }}
       GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
   ```

5. **更新 SHA 存储**
   ```yaml
   - name: 更新存储的SHA
     if: success()
     env:
       GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
   ```

6. **发送通知**
   ```yaml
   - name: 发送通知
     if: steps.check-updates.outputs.has_updates == 'true'
     uses: candies404/Multi-Channel-Notifier@latest
   ```

## 依赖项目

- [Multi-Channel-Notifier](https://github.com/candies404/Multi-Channel-Notifier)

## 注意事项

1. 确保仓库列表格式正确（空格分隔）
2. 首次运行会创建用于存储 SHA 的 Issue
3. API 请求可能受到 GitHub 限制
4. 建议配置 WxPusher 通知

## 常见问题

Q: 如何确认工作流正常运行？  
A: 检查 Actions 页面的运行日志和创建的 SHA 存储 Issue

Q: 为什么收不到通知？  
A: 1. 检查 WPUSH_KEY 配置是否正确
2. 确认是否有仓库更新
3. 查看 Actions 运行日志是否有错误

Q: 如何重置更新检查？  
A: 删除或清空 SHA 存储 Issue 的内容，下次运行将重新开始检查

Q: 支持私有仓库检查吗？  
A: 支持，但需要确保 GitHub Token 有足够的权限
