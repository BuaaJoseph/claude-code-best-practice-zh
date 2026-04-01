# Linux 配置

[返回第 0 天](README.md)

## 前提条件

您需要 **Node.js v18 或更高版本**以及 **npm**。

## 步骤 1：安装 Node.js

### 方式 A：通过 nodejs.org 下载页面使用 fnm（推荐）

**fnm**（Fast Node Manager）是 Node.js 官方推荐的工具。它轻量、快速，如果您以后需要切换 Node 版本也很方便。

1. 打开浏览器，访问 [nodejs.org/en/download](https://nodejs.org/en/download)。

2. 您会看到一排下拉框，上面写着：**"Get Node.js® vXX.XX.X (LTS) for __ using __ with __"**。按如下设置：

   | 下拉框 | 选择 |
   |--------|------|
   | 版本 | **vXX.XX.X (LTS)** — 保持默认的 LTS 版本，不要更改 |
   | 操作系统 | **Linux** |
   | 包管理器 | **fnm**（位于 "Recommended (Official)" 下方）|
   | 包格式 | **npm** — 保持默认 |

3. 页面会显示您需要运行的精确命令。打开终端并复制粘贴这些命令。它们大致如下：

   ```bash
   # 步骤 1 — 安装 fnm
   curl -fsSL https://fnm.vercel.app/install | bash

   # 步骤 2 — 重启终端或重新加载 shell 配置
   source ~/.bashrc   # 或者：source ~/.zshrc（如果您使用 zsh）

   # 步骤 3 — 安装 Node.js
   fnm install 24   # 页面会显示确切的版本号
   ```

   > 版本号可能与上面不同 — 请始终使用网站显示的版本号。

4. **关闭并重新打开终端**（或运行上面的 `source` 命令），这样 `fnm`、`node` 和 `npm` 就可以使用了。

> **为什么选择 fnm？** 它位于 Node.js 下载页面的 "Recommended (Official)" 分类中。与 nvm 类似，它会将 Node 安装到您的家目录中，因此永远不需要为 npm 全局安装使用 `sudo` — 但 fnm 明显更快（用 Rust 编写），并且在 Windows、macOS 和 Linux 上都能正常工作。

### 方式 B：使用您发行版的包管理器

这种方法更快，但可能会安装旧版本的 Node.js。**安装后检查版本** — 如果低于 v18，请改用方式 A。

**Ubuntu / Debian：**

```bash
sudo apt update
sudo apt install -y nodejs npm

# 检查版本
node --version   # 必须为 v18 或更高版本
```

**Fedora：**

```bash
sudo dnf install -y nodejs npm
```

**Arch Linux：**

```bash
sudo pacman -S nodejs npm
```

### 方式 C：NodeSource（通过 apt 安装最新 LTS，无需 nvm）

适用于想要最新 LTS 版本但不想使用 nvm 的 Ubuntu/Debian 用户：

```bash
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt install -y nodejs
```

## 步骤 2：验证 Node.js

```bash
node --version
npm --version
```

两者都应打印版本号。`node --version` 必须显示 v18.x 或更高版本。

## 步骤 3：安装 Claude Code

```bash
npm install -g @anthropic-ai/claude-code
```

> **遇到权限错误？**
> - 如果您使用了 **fnm** 或 **nvm**：这不应该发生。请检查它是否已激活（`which node` 应该指向您家目录内的路径，而不是 `/usr/...`）。
> - 如果您使用了系统安装：可以尝试使用 `sudo npm install -g @anthropic-ai/claude-code`，或者修复 npm 的全局目录权限：
>   ```bash
>   mkdir -p ~/.npm-global
>   npm config set prefix '~/.npm-global'
>   echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
>   source ~/.bashrc
>   ```

## 步骤 4：验证 Claude Code

```bash
claude --version
```

您应该能看到 Claude Code 的版本号。现在返回 [README.md](README.md) 进行身份验证配置。

---

## 注意事项

- **WSL（Windows Subsystem for Linux）：** 本指南同样适用于 WSL。只需从您的 WSL 终端按这些步骤操作即可。
- **PATH 问题：** 如果安装后找不到 `claude`，请确保 npm 的全局 bin 目录在您的 PATH 中。运行 `npm config get prefix` — 该路径的 `bin/` 子目录需要添加到您的 PATH 中。