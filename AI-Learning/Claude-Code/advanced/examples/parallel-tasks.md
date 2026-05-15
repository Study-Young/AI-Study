# 实战示例：并行任务处理 (Parallel Tasks)

本文档演示如何使用 Claude Code 高效处理多个并行任务，包括批量文件处理、多模块并行开发、自动化测试和 CI/CD 流水线优化。

## 场景概述

Claude Code 虽然本身是单线程执行，但可以通过策略性地拆分任务、利用 shell 并行机制和独立调用来实现并行处理，大幅提升工作效率。

## 策略一：Shell 级并行

利用 shell 的后台进程和并行工具同时运行多个 Claude Code 实例。

### 使用 & 后台运行

```bash
# 同时运行多个审查任务
claude -p "审查 src/module-a.ts 的代码质量" --max-turns 10 --output-format json > report-a.json &
claude -p "审查 src/module-b.ts 的代码质量" --max-turns 10 --output-format json > report-b.json &
claude -p "审查 src/module-c.ts 的代码质量" --max-turns 10 --output-format json > report-c.json &

# 等待所有任务完成
wait

# 合并报告
echo "审查完成，生成汇总报告..."
cat report-a.json report-b.json report-c.json | claude -p "合并以下代码审查报告并生成总结" \
  --max-turns 5
```

### 使用 GNU Parallel

```bash
# 安装 GNU Parallel
sudo apt-get install parallel  # Ubuntu/Debian
# brew install parallel        # macOS

# 并行审查多个文件
cat << 'EOF' > files-to-review.txt
src/module-a.ts
src/module-b.ts
src/module-c.ts
src/module-d.ts
src/module-e.ts
EOF

# 使用 parallel 并行执行
cat files-to-review.txt | parallel -j 3 \
  "claude -p '详细审查 {} 的代码质量、安全性和性能' \
   --max-turns 10 \
   --output-format json \
   > {}.review.json"

echo "所有文件审查完成"
```

## 策略二：拆分任务为独立子任务

将大型任务拆分为多个独立的子任务，并行提交给 Claude Code。

### 多模块代码生成

```bash
#!/bin/bash
# 并行生成多个 API 路由文件

# 定义任务
TASKS=(
  "创建用户 CRUD API 路由，包含 GET/POST/PUT/DELETE 方法"
  "创建文章 CRUD API 路由，包含分页和搜索功能"
  "创建评论 CRUD API 路由，包含评论审核功能"
  "创建标签管理 API 路由，包含标签合并功能"
)

# 设置输出目录
OUTPUT_DIR="src/routes"
mkdir -p "$OUTPUT_DIR"

# 并行执行
for i in "${!TASKS[@]}"; do
  task="${TASKS[$i]}"
  output_file="${OUTPUT_DIR}/route-${i}.ts"

  claude -p "$task，将代码写入 $output_file" \
    --max-turns 15 \
    --temperature 0.2 &
done

# 等待所有任务完成
wait
echo "所有 API 路由文件已生成"
```

### 并行文档生成

```bash
#!/bin/bash
# 为多个模块并行生成文档

MODULES=(
  "src/services/auth.service.ts"
  "src/services/user.service.ts"
  "src/services/order.service.ts"
  "src/services/payment.service.ts"
)

generate_docs() {
  local file=$1
  local module_name=$(basename "$file" .ts)
  local output="docs/${module_name}.md"

  claude -p "读取 $file 的源代码，为该模块生成完整的 API 文档：
- 模块概述和用途
- 所有导出函数的详细文档
- 参数说明和类型定义
- 使用示例代码
- 错误处理说明

输出 Markdown 格式文档到 $output" \
    --max-turns 10 \
    --temperature 0.1
}

# 并行生成文档
for module in "${MODULES[@]}"; do
  generate_docs "$module" &
done

wait
echo "所有模块文档已生成"
```

## 策略三：分阶段流水线

设计分阶段的并行处理流水线，前一阶段的输出作为后一阶段输入。

