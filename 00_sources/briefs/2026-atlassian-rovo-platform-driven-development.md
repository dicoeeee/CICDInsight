---
title: Atlassian Rovo Dev Driven Development
source_id: atlassian-rovo-platform-driven-development-2026-04-23
organization: Atlassian
source_type: official-engineering-practice
published: 2026-04-23
verified: 2026-07-14
availability: case-study
confidence: medium
geography:
  - global
lifecycle_stages:
  - code-review
  - testing-gates
  - build
  - deployment
  - release
tool_categories:
  - coding-agent
  - ci-pipeline
  - testing
  - infrastructure-deployment
  - governance
company_topics:
  - Atlassian
  - Rovo Dev
autonomy_levels:
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Atlassian Rovo Dev Driven Development

## 来源

- 标题：Rovo Dev Driven Development — How we built a platform in 4 weeks
- 组织或项目：Atlassian
- 发布日期：2026-04-23
- 链接：[Atlassian Blog](https://www.atlassian.com/blog/rovo/rovo-dev-platform-driven-development)
- 来源类型：第一方工程师实践复盘
- 能力状态：内部实践案例

## 一句话结论

当 Agent 承担大量实现工作后，Atlassian 工程实践把安全重心从逐行阅读代码转向 CI/CD、分片、RBAC/JIT、渐进发布和 AI 编写的端到端测试。

## 可核验事实

- 作者描述的工作流包括创建 PR、等待 CI、修复分支构建问题，再处理 PR Bot 评论。
- 风险控制强调 CI/CD Pipeline、变更 Sharding、RBAC/JIT、Progressive Rollout 和 Canary。
- 作者明确提出“如果阅读代码，优先阅读测试”的新评审习惯。
- Rovo Dev 能操作 Bitbucket 与 Pipeline，创建 PR、读取 Diff 和监控 Build。
- “四周建成平台”和“完全由 LLM 构建”为单一团队第一方叙述，不是受控研究。

## CI/CD 相关性

- 涉及阶段：评审、测试、构建、部署和渐进发布。
- 工具类别：Agent 驱动开发、CI 证据、隔离执行和发布控制。
- 自主等级：L2—L3。
- 涉及角色：开发者、平台工程师、安全团队和评审者。

## 对洞察的价值

该案例直接触及人员与流程变化：代码阅读的重要性相对下降，测试证据、权限边界、变更大小和渐进发布的重要性上升。

## 限制与待验证项

- 单个团队经验可能存在幸存者偏差和宣传色彩。
- 缺少缺陷率、返工量和长期维护成本数据。

## 可引用判断

- Agent 大幅提高实现速度后，人类控制点可能从“审查每一行代码”迁移到“审查意图、测试证据、权限和发布风险”。

