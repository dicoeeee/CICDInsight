---
title: MCP 专题实验计划
tags:
  - research/agentic-cicd
  - research/lab
  - tool/mcp
status: proposed
as_of: 2026-07-15
---

# MCP 专题实验计划

本轮完成实验设计，未把未执行结果混入报告。

| 实验 | 变量 | 指标 | 目的 |
|---|---|---|---|
| M1：Toolset Size | 10/50/100 个 Tool | 选择准确率、提示 Token、延迟 | 验证 Tool 爆炸阈值 |
| M2：Direct CLI vs MCP | 同一底层 CLI | 首次成功、接入工时、延迟、可审计性 | 衡量协议增量价值 |
| M3：stdio vs Remote HTTP | 同一 Server | 启动、并发、升级、身份、故障恢复 | 验证部署模式 |
| M4：恶意 Server/Description | 注入、Schema 漂移、返回污染 | 拦截率、越权率、审计 | 验证 Host 信任边界 |
| M5：Scope/Allowlist | 不同任务身份与环境 | 最小权限、拒绝质量、误配置 | 验证企业控制面 |

执行时必须固定客户端、Server、模型和规范版本，并将 Tool Schema、权限、原始调用与产物保存为独立证据。