```bash
#!/bin/bash
# 完整项目开发流水线

echo "======= 第一阶段：代码分析与规划 ======="

# 阶段 1.1: 分析现有代码结构
claude -p "分析 src/ 目录的代码结构：
- 列出所有模块和它们之间的依赖关系
- 识别公共接口和类型
- 输出项目结构图" \
  --output-format json > phase1-structure.json &

# 阶段 1.2: 分析测试覆盖率
claude -p "分析项目测试：
- 检查测试文件覆盖率
- 找出缺少测试的模块
- 给出测试优先级建议" \
  --output-format json > phase1-test-coverage.json &

wait
echo "阶段 1 完成"

echo "======= 第二阶段：并行开发 ======="

# 阶段 2.1: 根据分析结果生成代码
cat phase1-structure.json | claude -p "根据项目结构分析结果：
1. 为缺少接口定义的模块创建类型定义
2. 添加必要的注释和文档
3. 确保模块间接口一致性" \
  --max-turns 20 --temperature 0.1 &

# 阶段 2.2: 生成测试代码
cat phase1-test-coverage.json | claude -p "根据测试覆盖率分析：
1. 为覆盖率低的模块生成单元测试
2. 使用项目中已有的测试框架
3. 包含边界条件测试" \
  --max-turns 20 --temperature 0.2 &

wait
echo "阶段 2 完成"

echo "======= 第三阶段：验证与合并 ======="

# 阶段 3: 运行测试并修复问题
claude -p "审查所有更新的代码：
1. 运行测试套件：$(test_command)
2. 分析测试结果
3. 修复失败的测试
4. 确保所有测试通过" \
  --max-turns 30 --temperature 0.1

echo "流水线全部完成！"
```

## 策略四：自动化测试并行执行

```bash
#!/bin/bash
# 并行生成和运行测试

# 创建测试任务文件
cat << 'TASKS' > test-tasks.json
[
  {
    "file": "src/utils/format.ts",
    "testFile": "tests/utils/format.test.ts",
    "description": "测试格式化工具函数"
  },
  {
    "file": "src/utils/validation.ts",
    "testFile": "tests/utils/validation.test.ts",
    "description": "测试数据验证函数"
  },
  {
    "file": "src/utils/date.ts",
    "testFile": "tests/utils/date.test.ts",
    "description": "测试日期处理函数"
  },
  {
    "file": "src/utils/crypto.ts",
    "testFile": "tests/utils/crypto.test.ts",
    "description": "测试加密工具函数"
  }
]
TASKS

# 使用 Claude Code 拆分任务
claude -p "读取 test-tasks.json 中的测试任务列表。
为每个任务创建对应的测试文件。
确保：
1. 测试覆盖率覆盖所有主要功能
2. 包含正常路径和异常路径
3. 使用项目中已有的测试框架和约定
4. 每个测试文件可以独立运行" \
  --max-turns 30

# 并行运行测试
echo "并行运行测试..."
npx jest --shard=1/4 &
npx jest --shard=2/4 &
npx jest --shard=3/4 &
npx jest --shard=4/4 &
wait
echo "所有测试完成"
```

## 策略五：批量文件处理

```bash
#!/bin/bash
# 批量处理文件（例如：添加国际化支持）

# 获取所有需要处理的组件文件
COMPONENTS=($(find src/components -name "*.tsx" -type f))

echo "发现 ${#COMPONENTS[@]} 个组件需要处理"

# 分批并行处理（每次 3 个）
BATCH_SIZE=3
for ((i=0; i<${#COMPONENTS[@]}; i+=BATCH_SIZE)); do
  batch=("${COMPONENTS[@]:i:BATCH_SIZE}")

  echo "处理批次 $((i/BATCH_SIZE+1)): ${batch[*]}"

  for file in "${batch[@]}"; do
    claude -p "处理文件 $file：
1. 提取所有中文字符串
2. 替换为 i18n 调用
3. 在对应的语言文件中添加翻译键

保持原有代码结构和逻辑不变" \
      --max-turns 15 \
      --temperature 0.1 &
  done

  # 等待当前批次完成
  wait
  echo "批次 $((i/BATCH_SIZE+1)) 完成"
done

echo "所有组件处理完成"
```

