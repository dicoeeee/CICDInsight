---
title: AWS DevOps Agent Release Management Preview
source_id: aws-devops-agent-release-management-2026-06-17
organization: AWS
source_type: official-announcement
published: 2026-06-17
verified: 2026-07-14
availability: preview
confidence: high
geography:
  - global
lifecycle_stages:
  - code-review
  - testing-gates
  - release
tool_categories:
  - code-review
  - testing
  - release-management
  - observability-operations
company_topics:
  - AWS
autonomy_levels:
  - L1
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# AWS DevOps Agent Release Management Preview

## 来源

- 标题：AWS DevOps Agent adds release management capability (preview)
- 组织或项目：AWS
- 发布日期：2026-06-17
- 链接：[AWS What's New](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-release-management/)
- 来源类型：官方发布公告
- 能力状态：Preview

## 一句话结论

AWS 正把已经 GA 的生产运维 Agent 向发布前延伸，用同一环境理解执行发布就绪评审和变更相关的自主测试。

## 可核验事实

- Release Readiness Review 检查内部标准偏差、依赖影响和访问控制，并映射跨仓依赖。
- 对基础设施变更使用确定性证明检查权限是否超出应用需要及是否偏离 AWS 最佳实践。
- Release Testing 为 Web 和 API 应用生成并运行针对具体变更的测试计划。
- 能力可嵌入 IDE、PR/MR、CI/CD Pipeline 或 Agent Chat。
- 截至资料截点，仅在 `us-east-1` 预览，预览期不额外收费。

## CI/CD 相关性

- 涉及阶段：代码检查、测试门禁、发布风险决策。
- 工具类别：发布就绪、测试生成与执行、拓扑和依赖分析。
- 自主等级：L1—L3。
- 涉及角色：开发、QA、发布经理、安全与平台工程师。

## 对洞察的价值

它把发布前验证和发布后运维连接成同一个闭环，是“从流水线阶段优化转向变更全生命周期风险管理”的代表。

## 限制与待验证项

- 能力仍为预览，区域、场景和定价受限。
- 自主测试的覆盖率、误报和真实回归发现率尚缺少独立数据。

## 可引用判断

- 云厂商正在利用生产拓扑和运行历史，让发布门禁从静态规则判断升级为针对具体变更的风险推理与测试。

