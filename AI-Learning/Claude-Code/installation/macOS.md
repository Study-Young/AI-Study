# macOS 平台安装 Claude Code

Claude Code 是 Anthropic 官方推出的命令行 AI 编码代理。本文档介绍如何在 macOS 平台上安装和配置 Claude Code。

## 系统要求

| 项目 | 最低要求 |
|------|----------|
| 操作系统 | macOS 12 (Monterey) 或更高版本 |
| Node.js | 18.0.0 或更高版本 |
| npm | 9.0.0 或更高版本 |
| 终端 | Terminal.app、iTerm2、Warp 或 Alacritty |
| 磁盘空间 | 约 500 MB（含依赖） |
| 网络 | 需要访问 api.anthropic.com |

## 安装步骤

### 1. 安装 Node.js 和 npm

#### 使用 Homebrew 安装（推荐）

```bash
# 如果尚未安装 Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 安装 Node.js
brew install node@18

# 将 Node.js 添加到 PATH
echo 'export PATH="/usr/local/opt/node@18/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# 验证安装
node --version
npm --version
```

#### 使用 nvm 安装（推荐多版本管理）

```bash
# 安装 nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

# 重新加载 shell 配置（macOS 默认使用 zsh）
source ~/.zshrc

# 安装并使用 Node.js 18
nvm install 18
nvm use 18

# 设置默认版本
nvm alias default 18

# 验证
node --version
```

#### 使用官方安装包

访问 [nodejs.org](https://nodejs.org/) 下载 macOS 安装包（LTS 版本），运行安装程序即可。

### 2. 全局安装 Claude Code

```bash
npm install -g @anthropic-ai/claude-code
```

安装完成后，验证 CLI 是否可用：

```bash
claude --version
```

如果命令未找到，请检查 PATH：

```bash
# npm 全局 bin 路径
npm bin -g
# 典型输出：/usr/local/bin 或 /Users/yourname/.npm-global/bin

# 如果使用 nvm，全局 bin 路径为
# ~/.nvm/versions/node/v18.x.x/bin/
```

## 身份认证

### OAuth 认证（推荐）

```bash
claude login
```

此命令会打开默认浏览器，引导您通过 Anthropic Console 完成 OAuth 授权。认证成功后，令牌会存储在 `~/.claude/credentials.json` 中。

### API Key 认证

如果您已经拥有 Anthropic API Key：

```bash
export ANTHROPIC_API_KEY="sk-ant-xxxxxxxxxxxx"
```

推荐添加到 shell 配置文件（macOS 默认使用 zsh）：

```bash
echo 'export ANTHROPIC_API_KEY="your-api-key-here"' >> ~/.zshrc
source ~/.zshrc
```

### SSO 认证（企业用户）

```bash
claude login --sso
```

适用于使用单点登录（SSO）的企业组织。系统会引导您完成企业身份验证流程。

## 验证安装

运行诊断检查：

```bash
claude doctor
```

该命令会检查：

- Node.js 版本
- npm 全局安装状态
- 认证状态
- API 连接
- 系统依赖完整性

## macOS 特定注意事项

### 代码签名与权限

第一次运行 `claude` 时，macOS 可能会提示允许网络访问。请在系统提示中选择"允许"。

### 终端集成

Claude Code 在 macOS 上会尝试：

- 自动检测是否在 tmux 会话中运行
- 使用 AppleScript 控制终端（需要辅助功能权限）
- 集成 Finder 和 Xcode 等开发工具

如需终端集成功能，可能需要授予终端应用辅助功能权限：

1. 打开 **系统设置** → **隐私与安全性** → **辅助功能**
2. 点击 **+** 号，添加您的终端应用（如 Terminal.app 或 iTerm2）

### Homebrew 安装的 Node.js 注意事项

如果通过 Homebrew 安装 Node.js，请注意全局 npm 包的路径：

```bash
# 确保 /usr/local/bin 在 PATH 中
echo $PATH | grep /usr/local/bin

# 如果使用 Apple Silicon (M1/M2/M3) Mac
# Homebrew 默认安装路径为 /opt/homebrew
echo 'export PATH="/opt/homebrew/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

## 更新 Claude Code

```bash
# 更新到最新版本
npm update -g @anthropic-ai/claude-code

# 查看当前版本
claude --version
```

## 卸载

```bash
# 卸载全局包
npm uninstall -g @anthropic-ai/claude-code

# 清除配置和数据（可选）
rm -rf ~/.claude
```

## 常见问题

### Q: 安装时提示权限错误

使用 nvm 安装 Node.js 可避免权限问题。如已使用 Homebrew：

```bash
# 修复 npm 权限
npm config set prefix ~/.npm-global
mkdir -p ~/.npm-global
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.zshrc
source ~/.zshrc
```

### Q: "claude" 命令无法识别

```bash
# 检查安装
npm list -g @anthropic-ai/claude-code

# 确认 PATH
echo $PATH
# 确保 npm bin 目录在 PATH 中
```

### Q: OAuth 登录时浏览器未自动打开

```bash
# 手动复制浏览器中显示的 URL
# 或在无头环境中使用 API Key 认证
export ANTHROPIC_API_KEY="sk-ant-xxxxx"
```

### Q: M1/M2/M3 Mac 兼容性

Claude Code 完全兼容 Apple Silicon（ARM64）架构。如果遇到兼容性问题，请确保 Node.js 为 ARM64 版本：

```bash
node -p "process.arch"
# 应输出 arm64
```
