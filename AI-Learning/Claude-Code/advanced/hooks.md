# 钩子 (Hooks)

Claude Code 的钩子系统（Hooks）允许您在 Claude 的工具调用生命周期的不同阶段注入自定义逻辑。通过钩子，您可以实现安全检查、日志记录、数据验证、权限控制等功能。

## 钩子类型

Claude Code 支持三种类型的钩子：

| 钩子类型 | 触发时机 | 用途 |
|----------|----------|------|
| **PreToolUse** | 工具调用**前** | 验证输入、安全检查、权限控制、修改参数 |
| **PostToolUse** | 工具调用**后** | 验证输出、记录日志、转换结果、数据清洗 |
| **Stop** | 会话**结束时** | 清理资源、生成摘要、发送通知 |

## 钩子生命周期

```
用户输入
    │
    ▼
┌──────────────┐
│  PreToolUse  │ ← 在此可以拦截、验证或修改工具调用
│    钩子(x N)  │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│   工具执行    │ ← Claude 实际调用工具（Read/Write/Bash 等）
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ PostToolUse  │ ← 在此可以处理工具调用的结果
│    钩子(x N)  │
└──────┬───────┘
       │
       ▼
   继续/完成
       │
       ▼
┌──────────────┐
│    Stop      │ ← 会话结束时触发
│    钩子       │
└──────────────┘
```

## 配置钩子

钩子通过 `.claude/hooks/` 目录中的可执行脚本配置。每个钩子类型对应一个特定命名的脚本文件。

### 目录结构

```
项目根目录/
├── .claude/
│   ├── hooks/
│   │   ├── PreToolUse   ← 工具调用前置钩子（可执行文件）
│   │   ├── PostToolUse  ← 工具调用后置钩子（可执行文件）
│   │   └── Stop         ← 会话结束钩子（可执行文件）
│   ├── agents/
│   │   └── ...
│   └── CLAUDE.md
```

### 钩子脚本要求

- 必须是**可执行文件**
- 可以是任何脚本语言（bash、Python、Node.js、Ruby 等）
- 通过 stdin/stdout 与 Claude Code 通信（JSON 格式）
- 脚本必须返回合法的 JSON 响应

## PreToolUse 钩子

在每次 Claude 调用工具之前触发。用于验证和拦截工具调用。

### 输入格式

```json
{
  "tool_name": "Read",
  "input": {
    "file_path": "src/config.json"
  },
  "type": "PreToolUse"
}
```

### 输出格式

```json
// 允许调用（修改参数）
{
  "input": {
    "file_path": "src/config.json",
    "encoding": "utf-8"
  }
}

// 拒绝调用
{
  "error": "不允许读取此文件：包含敏感信息"
}
```

### 示例：安全检查钩子

创建 `.claude/hooks/PreToolUse`（使用 bash）：

```bash
#!/bin/bash
# PreToolUse 钩子：防止读取或修改敏感文件

# 读取输入
input=$(cat)

# 提取工具名称和参数
tool_name=$(echo "$input" | jq -r '.tool_name')
file_path=$(echo "$input" | jq -r '.input.file_path // .input.file_path // ""')

# 定义敏感文件模式
SENSITIVE_PATTERNS=(
  ".env"
  ".env.*"
  "*.pem"
  "*.key"
  "credentials.json"
  "secrets.*"
  "id_rsa*"
  ".docker/config.json"
  "service-account.json"
  "*.cert"
  "*.p12"
)

# 检查是否为敏感文件
is_sensitive() {
  local path="$1"
  for pattern in "${SENSITIVE_PATTERNS[@]}"; do
    # 使用 shell glob 匹配
    case "$path" in
      *$pattern) return 0 ;;
    esac
  done
  return 1
}

# 只检查涉及文件路径的工具
if [[ "$tool_name" == "Read" || "$tool_name" == "Write" || "$tool_name" == "Edit" ]]; then
  if is_sensitive "$file_path"; then
    # 拒绝访问敏感文件
    echo '{"error": "安全拦截：不允许访问敏感文件 '"$file_path"'\n请联系项目管理员获取访问权限"}'
    exit 0
  fi
fi

# 没有拦截，原样通过
echo "$input"
```

