# Linux 平台安装 Claude Code

Claude Code 是 Anthropic 官方推出的命令行 AI 编码代理。本文档介绍如何在 Linux 平台上安装和配置 Claude Code。

## 系统要求

| 项目 | 最低要求 |
|------|----------|
| 操作系统 | Ubuntu 20.04+ / Debian 11+ / Fedora 38+ / CentOS 8+ / Rocky Linux 9+ |
| Node.js | 18.0.0 或更高版本 |
| npm | 9.0.0 或更高版本 |
| 终端 | 支持 UTF-8 编码的现代终端（GNOME Terminal, Konsole, Alacritty 等） |
| 磁盘空间 | 约 500 MB（含依赖） |
| 网络 | 需要访问 api.anthropic.com |

## 安装步骤

### 1. 安装 Node.js 和 npm

#### Ubuntu / Debian

```bash
# 使用 NodeSource 安装 Node.js 18+
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# 验证安装
node --version   # 应输出 v18.x.x 或更高
npm --version    # 应输出 9.x.x 或更高
```

#### Fedora / RHEL / CentOS

```bash
# 使用 NodeSource 安装
curl -fsSL https://rpm.nodesource.com/setup_18.x | sudo bash -
sudo dnf install -y nodejs

# 验证安装
node --version
npm --version
```

#### 使用 nvm（推荐，便于多版本管理）

```bash
# 安装 nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

# 重新加载 shell 配置
source ~/.bashrc

# 安装并使用 Node.js 18
nvm install 18
nvm use 18

# 验证
node --version
```

### 2. 全局安装 Claude Code

```bash
npm install -g @anthropic-ai/claude-code
```

安装完成后，验证 CLI 是否可用：

```bash
claude --version
```

如果命令未找到，请确保 npm 全局 bin 目录在 `PATH` 中：

```bash
# 查看 npm 全局 bin 路径
npm bin -g

# 添加到 ~/.bashrc（如果不在 PATH 中）
echo 'export PATH="$(npm bin -g):$PATH"' >> ~/.bashrc
source ~/.bashrc
```

## 身份认证

Claude Code 支持多种身份认证方式：

### OAuth 认证（推荐）

```bash
claude login
```

这将打开浏览器窗口，引导您通过 Anthropic Console 完成 OAuth 授权。认证成功后，令牌会存储在 `~/.claude/credentials.json` 中。

### API Key 认证

如果您已经拥有 Anthropic API Key，可以设置环境变量：

```bash
export ANTHROPIC_API_KEY="sk-ant-xxxxxxxxxxxx"
```

建议将其添加到 shell 配置文件中：

```bash
# 添加到 ~/.bashrc 或 ~/.zshrc
echo 'export ANTHROPIC_API_KEY="your-api-key-here"' >> ~/.bashrc
source ~/.bashrc
```

### SSO 认证（企业用户）

```bash
claude login --sso
```

适用于使用单点登录（SSO）的企业组织。系统会引导您完成企业身份验证流程。

## 验证安装

运行诊断检查，确认所有组件正常工作：

```bash
claude doctor
```

该命令会检查：

- Node.js 版本是否满足要求
- npm 全局安装是否正确
- 认证状态是否有效
- API 连接是否正常
- 系统依赖是否完整

## 更新 Claude Code

### 检查当前版本

```bash
claude --version
```

### 更新到最新版本

```bash
npm update -g @anthropic-ai/claude-code
```

### 查看更新日志

```bash
npm view @anthropic-ai/claude-code versions --json
```

通常会输出类似于 `0.8.1` 或 `0.9.0` 的版本号。

## 卸载

```bash
npm uninstall -g @anthropic-ai/claude-code
```

如需同时清除配置文件：

```bash
rm -rf ~/.claude
```

## 常见问题

### Q: 安装时出现权限错误

使用 `nvm` 安装 Node.js 可以避免全局安装时的权限问题。如果使用系统级 Node.js，请考虑：

```bash
# 使用 sudo 安装（不推荐）
sudo npm install -g @anthropic-ai/claude-code

# 或配置 npm 前缀到用户目录（推荐）
npm config set prefix ~/.npm-global
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

### Q: 终端显示乱码

确保终端支持 UTF-8：

```bash
# 检查 locale
locale
# 设置 UTF-8 locale
export LANG=en_US.UTF-8
export LC_ALL=en_US.UTF-8
```

### Q: claude 命令找不到

```bash
# 查找安装位置
npm list -g @anthropic-ai/claude-code
which claude  # 或使用 npm bin -g 查看路径
```

### Q: API 连接超时

检查网络代理或防火墙设置：

```bash
# 如果有代理
export HTTP_PROXY="http://proxy.example.com:8080"
export HTTPS_PROXY="http://proxy.example.com:8080"

# 测试 API 连接
curl -I https://api.anthropic.com
```
