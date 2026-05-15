# Linux / WSL 安装指南

本指南介绍如何在 Linux 桌面或 Windows WSL（Windows Subsystem for Linux）环境中安装 Hermes Agent。

## 系统要求

- **操作系统**：Ubuntu 20.04+、Debian 11+、CentOS 8+、Fedora 36+ 或其他主流 Linux 发行版
- **WSL**：Windows 10 版本 2004+（Build 19041+）或 Windows 11
- **Python**：3.10 或更高版本
- **curl**：用于下载安装脚本
- **内存**：至少 2GB 可用内存
- **磁盘空间**：至少 500MB 可用空间

## 安装步骤

### 1. 一键安装

使用官方安装脚本进行安装：

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

安装脚本会自动完成以下操作：
- 检测系统环境
- 安装 Python 依赖（如需要）
- 下载 Hermes Agent 二进制文件
- 创建配置文件目录

### 2. 验证安装

安装完成后，验证是否安装成功：

```bash
hermes --version
```

如果显示版本号，说明安装成功。

### 3. 运行设置向导

```bash
hermes setup
```

设置向导会引导您：
- 选择 LLM 提供商（OpenAI、Anthropic、OpenRouter、DeepSeek 等）
- 输入 API 密钥
- 配置默认模型
- 设置平台连接（可选）

### 4. 配置 API 密钥

编辑 `~/.hermes/.env` 文件，添加您的 API 密钥：

```env
OPENAI_API_KEY=sk-your-openai-key
ANTHROPIC_API_KEY=sk-ant-your-anthropic-key
OPENROUTER_API_KEY=sk-or-your-openrouter-key
DEEPSEEK_API_KEY=sk-ds-your-deepseek-key
```

### 5. 启动聊天

```bash
hermes chat
```

## WSL 特有配置

### 启用 WSL

如果尚未启用 WSL，以管理员身份运行 PowerShell：

```powershell
wsl --install
```

安装完成后重启计算机。

### 推荐发行版

- **Ubuntu 22.04 LTS** — 最稳定的选择
- **Debian 12** — 轻量级选择
- **Arch Linux** — 滚动更新

### 文件系统

WSL 下注意以下路径映射：
- Linux 文件：`/home/<用户名>/`
- Windows 文件：`/mnt/c/Users/<用户名>/`

建议将 Hermes Agent 配置和数据存储在 Linux 文件系统中（而非 `/mnt/c/`），以获得更好的性能。

### 图形界面支持

Hermes Agent 的 CLI 模式在 WSL 中完全支持。如需使用语音功能，可能需要额外配置 PulseAudio 或 PipeWire。

## 常见问题

### Q: 安装脚本执行失败

A: 确保已安装 curl 和 Python 3.10+，尝试手动安装依赖：
```bash
sudo apt update && sudo apt install -y curl python3 python3-pip
```

### Q: 找不到 hermes 命令

A: 安装脚本会将 hermes 添加到 `~/.local/bin/`，请确保该目录在 PATH 中：
```bash
export PATH="$HOME/.local/bin:$PATH"
```
并将此行添加到 `~/.bashrc` 或 `~/.zshrc`。

### Q: WSL 网络问题

A: 如果 WSL 中无法连接外部 API，检查 DNS 配置：
```bash
sudo nano /etc/resolv.conf
# 添加 nameserver 8.8.8.8
```

## 下一步

- [快速上手](../usage/quick-start.md) — 学习基本用法
- [CLI 参考](../usage/cli-reference.md) — 查看所有命令
- [斜杠命令](../usage/slash-commands.md) — 了解内置斜杠命令
