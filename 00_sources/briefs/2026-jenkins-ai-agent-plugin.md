---
title: Jenkins AI Agent Pipeline Step
source_id: jenkins-ai-agent-plugin-2026
organization: Jenkins Community Plugin
source_type: official-plugin-docs
published: null
verified: 2026-07-14
availability: open-source
confidence: high
geography:
  - global
lifecycle_stages:
  - testing-gates
  - build
tool_categories:
  - ci-pipeline
  - agent-runtime-orchestration
  - human-approval
company_topics:
  - Jenkins
autonomy_levels:
  - L1
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
  - open-source
---

# Jenkins AI Agent Pipeline Step

## 来源

- 标题：AI Agent Pipeline Steps Reference
- 组织或项目：Jenkins Community Plugin
- 核验日期：2026-07-14
- 链接：[Jenkins Pipeline Steps](https://www.jenkins.io/doc/pipeline/steps/ai-agent/)
- 来源类型：官方插件步骤文档
- 能力状态：社区插件能力，不代表 Jenkins Core

## 一句话结论

Jenkins 社区已经把 Claude Code、Codex 等编码 Agent 包装成 Pipeline Step，并支持每次工具调用暂停等待人工批准，为既有 Jenkins 环境提供渐进式 Agent 接入点。

## 可核验事实

- `aiAgent` Step 支持多个 Agent 类型，包括 Claude Code 和 Codex。
- 可配置模型、Prompt、工作目录、输出 Schema 和 Agent 参数。
- 可在 Agent 每次请求 Shell、文件编辑等工具调用时暂停 Build，由 Jenkins UI 批准或拒绝。
- 文档建议将 Secret 保存在 Jenkins Credential 或配置中，而不是直接传给 Agent 参数。

## CI/CD 相关性

- 涉及阶段：构建、测试和其他 Pipeline 内 Agent 任务。
- 工具类别：Jenkins Pipeline、Agent Runtime 和 Human Approval。
- 自主等级：L1、L3。
- 涉及角色：Jenkins 管理员、流水线工程师、安全人员和开发者。

## 对洞察的价值

它说明 Agentic CI/CD 不只发生在新平台；成熟的 Jenkins 安装也可以通过插件把 Agent 作为受控 Step 增量引入。

## 限制与待验证项

- 社区插件的维护、隔离、供应链安全和企业支持需要单独评估。
- 工具调用逐次审批可控但可能显著降低自动化效率。

## 可引用判断

- 传统流水线平台的 Agent 化可以从一个带人工工具审批的 Pipeline Step 开始，而不要求全面迁移平台。

