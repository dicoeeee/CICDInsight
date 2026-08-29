---
title: Multica 与业界真实使用场景逐主张事实审计
tags:
  - research/agentic-cicd
  - research/fact-audit
  - company/multica
status: passed-with-gaps
as_of: 2026-08-29
confidence: medium
---

# Multica 与业界真实使用场景逐主张事实审计

## 审计结果

- **核心 Claim：** 32 项产品/场景 Claim + 5 项决策主张；均在 [[50_deepdives/multica-real-use-cases/20_evidence-map|证据矩阵]]记录直接证据、限制与置信度；
- **主 Agent 复核：** 已打开 Multica 最新 Release、main 源码文档、Security Model、Tasks、Projects/Resources、Squads、Autopilots、License、Vision；重新打开关键社区 Issue，并浅克隆公开 Webhook Inbox demo 核对 PR merge commits、DAG、CI 与验收文件；未在本机重跑其完整测试；
- **研究 Subagent：** 先后按仓库要求使用 `gpt-5.6-terra`、`high` 和网络检索研究 Multica 官方材料、相邻平台真实案例与新增 Multica 社区运行案例；主 Agent 对写入报告的关键事实逐项复核；
- **证据分层：** 官方自用、具名供应商案例、社区自述、产品文档、愿景/请求严格分开；
- **产品状态：** 只写 v0.4.36/0.x/快速迭代，不写 GA 或企业成熟；
- **结果口径：** Asana、Datadog、Cube、Barclays、AMD、You.com 的数字均标注客户/厂商自述，不外推为行业均值。

## 主张—门禁核对

| 页面主张 | 直接证据 | 必须保留的边界 | 结果 |
|---|---|---|---|
| Multica 是协调与记录层 | README、How Multica works、Agents、Tasks | 不写成模型、CI、质量或发布 Oracle | passed |
| Squad 是动态路由，不是强状态机 | Squads、#1943、#5972 | 已有系统 protocol，但无完整 DAG/step enforcement | passed |
| 默认安全边界是 daemon 用户 | Security Model | worktree 与 task token 都不构成 OS/凭据安全边界 | passed |
| Multica 已有真实运行和可复核交付实验 | oh-my-multica demo、#1998、#4276、#5856、#1351、#4804、#6150 | demo/社区实测不写成具名客户或行业成熟 | passed |
| Multica 的 Task completed 不能当交付 Oracle | #451、#5233、#3933、Tasks docs | 分开进程终态、正确产物、独立验证与业务验收 | passed |
| 业界已验证有界 Issue→候选产物 | GitHub Secret Protection、GitHub 核心仓、Asana | 测试、人审、darkship/观察不能省略 | passed |
| Review 需要独立评价 | Datadog incident replay | 22% 不是生产事故降低率 | passed |
| CI 失败是实际场景 | Barclays、AMD | 诊断/分诊不等于自动修复、重跑、合并、上线 | passed |
| 跨项目专业 Agent 可生产运行 | Cube | Channel Partner 来源降权，权限/merge 细节缺失 | passed-with-gap |
| Multica 适合先做选择性试点 | 全部机制与案例综合 | 是分析建议，不写成厂商官方推荐 | passed |

## 证据冲突处理

### “Nothing ships without a human”

- README 的流程表述与 Tutorial 的 sign-off 模式支持“人最终判断”的产品意图；
- Security Model 明确默认 Task 拥有 daemon 用户的文件、凭据和网络权限；
- **处理：** 报告不把 Multica Issue 状态当硬授权门，要求 Git/CI/CD 外部 enforcement。

### open-source 表述

- README 直接称 open-source；
- LICENSE 以 Apache-2.0 加附加条件限制对外托管、商业嵌入和品牌修改；
- **处理：** 只陈述源码公开、可自托管和具体附加条件，不做 OSI 或法律定性。

### Autopilot / Slack 文档与社区请求的时间差

- 早期 Issue 中的功能缺口可能已在 v0.4.36 前后变化；
- 当前官方文档已支持 webhook/run-only 和原生 Slack Bot，但 Slack 仍是一 App 对一 Agent；
- **处理：** 社区 Issue 只用于证明当时真实使用/问题；当前能力以观察日文档为准。

## 负面搜索边界

已找到一个带公开 PR/Git/测试/验收产物的工具作者演示，以及多类社区运行证据；仍未找到 Multica 的具名客户案例、SLA、安全认证、独立生产质量/ROI 指标、严格工作流引擎和平台级不可绕过的 merge/deploy 审批证据。该结论只说明本轮公开范围内存在证据缺口，不证明私有客户、后续实现或未公开材料不存在。

## Presentation-ready 决定

**结论：暂不通过。** 报告已经足以支持试点决策，但如果压缩成正式产品页，很容易把“场景与机制匹配”误读为“Multica 自身已在生产证明效果”。

转为 `presentation_ready: true` 前至少需要：

1. 一个独立于工具作者的具名客户/团队 Multica 端到端案例；
2. accepted/iterated/closed、CI、返工和合并后质量数据；
3. daemon 隔离、凭据、Skill、外部触发和审计的事实记录；
4. handoff、失败恢复和外部 approval Gate 的运行证据。
