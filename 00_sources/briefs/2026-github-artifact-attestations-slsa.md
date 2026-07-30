---
title: GitHub Artifact Attestations 与 SLSA 构建信任
source_id: github-artifact-attestations-slsa-2026
organization: GitHub
source_type: official-docs-spec-and-changelog
published: 2024-06-25
verified: 2026-07-28
availability: ga
confidence: high
geography:
  - global
lifecycle_stages:
  - build
  - security-compliance
  - artifact-versioning
  - release
tool_categories:
  - artifact-supply-chain
  - provenance
  - attestation
  - governance
company_topics:
  - GitHub
  - SLSA
autonomy_levels:
  - L0
  - L1
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# GitHub Artifact Attestations 与 SLSA 构建信任

## 来源

- [Artifact Attestations is generally available](https://github.blog/changelog/2024-06-25-artifact-attestations-is-generally-available/)，GitHub Changelog，2024-06-25。
- [Artifact attestations](https://docs.github.com/en/actions/concepts/security/artifact-attestations)，GitHub Docs，无发布日期，访问于 2026-07-28。
- [Using artifact attestations to establish provenance for builds](https://docs.github.com/en/actions/how-tos/secure-your-work/use-artifact-attestations/use-artifact-attestations)，GitHub Docs，无发布日期，访问于 2026-07-28。
- [SLSA v1.2 Specification](https://slsa.dev/spec/v1.2/)、[Build requirements](https://slsa.dev/spec/v1.2/build-requirements)，SLSA 规范，状态 Approved，访问于 2026-07-28。

## 一句话结论

Agent 时代并没有产生一套新的制品信任原理；可验证的 Artifact Digest、构建来源、工作负载身份、隔离 Builder 和发布前策略仍是核心，只是机器生成和机器操作提高了自动生成、分发和验证 Provenance 的必要性。

## 可核验事实

- GitHub Artifact Attestations 于 2024-06-25 GA，可为 Actions 构建的二进制和容器生成并验证签名 Provenance。
- Attestation 绑定 Workflow、Repository、Organization、Environment、Commit SHA、Trigger 和 Artifact Digest，并可关联 SBOM。
- GitHub 使用 Sigstore；公共仓库写入公共透明日志，私有仓库使用 GitHub 的私有 Sigstore 实例。
- GitHub 文档明确：单独使用 Artifact Attestations 提供 SLSA v1.0 Build Level 2；采用已知、受审的 Reusable Workflow 可进一步满足 Level 3 所需隔离条件。
- SLSA v1.2 要求更高等级的 Build Platform 由控制面生成可信 Provenance，并隔离不同 Build、后续 Build 与缓存污染。
- GitHub 文档明确：Attestation 只能证明从哪里、如何构建，不能保证制品本身安全；只有验证并按期望执行策略才产生控制价值。

## CI/CD 相关性

- **构建系统：** Builder 控制面而非 Tenant/Agent 应生成可信 Provenance。
- **制品仓：** 仓库需要保存或关联 Digest、Provenance、SBOM、存储位置与晋级记录。
- **发布：** 部署门禁应验证 Attestation 与组织期望，而不是只相信标签、文件名或 Agent 结论。
- **Agent 边界：** Agent 可以请求构建或解释证据，但不能自签名、自定义验证期望并同时批准自己的制品。

## 对洞察的价值

该规范与 GA 产品构成“哪些没有因大模型改变”的核心反证：可信构建仍依赖模型之外的控制面、隔离、不可伪造身份和独立策略。Agent 让这些能力更重要，而不是让它们过时。

## 限制与待验证项

- Attestation 存在不等于下游已验证，更不等于业务正确或没有漏洞。
- SLSA Level 是 Build Platform 与采用方式的综合结果，不能只因启用一个 Action 就宣称达到 Level 3。
- GitHub 是一种实现，企业还需核验外部制品仓、部署系统和私有环境的验证链是否连续。

## 可引用判断

- Agent 可以生成候选代码和触发构建，但 Artifact Trust 必须由 Builder 身份、Digest、Provenance 和外部 Policy 共同决定。
- 制品仓的演进重点不是保存更多文件，而是保存并执行“哪个可信 Builder 以何输入产生哪个 Digest、经过何种验证与晋级”的证据链。
