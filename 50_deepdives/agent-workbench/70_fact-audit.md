---
title: Agent 工作台产品功能逐主张事实审计
tags:
  - research/agentic-cicd
  - research/fact-audit
  - topic/agent-workbench
status: passed
as_of: 2026-08-08
confidence: high
---

# Agent 工作台产品功能逐主张事实审计

## 审计范围

- 六个核心产品详章。
- 五个补充产品 Case Map。
- Source Brief、研究证据日志、专题报告和下游候选页。
- URL、页面日期、产品/能力状态、产品边界和 CI/CD 接受边界。

## 功能覆盖审计

| 产品 | 状态 | 入口 | 配置 | 上下文 | 任务 | 协作 | 扩展 | 触发 | 产物 | 权限 | 管理/审计 | 限制 |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| WorkBuddy | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| ChatGPT Work/Codex/Workspace Agents | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Claude Cowork | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| GitLab Duo Agent Platform | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Harness Inc. Worker Agents | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| GitHub Agentic Workflows | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |

## 产品状态审计

| 主张 | 审计结果 | 保留的限制 |
|---|---|---|
| WorkBuddy 4.5.0 正式发布 | 通过 | 不外推为全部细分能力 GA |
| Workspace Agents Research Preview | 通过 | 不回填为 Work/Codex GA 能力 |
| Claude Remote Beta / Computer Use Research Preview | 通过 | Local、Remote、Computer Use 分开 |
| GitLab Platform 18.8 GA | 通过 | 单项 Beta/Experiment 保留独立状态 |
| Harness Inc. Worker Agents | 通过，使用“官方文档能力；统一状态未声明” | 不自行判定 GA |
| GitHub Agentic Workflows Public Preview | 通过 | 全文随关键主张保留 |
| Octopus Claude Agent Step Alpha | 通过 | 不建议关键无人值守自动化、无中途批准 |

## 产品边界审计

| 边界 | 审计结果 |
|---|---|
| WorkBuddy 与 CodeBuddy 不混用 | 通过 |
| Work、Projects、Codex、Subagents、Workspace Agents 分开 | 通过 |
| Cowork 与 Claude Code 分开 | 通过 |
| 通用 Harness 与 Harness Inc. 公司名分开 | 通过 |
| GitLab Platform GA 与子能力状态分开 | 通过 |
| GitHub Agent Job、Safe Output 与 Ruleset/Required Check 分开 | 通过 |

## CI/CD 接受边界审计

| 产品 | Agent 产物 | 独立接受边界 | 审计结果 |
|---|---|---|---|
| WorkBuddy | 文件、变更、预览 | 用户与外部系统流程 | 未写成 CI/CD 发布授权 |
| ChatGPT Work/Codex | 文件、分析、代码差异 | 用户审查、测试与仓库规则 | 未写成发布授权 |
| Claude Cowork | 文件、报告、命令/应用结果 | 用户与外部系统流程 | 未写成 CI/CD 发布授权 |
| GitLab | Commit、MR、Review、CI 修复 | Approval、Pipeline、Branch/Deployment Rule | 已保留 |
| Harness Inc. | Output Variable、分析或生成结果 | Condition、Approval、Policy、Deployment Step | 已保留 |
| GitHub | Issue、Comment、PR | Ruleset、Required Checks、Review、Environment Rule | 已保留 |
| Octopus | 命令/文件结果、Log、Transcript | Deployment/Runbook 后续 Step 与人工流程 | 已保留 |

## 禁语与内容类型审计

人员职责变化、组织分层、职责分工表和企业落地建议只允许出现在 `95_appendix-operating-model-hypotheses.md`。

正文允许记录厂商产品角色名、管理员功能和用户操作，但不得从中推导企业人员配置或采用效果。

## Presentation-ready 决定

- 当前状态：`passed`；专题 `presentation_ready: true`。
- 该决定仅允许制作具名产品功能与控制边界矩阵；不把 Preview/Beta/Alpha 能力改写成 GA，也不表示候选页已经制作。
- 保留的证据缺口继续显示在专题 README、各产品详章和 Evidence Map，不影响已经精确回链的功能事实。

## 验证记录

- [x] 十份 Source Brief 完成事实化改写，Claude、Microsoft、Google 三份新增简报已纳入索引；独立 Source Brief 机械计数为 81。
- [x] 全量官方 URL 首轮校验均返回 HTTP 2xx；最终复扫时 Google 与 OpenAI 文档域出现传输超时，链接未变化且已由首轮校验和官方页面复核确认。
- [x] 26 个专题/简报 Frontmatter 可解析，21 个范围内文档的 Wikilink 目标存在。
- [x] 六个核心产品覆盖统一功能字段；GitHub 未公开的多 Agent 协作与专用审计接口明确记录为 `not-stated`。
- [x] 下游横向洞察、主报告、候选页和 Source Map 已改为具名产品功能事实；规定禁语在隔离附录外零命中。
- [x] Standards 与 Spec 双轴复审的内容问题全部关闭；提交前 `git diff --check` 通过。
