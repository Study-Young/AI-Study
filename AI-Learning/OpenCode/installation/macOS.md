# macOS 安装指南

本文档介绍在 macOS 系统上安装 OpenCode 的多种方法。

## 系统要求

- **操作系统**: macOS 12 (Monterey) 或更高版本
- **Node.js**: 18.0.0 或更高版本（使用 npm 安装时需要）
- **终端**: 终端.app、iTerm2、Warp 或任何现代终端模拟器
- **Git**: 随 Xcode Command Line Tools 一起安装（可选，用于部分高级功能）

## 安装方法

### 方法一：通过 Homebrew 安装（推荐）

Homebrew 是 macOS 上最流行的包管理器，推荐 macOS 用户使用此方式。

```bash
# 安装 OpenCode
brew install anomalyco/tap/opencode

# 验证安装
opencode --version
```

> **前提条件**: 如果尚未安装 Homebrew，请先运行：
> ```bash
> /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
> ```

### 方法二：通过 npm 安装

如果您使用 Node.js 开发环境，也可以通过 npm 安装。

```bash
# 全局安装
npm i -g opencode-ai@latest

# 验证安装
opencode --version
```

#### 使用 nvm 管理 Node.js（推荐方式）

```bash
# 安装 nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash

# 重启终端或执行
source ~/.zshrc

# 安装 Node.js LTS
nvm install --lts

# 安装 OpenCode
npm i -g opencode-ai@latest
```

### 方法三：二进制下载

从 [opencode.ai](https://opencode.ai) 或 [GitHub Releases 页面](https://github.com/anomalyco/opencode/releases) 下载预编译的 macOS 二进制文件。

```bash
# 示例：下载并安装 macOS 版本（Apple Silicon 或 Intel）
curl -L -o opencode.zip "https://opencode.ai/downloads/latest/macos-arm64.zip"
unzip opencode.zip
sudo mv opencode /usr/local/bin/

# 验证安装
opencode --version
```

> **提示**: 如果您使用的是 Intel Mac，请将 URL 中的 `macos-arm64` 替换为 `macos-x86_64`。

## 安装后配置

### 认证

安装完成后，您需要通过以下方式之一进行认证：

```bash
# 交互式登录（推荐）
opencode auth login

# 或设置环境变量
export OPENAI_API_KEY="sk-your-key-here"
export ANTHROPIC_API_KEY="sk-ant-your-key-here"
export OPENROUTER_API_KEY="sk-or-your-key-here"
```

建议将环境变量添加到您的 shell 配置文件中（如 `~/.zshrc` 或 `~/.bash_profile`）：

```bash
echo 'export OPENAI_API_KEY="sk-your-key-here"' >> ~/.zshrc
source ~/.zshrc
```

### Shell 自动补全（可选）

OpenCode 支持为常用 Shell 生成自动补全脚本：

```bash
# Zsh（macOS 默认）
opencode completion zsh > ~/.zsh/completions/_opencode
echo 'fpath=(~/.zsh/completions $fpath)' >> ~/.zshrc
echo 'autoload -U compinit && compinit' >> ~/.zshrc
source ~/.zshrc

# Bash
opencode completion bash > /usr/local/etc/bash_completion.d/opencode
source ~/.bash_profile

# Fish（需单独安装）
opencode completion fish > ~/.config/fish/completions/opencode.fish
```

## 卸载

根据安装方式选择对应命令：

```bash
# Homebrew 方式
brew uninstall opencode
brew untap anomalyco/tap

# npm 方式
npm uninstall -g opencode-ai

# 二进制方式
sudo rm /usr/local/bin/opencode

# 清理配置文件（可选）
rm -rf ~/.config/opencode
rm -rf ~/.local/share/opencode
```

## 故障排除

### 常见问题

#### macOS 安全提示 "无法验证开发者"

当从二进制文件安装时，macOS 的 Gatekeeper 可能会阻止运行：

```bash
# 手动绕过安全验证
sudo xattr -rd com.apple.quarantine /usr/local/bin/opencode

# 或前往 系统设置 > 隐私与安全性，点击"仍要打开"
```

#### `brew install` 失败

```bash
# 更新 Homebrew
brew update && brew upgrade

# 重试安装
brew install anomalyco/tap/opencode
```

#### 终端显示乱码

确保您的终端支持 UTF-8 编码：

```bash
# 检查 locale 设置
locale

# 设置 UTF-8（如需要）
export LANG=en_US.UTF-8
export LC_ALL=en_US.UTF-8
```

### 获取帮助

- 运行 `opencode --help` 查看帮助信息
- 访问 [GitHub Issues](https://github.com/anomalyco/opencode/issues) 报告问题
- 加入社区讨论获取支持

## 下一步

安装完成后，请参阅 [快速入门](../usage/quick-start.md) 开始使用 OpenCode。