### 示例：命令白名单钩子

创建 `.claude/hooks/PreToolUse`（使用 Python）：

```python
#!/usr/bin/env python3
"""PreToolUse 钩子：Bash 命令白名单"""
import json
import sys

# 允许的命令前缀
ALLOWED_COMMANDS = [
    "npm ", "yarn ", "pnpm ", "npx ",
    "git ", "python", "node ", "tsc ",
    "make ", "cmake ",
    "ls ", "cat ", "head ", "tail ", "grep ",
    "mkdir ", "cp ", "mv ", "rm ",
    "cd ", "pwd ", "echo ",
    "docker ", "kubectl ",
    "curl ", "wget ",
]

# 禁止的命令
BLOCKED_COMMANDS = [
    "sudo ", "su ", "chmod 777", "chown ",
    "rm -rf /", "rm -rf ~",
    ":(){ :|:& };:",  # fork bomb
    "dd if=", "mkfs.", "fdisk",
    "> /dev/", "> /dev/sd",
]

def check_command(command):
    """检查命令是否允许执行"""
    command_stripped = command.strip()

    # 检查禁止命令
    for blocked in BLOCKED_COMMANDS:
        if command_stripped.startswith(blocked):
            return False, f"禁止执行危险命令: {blocked}"

    # 检查允许命令
    for allowed in ALLOWED_COMMANDS:
        if command_stripped.startswith(allowed):
            return True, ""

    # 不在白名单中
    return False, f"命令不在白名单中: {command_stripped.split()[0] if command_stripped.split() else ''}"

def main():
    data = json.load(sys.stdin)

    if data.get("tool_name") == "Bash":
        command = data.get("input", {}).get("command", "")
        allowed, reason = check_command(command)
        if not allowed:
            result = {"error": reason}
            print(json.dumps(result))
            return

    # 通过验证
    print(json.dumps({"input": data.get("input", {})}))

if __name__ == "__main__":
    main()
```

## PostToolUse 钩子

在每次 Claude 工具调用完成后触发。用于处理工具执行结果。

### 输入格式

```json
{
  "tool_name": "Bash",
  "input": {
    "command": "npm test"
  },
  "output": "PASS: 42 tests passed, 0 failed",
  "type": "PostToolUse"
}
```

### 输出格式

```json
// 修改输出内容
{
  "output": "测试结果摘要：全部通过"
}

// 原样传递
{
  "output": "PASS: 42 tests passed, 0 failed"
}
```

### 示例：日志记录钩子

创建 `.claude/hooks/PostToolUse`（使用 Node.js）：

```javascript
#!/usr/bin/env node
/**
 * PostToolUse 钩子：记录所有工具调用到日志文件
 */
const fs = require('fs');
const path = require('path');

const chunks = [];
process.stdin.on('data', (chunk) => chunks.push(chunk));
process.stdin.on('end', () => {
    const input = JSON.parse(Buffer.concat(chunks).toString());

    // 构建日志条目
    const logEntry = {
        timestamp: new Date().toISOString(),
        tool: input.tool_name,
        input: input.input,
        output: input.output ? input.output.substring(0, 500) : null,
        output_truncated: input.output && input.output.length > 500
    };

    // 写入日志文件
    const logDir = path.join(process.cwd(), '.claude', 'logs');
    if (!fs.existsSync(logDir)) {
        fs.mkdirSync(logDir, { recursive: true });
    }

    const logFile = path.join(logDir, 'tool-calls.log');
    fs.appendFileSync(logFile, JSON.stringify(logEntry) + '\n');

    // 透传输出
    console.log(JSON.stringify({ output: input.output }));
});
```

### 示例：输出清理钩子

创建 `.claude/hooks/PostToolUse`（使用 Python）：

