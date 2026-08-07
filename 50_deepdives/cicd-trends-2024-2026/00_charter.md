---
title: 各公司智能化 CI/CD 演进趋势 Charter
aliases:
  - Intelligent CI/CD Evolution Charter
tags:
  - research/agentic-cicd
  - research/deep-dive
  - charter
topic_id: cicd-trends-2024-2026
status: complete
as_of: 2026-08-07
---

# Charter：各公司智能化 CI/CD 演进趋势（2024—2026）

## 要支持的决策问题

面向 CTO、研发效能负责人与平台工程负责人的决策：**2024 年至今，GitHub、Harness、AWS、Microsoft、字节、OpenAI、Anthropic 的"智能化 CI/CD"（AI/Agent 驱动的软件交付能力）各走了怎样的演进轨迹？共同演进模式是什么、各公司差异化在哪里、演进到什么程度可以采信？**

本专题**不**研究"从传统 CI 到智能化"的过渡，而是把智能化 CI/CD 当作已存在的事物，研究它**自身**如何 2024-2026 演进：

1. 能力形态如何演进：从"AI 建议"→"Agent 执行"→"自治门禁/发布"？
2. 智能化能力在 CI/CD 环节上如何扩散：从检查/评审扩散到构建修复、门禁、发布准备、恢复？
3. 自治等级如何演进：Agent 在哪些环节获得执行权、哪些环节仍被边界拦截？
4. 产品状态如何演进：Research → Preview → GA 的转正轨迹？
5. 执行形态如何演进：从终端 CLI Agent → 编译进流水线 → 常驻编排器（issue 追踪器/Agent Harness）？

决策产物：
- 一份"智能化 CI/CD 演进轨迹"跨公司地图（按能力形态、环节、自治等级、产品状态四维）；
- 各公司独自的演进时间线与成熟度曲线；
- 共性演进模式与差异化路线；
- 企业可采用度排序与证据缺口。

## 研究边界

- **观察窗口**：2024-01 至 2026-08-07（`as_of: 2026-08-07`）。
- **公司范围**：GitHub、Harness、AWS、Microsoft（Azure DevOps/GitHub 联动）、字节跳动（含火山引擎）、OpenAI、Anthropic。
- **智能化 CI/CD 定义**：在编码完成后的检查、门禁、构建、制品、部署、发布、恢复环节中，引入 AI/Agent 能力（推理、生成、自主执行、门禁判断、编排）的形态。编码辅助本身不是本专题主线，但编码 Agent 进入 CI/CD 环节（CI 修复、评审、护送合并）属于本专题。
- **变化类型**：能力形态、环节覆盖、自治等级、产品状态、执行形态五类演进。

## 非目标（明确不做）

- 不做"传统 CI/CD vs 智能化"的对比叙事（确定性引擎特征只作为演进发生的背景约束引用，不作为主线）。
- 不做传统 CI/CD 功能全量盘点（如 GitHub Actions 全部 action、Azure Pipelines 全部 task）。
- 不做 MCP/CLI/Agent Harness 协议级深研（已有独立专题 [[50_deepdives/mcp-protocol/README|MCP]]、[[50_deepdives/cli-agent-interface/README|CLI]]）。
- 不做基础模型能力 Benchmark 或模型间比较。
- 不把"未检索到证据"表述为"业界不存在"。

## 与既有专题的关系（复用而非重复）

| 既有专题 | 本专题用途 |
|---|---|
| [[50_deepdives/github-agentic-workflows/README|GitHub Agentic Workflows]] | 作为 GitHub 侧机制深研，本专题引用其结论并叠加"演进轨迹"视角 |
| [[50_deepdives/harness-company/README|Harness 公司]] | 作为 Harness 侧事实源，叠加 2024-2026 智能化演进事实表 |
| [[50_deepdives/aws-devops-agent/README|AWS DevOps Agent]] | 作为 AWS 侧机制深研 |
| [[50_deepdives/aws-microsoft-intelligent-cicd/README|AWS 与 Microsoft 智能 CI/CD 对比]] | 双公司能力对比基线 |
| [[50_deepdives/llm-era-cicd-infrastructure/README|大模型时代 CI/CD 基础设施]] | 跨基础设施技术变化的横向背景 |

## 关键研究问题

1. 各公司智能化 CI/CD 的能力形态在 2024-2026 如何演进（建议→执行→自治）？
2. 智能化能力在 CI/CD 环节上如何扩散（哪些环节先智能化、哪些后）？
3. 自治等级如何演进：Agent 在哪些环节获得执行权、哪些环节仍被边界拦截？
4. 产品状态如何演进：Research → Preview → GA 的转正轨迹与速度？
5. 执行形态如何演进：终端 CLI → 编译进流水线 → 常驻编排器？
6. 跨公司共同的演进模式与差异化路线、证据冲突有哪些？
7. 对企业可采用度排序：演进到什么程度可采信、哪些须保持阻塞？

## 验收标准

- 每个关键主张能回链到 `00_sources/` 研究底稿或 Source Brief；
- 产品状态、发布日期、版本、案例成效逐项核对原文；
- 区分事实/分析推断/待验证假设；厂商自述指标标注；
- 存在证据缺口时保持相关结论阻塞；
- 完成逐主张事实审计后设置 `presentation_ready`。

## 非目标证据口径

- 优先官方文档、官方工程博客、官方仓库、官方 release notes。
- 二手转载（微信公众号、博客转载）只作线索，不进入事实表。
- 单一厂商来源的指标标注"厂商自述"。
- `unverified` 项显式标记，不进入正式结论。