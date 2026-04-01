# 第 0 天 — Claude Code 安装配置

本指南将引导您在机器上安装 Claude Code 并完成身份验证，以便开始使用它。

## 步骤 1：安装 Claude Code

请选择您的操作系统：

| 操作系统 | 安装指南 |
|----------|----------|
| Windows | [windows.md](windows.md) |
| Linux | [linux.md](linux.md) |
| macOS | [mac.md](mac.md) |

请按照您操作系统的对应指南进行操作，然后回到本页完成身份验证。

---

## 步骤 2：验证安装

按照对应操作系统的指南完成后，确认一切正常：

```bash
node --version    # 应显示 v18.x 或更高版本
claude --version  # 应显示已安装的 Claude Code 版本
```

---

## 步骤 3：登录

<img src="assets/login.png" alt="Claude Code 登录界面" width="50%">

在终端中运行 `claude`。首次启动时，它会要求您选择登录方式。

### 方式 1：订阅（Claude Pro / Max）

- 选择 **Claude.ai 账户**
- 浏览器会自动打开 — 登录并授权
- 返回终端，您已登录成功

### 方式 2a：API 密钥（团队邀请）

您的团队管理员会在 Anthropic 控制面板向您发送邀请。

- 您会收到**邀请邮件** — 接受邀请并创建您的 Anthropic 账户
- 在终端中运行 `claude`
- 选择 **Anthropic API 密钥**
- 您的密钥会在控制面板**自动生成** — 无需手动设置
- Claude Code 会立即生效

### 方式 2b：API 密钥（您已持有密钥）

如果有人通过 Slack、邮件等方式与您分享了密钥，或者您自己创建了密钥：

- 在终端中运行 `claude`
- 选择 **Anthropic API 密钥**
- 粘贴您的密钥（以 `sk-ant-` 开头）
- 密钥会**永久存储** — 不会再提示您输入

---