```python
#!/usr/bin/env python3
"""PostToolUse 钩子：清理输出中的敏感信息"""
import json
import sys
import re

# 敏感信息模式
SENSITIVE_PATTERNS = [
    (r'(?i)(api[_-]?key["\s:=]+)["\']?[A-Za-z0-9_\-]{20,}', r'\1[REDACTED]'),
    (r'(?i)(secret["\s:=]+)["\']?[A-Za-z0-9_\-]{20,}', r'\1[REDACTED]'),
    (r'(?i)(password["\s:=]+)["\']?[^\s"\']{8,}', r'\1[REDACTED]'),
    (r'(?i)(token["\s:=]+)["\']?[A-Za-z0-9_\-\.]{20,}', r'\1[REDACTED]'),
    (r'(?i)(sk-ant-[a-zA-Z0-9]+)', '[API-KEY-REDACTED]'),
]

def clean_output(text):
    """清理输出中的敏感信息"""
    if not text:
        return text
    for pattern, replacement in SENSITIVE_PATTERNS:
        text = re.sub(pattern, replacement, text)
    return text

def main():
    data = json.load(sys.stdin)

    if data.get("output"):
        data["output"] = clean_output(data["output"])

    print(json.dumps({"output": data.get("output", "")}))

if __name__ == "__main__":
    main()
```

## Stop 钩子

在 Claude Code 会话结束时触发。用于执行清理操作。

### 输入格式

```json
{
  "type": "Stop"
}
```

### 输出格式

```json
// 成功
{}

// 报告错误
{
  "error": "清理过程出现问题"
}
```

### 示例：会话总结钩子

创建 `.claude/hooks/Stop`：

```bash
#!/bin/bash
# Stop 钩子：生成会话总结报告

# 获取会话统计
TOOL_CALLS=$(wc -l < .claude/logs/tool-calls.log 2>/dev/null || echo 0)
START_TIME=$(head -1 .claude/logs/tool-calls.log 2>/dev/null | jq -r '.timestamp' 2>/dev/null || echo "unknown")

# 生成总结
cat << EOF
=================================
  Claude Code 会话总结
=================================
  会话时间: $(date)
  工具调用次数: ${TOOL_CALLS}
  开始时间: ${START_TIME}
  日志文件: .claude/logs/tool-calls.log
=================================
EOF

# 返回成功
echo '{}'
```

## 钩子安全最佳实践

### 1. 设置正确的权限

```bash
# 钩子脚本必须可执行
chmod +x .claude/hooks/PreToolUse
chmod +x .claude/hooks/PostToolUse
chmod +x .claude/hooks/Stop
```

### 2. 验证输入

始终验证 `jq` 或 JSON 解析库的输入，防止注入攻击。

### 3. 最小权限原则

钩子脚本只应具有完成任务所需的最小权限。

### 4. 错误处理

```python
# Python 钩子中的错误处理示例
try:
    data = json.load(sys.stdin)
except json.JSONDecodeError as e:
    print(json.dumps({"error": f"JSON 解析错误: {str(e)}"}))
    sys.exit(0)  # 不要退出非零，否则会影响 Claude Code
```

### 5. 性能考虑

钩子应尽量轻量，因为每次工具调用都会触发。避免在钩子中进行耗时的操作。

### 6. 调试钩子

```bash
# 在钩子中输出调试信息到文件
echo "DEBUG: tool=$tool_name path=$file_path" >> .claude/hooks/debug.log
```

## 完整示例：企业安全钩子集合

```bash
.claude/hooks/
├── PreToolUse      # → 安全检查 + 命令白名单 + 审计日志
├── PostToolUse     # → 敏感信息过滤 + 结果验证
└── Stop            # → 会话总结 + 合规报告
```

这个组合可以确保：
1. 不会访问或修改敏感文件
2. 不会执行危险命令
3. 敏感 API Key 不会被打印到输出
4. 所有操作被记录以便审计
5. 会话结束时生成合规报告
