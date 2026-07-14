---
title: Claude Code GitHub Action v1
source_id: anthropic-claude-code-action-2026
organization: Anthropic
source_type: official-open-source-release
published: 2025-08-26
verified: 2026-07-14
availability: ga
confidence: high
geography:
  - global
lifecycle_stages:
  - code-review
  - security-compliance
  - testing-gates
  - build
tool_categories:
  - ci-action
  - coding-agent
company_topics:
  - Anthropic
autonomy_levels:
  - L1
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Claude Code GitHub Action v1

## 来源

- 标题：Claude Code GitHub Action releases
- 发布：2025-08-26 v1 GA；2026-07-11 仍持续更新
- 链接：[官方 Release](https://github.com/anthropics/claude-code-action/releases)
- 来源类型：官方开源 Action 与发布记录
- 能力状态：GA

## 一句话结论

通用编码 Agent 已能作为标准 GitHub Actions 步骤进入 PR 评审、CI 修复和安全检查，但 Runner、密钥和不可信输入仍由采用方治理。

## 可核验事实

- v1 统一交互与自动化模式，可配置模型、工具和系统提示。
- 官方列出自动 PR Review、CI Failure Fix、Issue Triage 和 Security Scanning 等流程。
- 支持通过 Amazon Bedrock 和 Google Vertex AI 使用模型。

## CI/CD 相关性

- 涉及阶段：代码评审、安全、测试门禁和构建诊断。
- 工具类别：CI Action、通用编码 Agent。
- 自主等级：L1—L3。
- 涉及角色：开发者、平台工程和安全团队。

## 对洞察的价值

证明 Agent 的部署单位正在从 IDE 会话转为可审计、可复用的流水线步骤。

## 限制与待验证项

- 官方发布记录证明能力，不证明企业效果或生产可靠性。
- 需要采用方正确配置 Runner 隔离、密钥权限与 PR 信任边界。

## 可引用判断

- “Agent as pipeline step”已经 GA，但“安全自治”仍取决于外部 CI 控制。
