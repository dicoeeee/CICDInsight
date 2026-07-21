---
title: CI/CD 问题自愈专题
aliases:
  - CI/CD Self-Healing Deep Dive
  - 流水线自愈专题
tags:
  - research/agentic-cicd
  - research/deep-dive
  - scenario/self-healing
status: complete
as_of: 2026-07-15
topic_id: cicd-self-healing
topic_type: scenario
stages:
  - testing-gates
  - build
  - deployment
  - release
  - post-release-operations
confidence: high
refresh_after: 2026-09-15
---

# CI/CD 问题自愈专题

> [!abstract] 当前判断
> CI/CD 自愈不是“让 Agent 看见红灯后随便改到绿”，而是一个有边界的控制系统：先分类失败，再把证据、候选修复、独立验证、受控执行、观察与回退连接起来。2026 年业界最成熟的是 CI 失败的诊断和修复 PR；自动验证、写回 PR 分支正在成熟；生产环境中的真正闭环仍只适合预批准、低爆炸半径 Runbook。

## 关键结论

1. “诊断”“提出修复”“验证并写回”“无人值守恢复”是四种不同能力，不能统称为自愈。
2. 自愈完整度 `SH0—SH4` 与仓库既有自主等级 `L0—L4` 正交：一个系统可以在临时分支完整修复，但对主分支仍只有 L2 权限。
3. 最可靠的架构不是纯 Agent 循环，而是“确定性快环 + Agent 慢环”：瞬态故障用有限重试/重调度/回退，代码和配置问题用 Agent 复现、最小修复、独立 Gate 和 PR。
4. 测试、扫描、策略、签名和 SLO 必须由 Agent 外部定义；允许 Agent 修改 Oracle，就会出现“门禁变绿、问题未愈”。
5. AWS DevOps Agent 等生产调查产品可以自动启动调查并生成缓解计划，但官方明确不代替操作员执行，属于 SH1—SH2，不应写成生产 L4 自愈。
6. 近期最值得试点的是可复现的 Lint/Type/Build/Dependency 失败、明确瞬态错误和非生产 GitOps Runbook；Flaky Test、跨仓回归和生产恢复需要更严格的分流与停止条件。

## 专题导航

| 交付物 | 状态 | 入口 |
|---|---|---|
| Charter | 完成 | [[50_deepdives/cicd-self-healing/00_charter|研究边界]] |
| Question Tree | 完成 | [[50_deepdives/cicd-self-healing/10_question-tree|问题树]] |
| Evidence Map | 完成 | [[50_deepdives/cicd-self-healing/20_evidence-map|证据矩阵]] |
| Case Map | 完成 | [[50_deepdives/cicd-self-healing/30_case-map|案例比较]] |
| Labs | 已设计、未执行 | [[50_deepdives/cicd-self-healing/40_labs/README|验证实验]] |
| Findings | 完成 | [[50_deepdives/cicd-self-healing/50_findings|分析发现]] |
| Playbook | 完成 | [[50_deepdives/cicd-self-healing/60_playbook|企业实践手册]] |
| Report | 完成 | [[50_deepdives/cicd-self-healing/90_report|完整报告]] |
| Research Evidence | 完成 | [[50_deepdives/cicd-self-healing/research-evidence|一手证据底稿]] |

## 上下游关系

- L0：[[00_sources/agentic-cicd-source-landscape|信息源景观]]与 [[00_sources/README|Source Brief]]。
- Stage：[[30_summaries/stages/README#4. 编译、构建与出包|构建自愈]]、[[30_summaries/stages/README#8. 发布后验证、观测、回滚与故障恢复|发布后恢复]]。
- Company：[[20_summaries/companies/README|厂商路线比较]]。
- Tool：[[10_summaries/tools/README|Agent 工具与技术栈]]。
- Cross-cutting：[[40_summaries/crosscutting/README|治理、反馈与经济性]]。
