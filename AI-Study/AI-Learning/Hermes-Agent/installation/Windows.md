# Windows 安装指南

本指南介绍如何在 Windows 上安装 Hermes Agent。

## 安装方式

Windows 用户推荐通过 **WSL（Windows Subsystem for Linux）** 使用 Hermes Agent。当然，也可以直接在 Windows 上通过 Python 安装。

---

## 方式一：通过 WSL 安装（推荐）

这是最佳使用方式，可以获得完整的 Linux 体验。

### 1. 安装 WSL

以管理员身份打开 PowerShell，运行：

```powershell
wsl --install
```

此命令会安装 WSL 2 和默认的 Ubuntu 发行版。安装完成后重启计算机。

### 2. 启动 WSL

```powershell
wsl
```

### 3. 在 WSL 中安装 Hermes Agent

进入 WSL 终端后，执行：

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

### 4. 配置

```bash
hermes setup
```

### 5. 使用

```bash
hermes chat
```

> **关于 WSL 的详细说明**，请参阅 [Linux / WSL 安装指南](Linux-WSL.md)。

---

## 方式二：直接在 Windows 上安装

### 系统要求

- **Windows 10** 版本 1809+ 或 **Windows 11**
- **Python** 3.10 或更高版本（[下载 Python](https://www.python.org/downloads/)）
- **Git**（可选，[下载 Git](https://git-scm.com/download/win)）

### 1. 安装 Python

从 [python.org](https://www.python.org/downloads/) 下载 Python 安装程序。

安装时务必勾选 **"Add Python to PATH"**（将 Python 添加到 PATH）。

验证安装：

```powershell
python --version
pip --version
```

### 2. 使用 pip 安装

```powershell
pip install hermes-agent
```

或从源码安装：

```powershell
git clone https://github.com/NousResearch/hermes-agent.git
cd hermes-agent
pip install -e .
```

### 3. 配置 API 密钥

创建配置文件 `%USERPROFILE%\.hermes\.env`：

```env
OPENAI_API_KEY=sk-your-openai-key
ANTHROPIC_API_KEY=sk-ant-your-anthropic-key
```

### 4. 启动聊天

```powershell
hermes chat
```

---

## 方式三：使用 Windows 终端 + PowerShell

如果不想使用 WSL 也不想直接安装 Python，可以使用 Windows 的便携版本：

### 1. 下载预编译二进制

从 [Hermes Agent Releases 页面](https://github.com/NousResearch/hermes-agent/releases) 下载 `hermes-windows-x64.exe`。

### 2. 添加到 PATH

将下载的 `.exe` 文件放在一个目录中（例如 `C:\tools\`），然后将该目录添加到系统 PATH：

1. 打开 **系统属性 > 环境变量**
2. 在"系统变量"中找到 `Path`，点击编辑
3. 添加 `C:\tools\`（或您的存放目录）
4. 点击确定保存

### 3. 配置

```powershell
hermes setup
```

### 4. 使用

```powershell
hermes chat
```

---

## Windows 终端推荐

建议使用以下终端以获得更好的体验：

- **Windows Terminal**（官方推荐）— 从 Microsoft Store 免费安装
- **PowerShell 7+** — 更现代的 PowerShell 版本
- **Tabby** — 跨平台终端模拟器

## 常见问题

### Q: "hermes 不是内部或外部命令"

A: 确保 Python 和 pip 已正确添加到 PATH，或已将 hermes 可执行文件所在目录添加到 PATH。

### Q: pip 安装失败

A: 尝试升级 pip 并安装构建工具：
```powershell
python -m pip install --upgrade pip
pip install wheel setuptools
pip install hermes-agent
```

### Q: Windows Defender 拦截

A: 某些杀毒软件可能误报。添加排除项或下载官方签名版本。

### Q: 中文显示乱码

A: 确保终端使用 UTF-8 编码：
```powershell
chcp 65001
```

## 下一步

- [快速上手](../usage/quick-start.md) — 学习基本用法
- [多平台部署](../usage/multi-platform.md) — 连接 Telegram/Discord 等
- [技能系统](../advanced/skills.md) — 学习创建自定义技能
