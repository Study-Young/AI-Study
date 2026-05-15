# Linux 安装指南

本文档介绍在 Linux 系统上安装 OpenCode 的多种方法。

## 系统要求

- **操作系统**: Ubuntu 20.04+、Debian 11+、Fedora 36+、Arch Linux 或其他主流 Linux 发行版
- **Node.js**: 18.0.0 或更高版本（使用 npm 安装时需要）
- **终端**: 支持 ANSI 转义序列的现代终端模拟器
- **Git**: 2.30 或更高版本（可选，用于部分高级功能）

## 安装方法

### 方法一：通过 npm 安装（推荐）

这是最推荐的方式，适用于所有 Linux 发行版。

```bash
# 全局安装
npm i -g opencode-ai@latest

# 验证安装
opencode --version
```

> **注意**: 如果 `npm i -g` 遇到权限问题，建议使用 Node.js 版本管理器（如 nvm、fnm 或 n）来管理 Node.js 环境，避免使用 sudo。

#### 使用 nvm（推荐方式）

```bash
# 安装 nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash

# 重启终端或执行
source ~/.bashrc

# 安装 Node.js LTS
nvm install --lts

# 安装 OpenCode
npm i -g opencode-ai@latest
```

### 方法二：通过 Homebrew 安装

如果您已安装 Homebrew for Linux（Linuxbrew）：

```bash
brew install anomalyco/tap/opencode

# 验证安装
opencode --version
```

#### 安装 Linuxbrew

```bash
# 安装 Homebrew（Linuxbrew）
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 添加到 PATH
echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"' >> ~/.profile
eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"

# 安装 OpenCode
brew install anomalyco/tap/opencode
```

### 方法三：二进制下载

从 [opencode.ai](https://opencode.ai) 或 [GitHub Releases 页面](https://github.com/anomalyco/opencode/releases) 下载预编译的 Linux 二进制文件。

```bash
# 示例：下载并安装 Linux x86_64 版本
curl -L -o opencode.tar.gz "https://opencode.ai/downloads/latest/linux-x86_64.tar.gz"
tar xzf opencode.tar.gz
chmod +x opencode
sudo mv opencode /usr/local/bin/

# 验证安装
opencode --version
```

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

建议将环境变量添加到您的 shell 配置文件中（如 `~/.bashrc`、`~/.zshrc` 或 `~/.profile`）：

```bash
echo 'export OPENAI_API_KEY="sk-your-key-here"' >> ~/.bashrc
source ~/.bashrc
```

### Shell 自动补全（可选）

OpenCode 支持为常用 Shell 生成自动补全脚本：

```bash
# Bash
opencode completion bash > /etc/bash_completion.d/opencode
source ~/.bashrc

# Zsh
opencode completion zsh > "${fpath[1]}/_opencode"
autoload -U compinit && compinit

# Fish
opencode completion fish > ~/.config/fish/completions/opencode.fish
```

## 故障排除

### 常见问题

#### `npm i -g` 权限被拒绝

```bash
# 解决方案：使用 nvm 管理 Node.js
nvm install --lts
nvm use --lts
npm i -g opencode-ai@latest
```

#### 终端显示异常

确保您的终端模拟器支持 256 色和 Unicode：

```bash
# 检查终端能力
echo $TERM
# 应返回类似 xterm-256color 的值

# 设置终端类型
export TERM=xterm-256color
```

#### 找不到 opencode 命令

```bash
# 检查 npm 全局 bin 目录是否在 PATH 中
npm root -g
# 输出类似 /home/user/.nvm/versions/node/v22/lib/node_modules

# 确保 npm 全局 bin 在 PATH 中
export PATH="$(npm root -g)/../bin:$PATH"
```

### 获取帮助

- 运行 `opencode --help` 查看帮助信息
- 访问 [GitHub Issues](https://github.com/anomalyco/opencode/issues) 报告问题
- 加入社区讨论获取支持

## 下一步

安装完成后，请参阅 [快速入门](../usage/quick-start.md) 开始使用 OpenCode。
