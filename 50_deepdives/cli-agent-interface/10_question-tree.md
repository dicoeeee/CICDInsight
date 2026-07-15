---
title: CLI 专题问题树
tags:
  - research/agentic-cicd
  - research/question-tree
  - tool/cli
status: complete
as_of: 2026-07-15
---

# CLI 专题问题树

## Q1：CLI 能力是什么

- 如何发现命令、参数和版本？
- 如何表达结构化输入、输出与错误？
- 如何处理会话状态、并发、副作用和长任务？
- OS 进程、容器和 Runner 能提供哪些天然隔离与重放能力？

## Q2：什么样的 CLI 才适合 Agent

- `--help` 是否稳定，还是只面向人类阅读？
- 是否支持 JSON、稳定字段、明确退出码和非交互模式？
- 是否有 dry-run、幂等键、超时、重试与取消语义？
- 凭据是否可按任务注入，日志是否泄露秘密？
- Agent 能否修改成功标准或绕过原有 Gate？

## Q3：CLI 与其他接口能否互相替代

- 与 API/SDK 相比，CLI 的包装成本和契约损失是什么？
- 与 GUI 相比，CLI 能否覆盖真实后端能力和视觉判断？
- 与 MCP 相比，能力发现、远程授权、资源语义、治理和上下文成本分别如何？
- 哪些场景适合 CLI 被 MCP Server 包装？

## Q4：2026 年行业向哪里发展

- 为什么终端 Agent/Harness 仍快速增长？
- 为什么 MCP 增长没有淘汰 CLI？
- Tool 数量、上下文税和供应链风险如何影响架构？
- 企业会把什么沉淀为共享平台能力？

## Q5：如何落到 Agentic CI/CD

- 八个阶段分别适合哪些命令与权限？
- PR-bound、Pipeline-bound、Plan-and-Approval、Runbook-bound 如何设计？
- 应如何评测任务成功、误操作、重放性和单位成本？
