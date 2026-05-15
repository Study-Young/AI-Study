# Windows 安装指南

本文档介绍在 Windows 系统上安装 OpenCode 的多种方法。

## 系统要求

- **操作系统**: Windows 10 21H2+ 或 Windows 11
- **Node.js**: 18.0.0 或更高版本（使用 npm 安装时需要）
- **终端**: Windows Terminal、PowerShell 7+ 或 Command Prompt
- **Git**: 2.30 或更高版本（可选，用于部分高级功能）

## 安装方法

### 方法一：通过 npm 安装（推荐）

npm 是 Windows 上最推荐的安装方式。

```powershell
# 以管理员身份运行 PowerShell 或终端
npm i -g opencode-ai@latest

# 验证安装
opencode --version
```

#### 安装 Node.js

如果尚未安装 Node.js，请从 [nodejs.org](https://nodejs.org) 下载 LTS 版本：

1. 访问 https://nodejs.org
2. 下载 **LTS** 版本（如 22.x）
3. 运行安装程序，勾选"自动安装必要工具"
4. 安装完成后，重启终端

```powershell
# 验证 Node.js 安装
node --version
npm --version
```

### 方法二：通过二进制安装

从 [opencode.ai](https://opencode.ai) 或 [GitHub Releases 页面](https://github.com/anomalyco/opencode/releases) 下载预编译的 Windows 二进制文件。

1. 下载 `opencode-windows-x86_64.zip`
2. 解压到您选择的目录（例如 `C:\Program Files\opencode`）
3. 将该目录添加到系统 PATH 环境变量：
   - 打开 **系统属性** > **高级** > **环境变量**
   - 在 **系统变量** 中找到 `Path`，点击 **编辑**
   - 点击 **新建**，输入解压目录路径（如 `C:\Program Files\opencode`）
   - 点击 **确定** 保存
4. 重启终端

```powershell
# 验证安装
opencode --version
```

### 方法三：通过 Scoop 安装（高级用户）

如果您使用 [Scoop](https://scoop.sh) 包管理器：

```powershell
# 安装 Scoop（如尚未安装）
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
irm get.scoop.sh | iex

# 添加存储库并安装
scoop bucket add anomalyco https://github.com/anomalyco/scoop-bucket
scoop install opencode
```

### 方法四：通过 WSL 安装（Windows 子系统 for Linux）

如果您更倾向于 Linux 环境，可以在 WSL 中安装：

```bash
# 在 WSL 终端中
npm i -g opencode-ai@latest

# 或使用 Homebrew（如果已安装）
brew install anomalyco/tap/opencode
```

> **提示**: 在 WSL 中安装后，您可以从 Windows 侧通过 `wsl opencode` 调用。

## 安装后配置

### 认证

安装完成后，您需要通过以下方式之一进行认证：

```powershell
# 交互式登录（推荐）
opencode auth login

# 或设置环境变量（PowerShell）
$env:OPENAI_API_KEY = "sk-your-key-here"
$env:ANTHROPIC_API_KEY = "sk-ant-your-key-here"
$env:OPENROUTER_API_KEY = "sk-or-your-key-here"
```

**永久设置环境变量**（PowerShell）：

```powershell
# 为当前用户设置永久环境变量
[System.Environment]::SetEnvironmentVariable('OPENAI_API_KEY', 'sk-your-key-here', 'User')

# 或通过系统设置
# 打开 系统属性 > 高级 > 环境变量 > 新建
```

### Windows Terminal 集成（可选）

OpenCode 的 TUI 模式在 Windows Terminal 中体验最佳。建议：

1. 从 Microsoft Store 安装 [Windows Terminal](https://apps.microsoft.com/detail/9n0dx20hk701)
2. 设置为默认终端应用程序
3. 推荐使用 PowerShell 7+ 或 Nerd Font 以获得最佳图标显示效果

## 卸载

```powershell
# npm 方式
npm uninstall -g opencode-ai

# Scoop 方式
scoop uninstall opencode

# 二进制方式
# 删除解压目录并从 PATH 中移除

# 清理配置文件（可选）
rm -r $env:USERPROFILE\.config\opencode
rm -r $env:USERPROFILE\.local\share\opencode
```

## 故障排除

### 常见问题

#### `opencode` 不被识别为命令

```powershell
# 检查 npm 全局安装路径是否在 PATH 中
npm root -g
# 通常为 C:\Users\<用户名>\AppData\Roaming\npm\node_modules

# 确保该路径下的 ..\npm 目录（包含 opencode.cmd）在 PATH 中
# 或在 PowerShell 中直接添加
$env:Path += ";$env:APPDATA\npm"
```

#### 权限错误

以管理员身份运行 PowerShell：

```powershell
# 右键点击 PowerShell 图标，选择"以管理员身份运行"
```

#### 终端显示问题

OpenCode 的 TUI 需要 ANSI 转义序列支持：

```powershell
# 确保使用 Windows Terminal 或 PowerShell 7+
# 较旧的 Command Prompt 可能无法完整显示 TUI

# 在 PowerShell 7+ 中启用虚拟终端
$PSStyle.OutputRendering = 'Host'
```

#### 防病毒软件报警

某些防病毒软件可能对新的二进制文件产生误报。如果出现此情况：

- 添加路径到防病毒排除列表
- 优先使用 npm 安装方式

### 获取帮助

- 运行 `opencode --help` 查看帮助信息
- 访问 [GitHub Issues](https://github.com/anomalyco/opencode/issues) 报告问题
- 加入社区讨论获取支持

## 下一步

安装完成后，请参阅 [快速入门](../usage/quick-start.md) 开始使用 OpenCode。
