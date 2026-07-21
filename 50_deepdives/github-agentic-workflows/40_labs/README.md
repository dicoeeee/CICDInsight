---
title: GitHub Agentic Workflows 实验计划
tags:
  - research/agentic-cicd
  - research/lab
  - company/github
status: proposed
as_of: 2026-07-15
---

# GitHub Agentic Workflows 实验计划

> [!warning] 证据边界
> 下列实验尚未执行，不把预期结果写入报告事实。

| ID | 实验 | 对照 / 注入 | 指标 | 目的 |
|---|---|---|---|---|
| GAW-L1 | Compile Reproducibility | 相同 `.md`、固定/浮动 gh-aw 版本 | Lock Diff、SHA Pin、编译时长 | 验证可重复构建与升级漂移 |
| GAW-L2 | Read-only + Safe Output | 直接写 GitHub、声明/未声明输出 | 写入拦截、Artifact、最终副作用 | 验证权限分阶段 |
| GAW-L3 | Prompt Injection | 恶意 Issue、PR、日志、MCP Description | Integrity Filter、Threat Detection、误放行 | 验证不可信输入边界 |
| GAW-L4 | CI Doctor | 10 类真实/合成 CI Failure | 根因准确率、PR 首次通过率、成本、时延 | 判断 CI 自愈适配度 |
| GAW-L5 | Deterministic vs Agentic | 纯脚本、纯 Agent、混合模式 | 成功率、波动、调试时间、成本 | 验证 DeterministicOps |
| GAW-L6 | MultiRepo Fan-out | 5/20/100 Repo；串行/并行 Worker | Token、Actions Minutes、失败隔离、补偿 | 验证 OrchestratorOps |
| GAW-L7 | CI Trigger Identity | 默认 Token、PAT、GitHub App | PR 后 CI 是否触发、审计 Actor、权限 | 验证写身份设计 |
| GAW-L8 | Version Revocation | 兼容、过旧、被阻止版本 | Activation 结果、升级恢复、MTTR | 验证 Preview 运营能力 |

每次实验保存 `.md`、`.lock.yml`、`actions-lock.json`、编译器版本、Prompt、Tool/MCP 配置、Network Policy、Safe Output Artifact、Threat Detection Verdict、Actions Run、AI Credits 和人工判定。
