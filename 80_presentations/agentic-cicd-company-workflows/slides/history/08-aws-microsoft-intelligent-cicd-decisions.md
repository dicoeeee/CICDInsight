---
title: Slide 08 - AWS × Microsoft 智能化 CI/CD 决策历史
tags:
  - research/agentic-cicd
  - deliverable/presentation/history
status: active
as_of: 2026-08-05
---

# 决策历史

## 初始约束

- 用户任务原文：`生成一页洞察ppt，主题是洞察aws和微软在智能化cicd领域提供哪些功能或者特性，整体左右两分，左边为aws，右边为微软`
- 已锁定的用户约束：页面整体左右两分；AWS 在左；Microsoft 在右。
- 尚未批准：洞察对齐路线、完整页面版式框架、区域内容计划、可见文案与视觉基线。

## 证据门禁

- 2026-08-05：复用 AWS DevOps Agent、GitHub Agentic Workflows、LLM-era CI/CD Infrastructure 三个 presentation-ready Deep Dive；
- 2026-08-05：完成 AWS / Microsoft 当前能力、产品状态与控制边界的一手来源刷新；
- 结论：证据门禁 `clear`，允许进入路线选择；正式生产仍受路线、页面版式框架、区域内容设计和视觉锁阻塞。

## 路线选择

- 提案：`Top-down` 先锁定跨公司单一洞察；`Bottom-up` 先确认两侧能力清单，再综合整页洞察。
- 用户选择原文：`bottom-up`。
- 结果：`insight_alignment.route = bottom_up`，状态为 `user_selected`。

## 页面版式框架提案 v1

- 16:9 单页；顶部 16% 为横跨全页的标题与一行综合判断占位；
- 中部 76% 为左右双区，AWS 左、Microsoft 右，正文净宽各约 48%，中间 4% 留白或细分隔线；
- 每区顶部固定公司标识，下方最多 4 个等高信息位，每个信息位容量不超过 1 行标签加 2 行说明；
- 底部 8% 固定为来源、产品状态说明与 `as_of`；
- 阅读顺序：顶部标题与综合判断 → AWS 自上而下 → Microsoft 自上而下 → 页脚；
- 容差：左右净宽各可浮动不超过 2 个百分点；槽位高度和间距可微调，但不得新增区域、交换左右、增加第五个信息位或改变顶部与页脚职责；
- 状态：`proposed`，等待用户批准。

### 页面版式框架批准

- 用户批准原文：`同意`。
- 结果：`layout_frame.state = user_approved`；v1 几何、容量、阅读顺序与容差成为当前页面合同。

## 证据簇与区域分配提案 v1

- `CD1`：AWS 左侧分配发布就绪审查、变更驱动的发布测试、上线后事件调查、事故回流与预防建议；
- `CD2`：Microsoft 右侧分配异步编码 Agent、PR 与安全反馈 / 候选修复、GitHub Agentic Workflows、Azure DevOps / Azure MCP 工具接入；
- 状态：两项均为 `proposed`，尚未批准区域任务、观点、可见文案、表示形式或页面主张。

### 用户反馈与提案撤回

- 用户反馈原文：`CD1改一下，看看还有其他能力不；CD2的1，2可以保留，34看看能不能换；同时功能点不一定只留4个，可以多个`；
- 原 `CD1`：撤回，重新研究 AWS 的其他候选能力；
- 原 `CD2`：第 1 项“异步编码 Agent”和第 2 项“PR 与安全反馈 / 候选修复”保留并记录为用户批准；第 3、4 项重新研究替换；
- 原布局 v1：因“每侧最多 4 个信息位”的容量约束不再成立，`layout_frame` 重新打开；AWS 左、Microsoft 右的总结构继续保留；
- 当前状态：扩展研究进行中，尚无新的 AWS 清单、Microsoft 替换组合或页面容量方案。

## 扩展研究结果

- AWS 一手来源候选池：9 项；建议将“交付—运行上下文地图”和“可调度自定义 SRE Agent”加入原四项的评审范围；
- Microsoft 一手来源候选池：8 项；Dependabot Agentic Remediation 与 GitHub Code Quality 可替换原 Agentic Workflows，Azure SRE Agent 可替换原 MCP 工具接入；
- Microsoft 的“异步编码 Agent”和“PR 与安全反馈 / 候选修复”继续保留；
- 研究文件：`00_sources/research-aws-intelligent-cicd-expanded-capabilities-2026-08-05.md`、`00_sources/research-microsoft-intelligent-cicd-expanded-capabilities-2026-08-05.md`；
- 当前只是候选池与替换方向，不是区域内容批准。

## 页面版式框架提案 v2

- 顶部 15% 为标题与一行综合判断占位；正文 78%；页脚 7%；
- 中部 AWS 左、Microsoft 右，净宽各约 49%，中间 2% 间隔；
- 每侧分三个纵向能力层，每层最多 2 个主功能点，即每侧最多 6 项；
- 单项容量为 1 行标签、1 行机制说明和 1 个紧邻的状态 / 边界短句；
- 容差：栏宽各浮动不超过 2 个百分点，层高各浮动不超过 3 个百分点；不得增加第四层或第七个主功能点；
- 状态：`proposed`，等待用户批准。功能名称、分层含义和具体能力分配仍保持开放。
