---
title: Agent 技术在 CI/CD 中的应用与实践洞察
aliases:
  - Agentic CI/CD Insight
tags:
  - research/agentic-cicd
  - research/2026
status: complete
as_of: 2026-07-15
---

# Agent 技术在 CI/CD 中的应用与实践洞察

> [!abstract] 研究目的
> 帮助 CTO、研发效能负责人和平台工程负责人判断未来两年 Agent 将如何重构 CI/CD，哪些场景已经具备落地条件，企业应如何调整平台能力、工作流程、人员职责与治理机制，并据此制定分阶段演进路线。

## 快速阅读

- [[90_report/seven-dimension-analysis|七维分析汇总报告]]：从 Stage、Company、Tool、Scenario、成熟度与价值、运行架构与控制边界、组织与工作流程七个维度形成统一判断。
- [[90_report/README|主报告]]：决策结论、八阶段影响、参考架构、18 个月路线与 2027—2028 展望。
- [[80_presentations/README|演示文稿层]]：管理 PPT 的叙事、页面文案、作业流表达和来源映射；最终渲染文件仍放在 `outputs/`。
- [[00_sources/agentic-cicd-source-landscape|81 条核心一手资料景观]]：精简并增补后的 L0 来源与逐条 Brief。
- [[00_sources/source-pruning-2026-07-14|信息源精简审计]]：从 107 条候选源缩减到 80 条核心源，并补入 CLI-Anything 后形成 81 条当前核心源的记录。
- [[00_sources/README|深度 Source Brief 索引]]：62 个高频来源的独立分析。
- [[00_sources/incremental-source-research-2026-07-14|本轮增量研究记录]]：25 条新增源的排重与选源依据。
- [[05_case_library/README|实践案例库]]：把跨专题复用的外部实践整理成架构、流程、控制边界和证据强度明确的案例卡。
- [[10_summaries/tools/README|Agent 工具与技术栈]]、[[20_summaries/companies/README|公司维度]]、[[30_summaries/stages/README|阶段维度]]、[[40_summaries/crosscutting/README|横向变化]]：主报告的四组中间证据。
- [[50_deepdives/README|专题深研索引]]：问题树、证据矩阵、案例比较、可复现实验、Findings、Playbook 和专题报告。
- [[50_deepdives/cli-agent-interface/90_report|CLI 深度报告]]、[[50_deepdives/mcp-protocol/90_report|MCP 深度报告]]、[[50_deepdives/cli-anything/90_report|CLI-Anything 项目深度报告]]：三个独立研究维度；跨维度选型见 [[50_deepdives/cli-vs-mcp-decision-guide|CLI 与 MCP 决策指南]]。

## 研究边界

- 时间：2025 年 7 月 1 日至 2026 年 7 月 15 日，重点关注 2026 年；趋势判断延伸至 2027—2028 年。
- 地域：全球实践为主，中国企业作为重要对照。
- 起点：编码完成，变更进入代码评审和交付系统。
- 终点：发布后验证、观测、回滚和故障恢复。
- 不单独研究传统 CI/CD；现有流程仅作为定位 Agent 介入点的坐标。
- 本次先产出一次性报告；可复用研究工作流不在本次范围内。

## Agent 纳入标准

一项能力至少具备上下文理解、任务规划、工具调用、多步骤执行或结果反馈中的两项，并实际介入 CI/CD 工作。Agent 运行时、编排、权限、审计和评测平台也纳入。

纯代码补全、普通聊天、单次摘要和没有执行能力的 AI 建议原则上不纳入，除非它们已经改变门禁、审批或责任流程。

## 自主等级

| 等级 | 定义 |
|---|---|
| L0 | AI 辅助，只生成信息或建议 |
| L1 | Agent 自主分析并给出行动方案 |
| L2 | Agent 生成可审查的变更、PR、策略或发布计划 |
| L3 | 经人类批准后调用工具执行 |
| L4 | 在预设权限和风险边界内闭环执行、验证并恢复 |

## CI/CD 阶段

1. 代码评审与质量检查
2. 静态分析、安全、依赖与合规检查
3. 自动化测试、质量门禁与风险决策
4. 编译、构建与出包
5. 制品、软件供应链与版本管理
6. 环境准备、基础设施与部署
7. 发布策略、审批与变更管理
8. 发布后验证、观测、回滚与故障恢复

门禁和 Agent 治理同时作为跨阶段能力研究。

## 交付结构

- [[00_sources/README|信息源层]]：原始来源、Source Brief 和事实证据。
- [[05_case_library/README|实践案例层]]：提取可复用的外部案例，明确架构、流程、自治与证据等级。
- [[10_summaries/tools/README|Agent 工具与技术栈总结]]：MCP、CLI/API、Agent 原生接口生成、Skills、Claude Code、Codex CLI、OpenCode、Gemini CLI、OpenHands、运行承载与治理控制面。
- [[20_summaries/companies/README|公司维度总结]]：厂商产品组合、内部实践、客户案例和战略方向。
- [[30_summaries/stages/README|阶段维度总结]]：沿八个 CI/CD 阶段分析 Agent 介入方式。
- [[40_summaries/crosscutting/README|横向变化总结]]：工具平台、工作流程、人员能力、治理与度量。
- [[50_deepdives/README|专题深研层]]：对重点课题建立问题树、Claim—Evidence Matrix、案例比较、实验、Playbook 和专题报告。
- [[80_presentations/README|演示文稿层]]：把跨公司、跨能力的研究结论改写为可比较的 PPT 叙事和单页作业流。
- [[90_report/seven-dimension-analysis|七维分析汇总报告]]：以任务场景为连接键，将三个描述维度与四个企业决策维度汇总。
- [[90_report/README|主报告]]：跨分类提炼趋势、成熟度、实践、风险和未来判断。

## 证据规则

- 能力状态严格区分：正式可用、预览或实验、仅宣布或路线图。
- 主报告中的重要判断必须能下钻到分类总结和 Source Brief。
- 优先采用官方文档、官方工程实践、源代码、规范和原始研究。
- 厂商效果数据标明为厂商自述；没有独立验证时不外推为行业结论。