## 策略六：使用 Makefile 组织并行任务

创建 `Makefile`：

```makefile
.PHONY: all clean lint test build docs review

# 并行执行所有任务
all: lint test build docs review

# 代码检查（并行）
lint:
	claude -p "检查 src/ 目录下的 ESLint 配置是否正确，并对所有 TypeScript 文件进行静态分析" \
		--output-format json --max-turns 10 &
	claude -p "检查样式文件是否符合 Prettier 规范" \
		--output-format json --max-turns 5 &
	wait

# 生成测试（并行）
test:
	@echo "并行生成测试文件..."
	@for file in $(shell find src/utils -name "*.ts" -type f); do \
		claude -p "为 $$file 编写 Jest 单元测试" \
			--max-turns 8 --temperature 0.1 & \
	done
	@wait
	@npx jest --parallel

# 生成文档（并行）
docs:
	claude -p "为 src/api/ 下的所有路由文件生成 OpenAPI 文档" \
		--max-turns 20 --output-format json > docs/openapi.json &
	claude -p "为 src/services/ 下的所有服务生成 Markdown 文档" \
		--max-turns 20 > docs/services.md &
	wait

# 构建
build:
	npm run build

# 代码审查（并行）
review:
	@for dir in src/services src/routes src/utils; do \
		claude -p "审查 $$dir 目录的代码质量和安全性" \
			--max-turns 15 --output-format json > review-$$(basename $$dir).json & \
	done
	@wait
	@cat review-*.json | claude -p "合并审查报告，生成总体质量评估" --max-turns 5
```

```bash
# 使用 Makefile
make all         # 运行所有任务
make review      # 仅运行审查
make docs        # 仅生成文档
make test        # 仅生成和运行测试
```

## 性能优化建议

### 1. 控制并行度

```bash
# 根据 CPU 核心数控制并行度
CPU_CORES=$(nproc)
MAX_PARALLEL=$((CPU_CORES / 2))  # 每个任务约使用半个核心
echo "并行度设置为: $MAX_PARALLEL"
```

### 2. 合理设置 max-turns

```bash
# 简单任务：较小的 max-turns
claude -p "格式化代码" --max-turns 3 &

# 复杂任务：较大的 max-turns
claude -p "重构整个模块" --max-turns 30 &
```

### 3. 使用轻量模型

```bash
# 简单的格式化任务使用 Haiku 模型
claude -p "格式化 src/*.ts" --model claude-haiku-3-5-20241022 &

# 复杂的重构任务使用 Sonnet 或 Opus
claude -p "重构核心算法" --model claude-sonnet-4-20250514 &
```

### 4. 监控资源使用

```bash
#!/bin/bash
# 监控并行任务的资源使用

# 启动后台任务
claude -p "任务1" &
PID1=$!
claude -p "任务2" &
PID2=$!
claude -p "任务3" &
PID3=$!

# 监控模式
echo "监控并行任务..."
while kill -0 $PID1 2>/dev/null || kill -0 $PID2 2>/dev/null || kill -0 $PID3 2>/dev/null; do
  echo "--- $(date) ---"
  ps -p $PID1,$PID2,$PID3 -o pid,%cpu,%mem,command 2>/dev/null || true
  sleep 5
done

wait
echo "所有任务完成"
```

## 最佳实践总结

| 策略 | 适用场景 | 优点 | 注意事项 |
|------|----------|------|----------|
| Shell 后台进程 | 独立的文件处理任务 | 实现简单 | 需要手动管理进程 |
| GNU Parallel | 批量文件处理 | 自动管理并行度 | 需要安装 parallel |
| 任务拆分 | 大型项目开发 | 充分利用 Claude Code 能力 | 需要合理规划任务粒度 |
| 流水线 | 多阶段开发流程 | 结构清晰，可重复执行 | 阶段间可能有依赖 |
| Makefile | 重复性开发任务 | 标准化团队工作流 | 需要维护 Makefile |
