# Windows 平台安装 Claude Code

Claude Code 是 Anthropic 官方推出的命令行 AI 编码代理。本文档介绍如何在 Windows 平台上安装和配置 Claude Code。

## 系统要求

| 项目 | 最低要求 |
|------|----------|
| 操作系统 | Windows 10 版本 1803+ / Windows 11 |
| 环境 | WSL 2 (Windows Subsystem for Linux) 或 Git Bash |
| Node.js | 18.0.0 或更高版本 |
| npm | 9.0.0 或更高版本 |
| 终端 | Windows Terminal (推荐) / PowerShell / Git Bash |
| 磁盘空间 | 约 500 MB（含依赖） |
| 网络 | 需要访问 api.anthropic.com |

> **注意**：Claude Code 原生运行在 Unix 环境中，Windows 用户强烈建议通过 **WSL 2** 安装和使用。

## 方案一：通过 WSL 2 安装（推荐）

### 1. 安装 WSL 2

以管理员身份打开 PowerShell 并运行：

```powershell
# 安装 WSL 2 并设置默认版本
wsl --install -d Ubuntu-22.04
wsl --set-default-version 2

# 安装完成后重启电脑
```

安装后，从开始菜单启动 **Ubuntu** 进入 WSL 环境。

> 更多信息请参考 [Microsoft WSL 安装文档](https://learn.microsoft.com/zh-cn/windows/wsl/install)

### 2. 在 WSL 中安装 Node.js

```bash
# 进入 WSL 终端后
# 使用 nvm 安装 Node.js
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc
nvm install 18
nvm use 18

# 验证
node --version
npm --version
```

### 3. 在 WSL 中安装 Claude Code

```bash
npm install -g @anthropic-ai/claude-code
```

### 4. 验证安装

```bash
claude --version
claude doctor
```

### 5. 在 Windows Terminal 中配置 WSL

推荐使用 Windows Terminal，它提供更好的 WSL 集成体验：

1. 从 Microsoft Store 安装 **Windows Terminal**
2. 启动后，下拉菜单中选择 **Ubuntu**（或您的 WSL 发行版）
3. 在此终端中运行 Claude Code

## 方案二：通过 Git Bash 安装

### 1. 安装 Git for Windows

从 [git-scm.com](https://git-scm.com/download/win) 下载并安装 Git for Windows。

### 2. 安装 Node.js

从 [nodejs.org](https://nodejs.org/) 下载 Windows LTS 安装包并安装。

### 3. 在 Git Bash 中安装 Claude Code

```bash
# 启动 Git Bash
npm install -g @anthropic-ai/claude-code
```

> **注意**：Git Bash 环境下部分终端集成功能可能受限，建议优先使用 WSL。

## 身份认证

### OAuth 认证（推荐）

```bash
claude login
```

在 WSL 或 Git Bash 中运行此命令，系统会自动打开默认浏览器完成 OAuth 授权。

### API Key 认证

```bash
export ANTHROPIC_API_KEY="sk-ant-xxxxxxxxxxxx"
```

添加到 WSL 的 `~/.bashrc`：

```bash
echo 'export ANTHROPIC_API_KEY="your-api-key-here"' >> ~/.bashrc
source ~/.bashrc
```

### SSO 认证（企业用户）

```bash
claude login --sso
```

## Windows 特定配置

### 将 WSL 添加到 PATH

在 Windows PowerShell 中，可以添加别名以便在 PowerShell 中直接调用 Claude Code：

```powershell
# 在 PowerShell $PROFILE 中添加函数
function claude { wsl -d Ubuntu-22.04 claude @args }
```

### 文件系统互操作

Claude Code 在 WSL 中可以访问：

- WSL 文件系统：`/home/yourname/`
- Windows 文件系统：`/mnt/c/Users/yourname/`

推荐将项目文件放在 WSL 文件系统中以获得最佳性能。

### Windows 防火墙

如果 API 连接失败，请确保 Windows 防火墙允许 WSL 的网络访问：

```powershell
# 以管理员身份运行
New-NetFirewallRule -DisplayName "WSL" -Direction Outbound -InterfaceType Any -Action Allow
```

## 更新 Claude Code

```bash
# WSL 环境中
npm update -g @anthropic-ai/claude-code
```

## 卸载

```bash
# 在 WSL 或 Git Bash 中
npm uninstall -g @anthropic-ai/claude-code

# 可选：清除配置文件
rm -rf ~/.claude
```

## 常见问题

### Q: WSL 网络连接问题

```bash
# 在 Windows PowerShell（管理员）中重启 WSL
wsl --shutdown
wsl

# 或在 WSL 中测试网络
curl -I https://api.anthropic.com
```

### Q: 中文显示乱码

确保 WSL 终端支持 UTF-8：

```bash
# 设置 locale
sudo locale-gen zh_CN.UTF-8
export LANG=zh_CN.UTF-8
export LC_ALL=zh_CN.UTF-8
```

### Q: 文件权限问题

如果遇到 Windows 和 WSL 文件系统之间的权限问题：

```bash
# 在 WSL 中，将项目放在 Linux 文件系统下
mkdir -p ~/projects
cd ~/projects

# 访问 Windows 文件
ls /mnt/c/Users/yourname/Desktop/
```

### Q: npm install 失败

```bash
# 清理 npm 缓存
npm cache clean --force

# 重试安装
npm install -g @anthropic-ai/claude-code
```
