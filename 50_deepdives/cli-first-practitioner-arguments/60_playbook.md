---
title: CLI-First 实践者论点企业 Playbook
tags:
  - research/agentic-cicd
  - research/playbook
  - tool/cli
  - tool/mcp
status: complete
as_of: 2026-08-06
---

# CLI-First 实践者论点企业 Playbook

> [!warning] 适用边界
> 本 Playbook 基于一线实践者的经验总结，主要适用于编码 Agent 和本地/Runner 自动化场景。企业级多客户端、远程服务和集中治理场景需要额外评估。

## 原则 1：默认 CLI 优先，MCP 需要证明增量价值

**决策标准：** 在引入 MCP Server 之前，先回答以下问题：

1. 是否有成熟 CLI 工具可以完成相同任务？
2. 模型训练数据是否已包含该 CLI 工具的知识？
3. 是否只有一个或少数 Agent Harness 使用该工具？
4. 工具是否运行在本地、开发容器或 CI Runner？
5. 是否不需要动态 Tool Discovery 或跨客户端 Schema 协商？

如果以上大部分答案为"是"，则直接使用 CLI，不引入 MCP。

**反例检查：** 只有当以下条件成立时，才应选择 MCP：
- 多个 Agent 客户端需要共享同一工具
- 需要远程 OAuth 隔离（API key 不暴露给 Agent）
- 需要集中 Registry/Allowlist/审计
- 需要 Resources/Prompts 等 MCP 特有语义

## 原则 2：测量上下文成本，不要假设

**实践方法：**

1. 在引入新工具前，测量其实际上下文消耗：
   - MCP Server：使用 MCP Inspector 或 Host 日志记录 `tools/list` 返回的 Schema token 数
   - CLI 工具：测量 `--help` 输出或实际使用的命令文档 token 数
2. 对比同一任务的两种实现（CLI vs MCP），记录：
   - 上下文 token 消耗
   - 首次成功率
   - P50/P95 延迟
   - 人工接管率
3. 没有对照数据时，选择架构复杂度最低的方案。

**参考数据点：**
- GitHub MCP: 23k-50k tokens (Peter Steinberger, Geoffrey Huntley)
- `gh` CLI: ~0 tokens（模型已知）
- 多个 MCP Server 累计: 55k+ tokens (Geoffrey Huntley)

## 原则 3：用脚本和 Makefile 替代 MCP Server

**实践模式：**

当需要封装内部工具或工作流时：

1. **优先写 shell 脚本**：放在 `scripts/` 目录，包含 `--help`、JSON 输出、退出码
2. **或加 Makefile 命令**：Armin Ronacher 模式——"instead of adding a new MCP tool, write a script or add a Makefile command"
3. **让 Agent 学习工具**：Geoffrey Huntley 模式——"tell them to run `--help` to learn how the tool"
4. **只有当脚本需要跨客户端共享时**，才考虑封装为 MCP Server

**示例：**

```bash
#!/bin/bash
# scripts/deploy-check.sh
# Usage: deploy-check.sh <environment> <version>
# Returns: JSON with deployment readiness status

set -euo pipefail

ENV="$1"
VERSION="$2"

# Check tests passed
TESTS=$(gh run list --workflow=ci.yml --json conclusion --jq '.[0].conclusion')
# Check build artifacts
ARTIFACT=$(aws s3api head-object --bucket builds --key "$VERSION.tar.gz" --query 'LastModified' --output text 2>/dev/null || echo "not found")

jq -n \
  --arg env "$ENV" \
  --arg version "$VERSION" \
  --arg tests "$TESTS" \
  --arg artifact "$ARTIFACT" \
  '{environment: $env, version: $version, tests_passed: ($tests == "success"), artifact_available: ($artifact != "not found")}'
```

Agent 可以通过 `scripts/deploy-check.sh --help` 学习用法，无需 MCP Tool Schema。

## 原则 4：安全边界在 OS 层，不在协议层

**实践方法：**

1. **不要依赖 MCP 的 Tool Description 作为安全边界**：它是 LLM 信任的输入，可以被注入
2. **使用 OS 级隔离**：
   - 容器/Runner 限定文件系统、网络、进程权限
   - 任务身份通过环境变量注入，不通过 MCP
   - 秘密不进 Agent 上下文，通过 wrapper 脚本处理
3. **避免 Lethal Trifecta**：
   - 私有数据访问、不可信内容处理、外部通信能力应分属不同进程/容器
   - 不要让单个 MCP Server 或 CLI 工具同时具备三要素
4. **审计在进程层**：
   - 记录每个 CLI 命令的调用参数、退出码、stdout/stderr
   - MCP 调用同样记录，但不依赖 MCP 协议自身的审计

## 原则 5：CLI-first + MCP 补充的混合架构

**推荐分层：**

```
Agent Harness
    ├── CLI 层（80-90% 操作）
    │   ├── 模型已知工具：gh, git, aws, gcloud, kubectl, terraform
    │   ├── 构建/测试工具：make, npm, pytest, go test
    │   ├── 自定义脚本：scripts/, Makefile
    │   └── 搜索/处理：rg, jq, awk, sed
    │
    └── MCP 层（10-20% 操作）
        ├── 认证网关：需要 OAuth 隔离的服务
        ├── 浏览器自动化：Playwright MCP
        ├── 特殊集成：设计工具、项目管理工具
        └── 跨客户端共享：多 Agent 共用的内部服务
```

**分工标准：**

| 场景 | 选择 | 理由 |
|---|---|---|
| GitHub 查询和操作 | `gh` CLI | 模型已知，零上下文成本 |
| 云资源管理 | `aws`/`gcloud`/`az` CLI | 模型已知，OS 级权限 |
| 构建和测试 | `make`/`npm`/`pytest` | 本地执行，可重放 |
| 内部 API 查询 | 自定义 CLI 或 curl | 简单直接 |
| 浏览器自动化 | Playwright MCP | CLI 不方便 |
| 多 Agent 共享服务 | Remote MCP Server | 统一发现和授权 |
| OAuth 隔离服务 | MCP with OAuth | API key 不进 Agent |

## 原则 6：验证优先，不要假设

**试点方法：**

1. 选择 5-10 个真实任务，覆盖 Observe/Propose/Verify/Change
2. 分别实现 CLI 版本和 MCP 版本
3. 测量：首次成功率、上下文 token、P50/P95 延迟、维护工时、人工接管率
4. 没有显著差异时，选择 CLI（更低复杂度）
5. 有显著差异时，用数据驱动决策

**持续评估：**

- 每季度回顾 MCP Server 使用情况，是否有可以替换为 CLI 的
- 关注 MCP 规范更新（渐进式加载、安全机制），可能改变成本收益
- 关注新 CLI 工具出现，可能替代现有 MCP Server
