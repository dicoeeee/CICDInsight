---
title: Slide 05B - 六家公司 CI 自愈高管总览
tags:
  - research/agentic-cicd
  - deliverable/presentation/slide
status: draft
slide: "05B"
content_status: deep-dive-ready
visual_status: pending
primary_deep_dive: "[[50_deepdives/cicd-self-healing/README]]"
as_of: 2026-08-09
---

# CI 自愈的分水岭：独立复验后的有界写回

> [!abstract] 页面任务
> 面向 CTO、研发效能负责人和平台工程负责人，用一页回答：六家公司分别把红灯处理推进到哪一步，哪个 Oracle 复验候选，最终写回与接受权在哪里。

## 唯一页面主张

**CI 自愈的分水岭不是能否解释红灯，而是候选修复能否经过独立复验后被有界写回。**

这是一项跨公司分析推断，不是厂商原话。它由 [[50_deepdives/cicd-self-healing/35_company-mechanism-audit|六家公司机制审计]] 与 [[50_deepdives/cicd-self-healing/70_fact-audit|逐主张事实审计]]共同支撑。

## 上部：闭环成熟路径

```text
确定性重试          Agent 诊断          Suggestion / PR          独立复验          受限写回
恢复一次执行   →   解释与根因假设   →   交付候选变更      →   原 Task/Scanner/CI   →   PR Branch/外部门禁
不计根因修复       SH1                  SH2                     SH3                 局部 SH4
```

- 路径从左到右不是产品排行榜，而是证据链逐级增加。
- `Retry Green` 只表示当前执行恢复；`PR Created` 只表示候选已交付；`Task/Scanner Green` 也不自动等于全部 Required Checks 或业务正确。

## 中部：六家公司停在哪里

| 公司 | 证据支持的闭环终点 | Oracle / 写回边界 | 生命周期与判级 |
|---|---|---|---|
| **GitHub** | CI Doctor 调查；Code Scanning Agentic Autofix 形成安全微闭环 | CodeQL 反馈 → Draft PR；完整 PR CI、Review、Merge 由 Ruleset 决定 | Agentic Workflows/Autofix **Public Preview**；通用 CI SH1，安全微域 SH3/L2 |
| **GitLab** | Fix Flow 输出 Inline Suggestion 或新 MR | 完整 Pipeline 自动复验未获直接证明；MR Approval/Pipeline/保护规则外置 | Fix Flow **GA**；SH2/L2 |
| **CircleCI** | Chunk 推分支、跑 Validation Pipeline、失败继续修；受保护分支开 Draft PR | Validation Pipeline 决定 PR 保留/关闭；检查集合取决于客户配置 | Chunk **Beta**；代码路径 SH3/L2 |
| **Harness** | AutoFix 生成 PR；Worker Autofix 可重触发 Build 至通过/Max Turns | Build Validation；完整 Required Checks 未获证明；RBAC/OPA/Approval/Audit 控制动作 | 已发布/可用，**无统一子能力阶段标签**；SH2—SH3/L2，批准执行可 L3 |
| **Nx** | 生成修复、复跑原失败 Task；白名单、高置信、已验证时写 PR Branch | 原失败 Task 是明确 Oracle；完整 Required Checks、Merge/Deploy 外置 | 已发布，**官方未标统一阶段**；SH3/L2，PR 微域局部 SH4 |
| **Buildkite** | Retry、Test State、失败分析与 Agent/MCP 底座 | 平台未证明原生通用 Patch/PR/复验；[官方 PR Build Fixer](https://buildkite.com/resources/blog/building-ai-powered-ci-workflows-three-practical-examples/)属客户编排，等待 Build 并迭代后仍由人合并 | **正式文档未标整体阶段**；平台 SH1/L1，参考编排 SH3/L2，授权 Run 可 L3 |

## 底部：三条企业启示

1. **快慢环分离：** Retry、Reschedule、Abort/Rollback 只处理已知、有限、可观察的恢复；代码修复进入独立慢环。
2. **Oracle 外置：** Agent 可读失败并生成候选，但不能修改原 Task、Scanner、Required Checks、Policy 或 SLO 来证明自己正确。
3. **授权按交叉单元细化：** 权限单位应是“故障类别 × Task × 分支/环境 × 动作”，分别绑定次数、Token Scope、写回目标、停止条件和人工接管。

## 口径边界

- 不做六家公司总体排名；产品生命周期、闭环完整度和行动权限是三条不同轴。
- GitLab 完整 Pipeline 复验、Harness 完整 Required Checks、Buildkite 通用补丁闭环均保留为本轮证据缺口。
- 本页不支持“六家公司已具备自动合并、自动部署或生产开放式修复”的更强主张。
- 现有 [[80_presentations/agentic-cicd-company-workflows/slides/05-ci-self-healing|第 05 页 Nx 机制页]]、视觉契约和全部 PPTX 保持不变；本文件只完成 05B 内容稿。

## Source Map

- 主要 Deep Dive：[[50_deepdives/cicd-self-healing/README|CI/CD 问题自愈专题]]，`presentation_ready: true`，`as_of: 2026-08-09`。
- 机制审计：[[50_deepdives/cicd-self-healing/35_company-mechanism-audit|六家公司 CI 自愈机制审计]]。
- 一手研究：[[50_deepdives/cicd-self-healing/research-six-company-mechanisms-2026-08-09|六家公司机制核验底稿]]。
- 逐主张审计：[[50_deepdives/cicd-self-healing/70_fact-audit|CI 自愈逐主张事实审计]]。
- L0 Brief：[[00_sources/briefs/2026-github-agentic-workflows]]、[[00_sources/briefs/2026-github-agentic-autofix-code-scanning]]、[[00_sources/briefs/2026-gitlab-fix-cicd-pipeline-flow]]、[[00_sources/briefs/2026-circleci-chunk-agent]]、[[00_sources/briefs/2026-harness-worker-agents]]、[[00_sources/briefs/2026-harness-code-quality-agents]]、[[00_sources/briefs/2026-nx-self-healing-ci]]、[[00_sources/briefs/2026-buildkite-ai-agents-in-pipelines]]。
- Buildkite 参考编排：[官方案例文章](https://buildkite.com/resources/blog/building-ai-powered-ci-workflows-three-practical-examples/)与[官方示例仓库](https://github.com/buildkite-agentic-examples/github-pr-build-fixer)。
