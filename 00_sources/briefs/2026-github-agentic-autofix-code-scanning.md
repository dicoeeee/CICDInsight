---
title: GitHub Agentic Autofix for Code Scanning
source_id: github-agentic-autofix-code-scanning-2026-07-10
organization: GitHub
source_type: official-changelog-and-docs
published: 2026-07-10
verified: 2026-08-10
availability: public-preview
confidence: high
geography:
  - global
lifecycle_stages:
  - security-compliance
  - testing-gates
tool_categories:
  - source-control
  - security
  - code-remediation
company_topics:
  - GitHub
autonomy_levels:
  - L1
  - L2
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# GitHub Agentic Autofix for Code Scanning

## 一手来源

| 页面 | 日期/状态 | 直接支持的功能 |
|---|---|---|
| [Agentic Autofix Public Preview](https://github.blog/changelog/2026-07-10-agentic-autofix-for-code-scanning-alerts-in-public-preview/) | 发布 2026-07-10；访问 2026-08-09；Public Preview | Alert 触发、Agent 修复、CodeQL 反馈、Draft PR |
| [About Copilot Autofix for code scanning](https://docs.github.com/en/enterprise-cloud@latest/code-security/concepts/code-scanning/autofix-for-code-scanning) | 页面未标日期；访问 2026-08-10 | 支持范围、查询套件限制、第三方工具质量与人工审查边界 |
| [Ruleset rules](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets) | 页面未标日期；访问 2026-08-09 | Review、Required Check 与指定 Check 来源 |

## 一句话结论

GitHub Agentic Autofix 把确定性 Code Scanning Alert、Agent 生成补丁、CodeQL 反馈迭代和 Draft PR 串成安全微域中的 SH3 形态；它不是 GitHub Actions 的通用 CI 失败修复器，也不拥有合并权。

## 可核验事实

- Code Scanning Alert 是外部检测器产生的结构化触发，不由修复 Agent 自己定义。
- Agent 结合告警和仓库上下文生成候选修复，并使用 CodeQL 反馈继续迭代。
- 输出为 Draft PR；Review、Required Checks 和 Merge 继续由仓库 Ruleset 控制。
- 能力处于 Public Preview，并依赖相应 GitHub Code Security 与 Coding Agent 许可/配置。

## CI/CD 相关性

- 自愈完整度：Code Scanning 微域可判为 SH3；业务正确性与完整 PR CI 仍在外部。
- 行动权限：L2，只生成并提交可审查的 Draft PR。
- 机制范式：`Analyzer → Agent → Analyzer → Draft PR → 外部 CI/Review`。

## 限制与待验证项

- CodeQL 复验只证明相关安全检测反馈，不证明业务测试、全部查询套件或第三方告警都已正确解决。
- GitHub 明确说明 Agentic Autofix 以 Code Scanning Query Suite 重跑 CodeQL，因此不能确认自定义 Query 或 `security-extended` Query Suite 产生的告警已修复；第三方扫描工具告警的修复质量也不保证。
- GitHub 明确要求审查生成的修复；本轮公开一手材料未证明自动合并、自动部署或跨客户长期缺陷逃逸率。
- 不应与 Agentic Workflows、CI Doctor、Dependabot Agent Remediation 聚合为一个统一的“GitHub CI 自愈产品”。

## 可引用判断

- 最可信的 Agent 修复闭环通常从确定性 Finding 开始，并让同类 Analyzer 复验；PR 仍是接受边界，不是正确性证明。
