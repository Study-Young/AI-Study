# macOS 安装指南

本指南介绍如何在 macOS 上安装 Hermes Agent。

## 系统要求

- **macOS 版本**：macOS 12 (Monterey) 或更高版本
- **处理器**：Intel 或 Apple Silicon (M1/M2/M3/M4)
- **Python**：3.10 或更高版本
- **curl**：系统自带
- **内存**：至少 2GB 可用内存
- **磁盘空间**：至少 500MB 可用空间

## 安装步骤

### 1. 一键安装

使用官方安装脚本进行安装：

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

安装脚本会自动检测 macOS 环境并完成安装。

### 2. 验证安装

```bash
hermes --version
```

### 3. 运行设置向导

```bash
hermes setup
```

设置向导会引导您配置 LLM 提供商和 API 密钥。

### 4. 配置 API 密钥

编辑 `~/.hermes/.env` 文件，添加您的 API 密钥：

```env
OPENAI_API_KEY=sk-your-openai-key
ANTHROPIC_API_KEY=sk-ant-your-anthropic-key
OPENROUTER_API_KEY=sk-or-your-openrouter-key
```

### 5. 启动聊天

```bash
hermes chat
```

## Apple Silicon (M 系列) 注意事项

### 原生支持

Hermes Agent 完全支持 Apple Silicon（arm64 架构），安装脚本会自动下载对应架构的二进制文件。

### Python 版本

建议使用原生 arm64 版本的 Python。如果通过 Rosetta 2 运行 x86_64 Python，可能会遇到性能问题。

```bash
# 检查 Python 架构
python3 -c "import platform; print(platform.machine())"
# 应输出 arm64
```

### Homebrew 用户

如果已安装 Homebrew，可以使用它来管理 Python：

```bash
brew install python@3.11
```

## 可选依赖

### 语音功能（可选）

如需使用语音输入/输出功能，安装以下依赖：

```bash
# 使用 Homebrew 安装 ffmpeg
brew install ffmpeg

# 安装音频处理库
pip install sounddevice pyaudio
```

### 麦克风权限

如果使用语音功能，需要授予终端应用麦克风权限：
1. 打开 **系统设置 > 隐私与安全性 > 麦克风**
2. 启用终端应用的麦克风访问权限

## 配置编辑器

### 编辑配置文件

可以使用任意文本编辑器编辑配置文件：

```bash
# 使用 Vim
vim ~/.hermes/config.yaml

# 使用 VS Code
code ~/.hermes/config.yaml

# 使用 Nano
nano ~/.hermes/config.yaml
```

## 卸载

如需卸载 Hermes Agent：

```bash
# 删除安装目录
rm -rf ~/.local/bin/hermes

# 删除配置和数据（谨慎操作）
rm -rf ~/.hermes
```

## 常见问题

### Q: "hermes: command not found"

A: 确保 `~/.local/bin` 在 PATH 中。将以下内容添加到 `~/.zshrc`（macOS 默认使用 zsh）：
```bash
export PATH="$HOME/.local/bin:$PATH"
```

### Q: Apple 安全提示

安装后首次运行可能遇到安全提示。前往 **系统设置 > 隐私与安全性**，点击"仍要打开"。

### Q: 权限错误

A: 如果遇到权限问题，确保拥有 `~/.hermes` 目录的写入权限：
```bash
chmod -R 755 ~/.hermes
```

### Q: 安装脚本卡住

A: 尝试设置代理或使用其他网络环境。在中国大陆的用户可能需要配置镜像源：
```bash
export HERMES_INSTALL_MIRROR=https://gitee.com/mirrors
```

## 下一步

- [快速上手](../usage/quick-start.md) — 学习基本用法
- [CLI 参考](../usage/cli-reference.md) — 查看所有命令
- [多平台部署](../usage/multi-platform.md) — 连接 Telegram/Discord 等
