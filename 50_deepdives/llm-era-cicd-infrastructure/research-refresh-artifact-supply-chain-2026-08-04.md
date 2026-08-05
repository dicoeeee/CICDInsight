---
title: 制品仓与供应链研究刷新——从制品证明到 Agent 行动与 AI 资产证据
date: 2026-08-04
as_of: 2026-08-04
status: researched
source_scope: primary-sources
baseline: research-artifact-supply-chain-2026-07-28.md
confidence: medium-high
tags:
  - research/agentic-cicd
  - research/software-supply-chain
  - artifact-repository
  - provenance
  - ai-ml-bom
---

# 制品仓与供应链：2026-08-04 刷新

> [!summary] 刷新判断
> 严格按 **2026-07-28 → 2026-08-04** 的七天窗口，未检索到能确认的、由官方发布说明或规范新发布的跨厂商能力跃迁。因此不能把这次刷新写成“本周已经出现新一代供应链平台”。但截至 8 月 4 日，基线尚未充分分开的三条 2026 信号已更清晰：**Agent 的 skills/MCP 已进入制品式的版本、扫描和安装前准入；供应链证据开始从单个 artifact attestation 走向可验证的声明图；AI/ML 模型、数据、tokenizer 和 prompt template 开始拥有可表达的 BOM 身份与谱系。**
>
> 这不是 Agent 已获准自动 promotion/delete，也不是 provenance 证明模型、提示词或 Agent 决策正确。它说明下一页应把控制对象从“二进制制品”扩为“可执行 Agent 资产 + 其动作证据 + AI/ML 依赖”，而最终准入仍由独立 policy、签名验证、环境门禁和审批执行。

## 1. 刷新范围、方法与时间结论

- **基线：** `research-artifact-supply-chain-2026-07-28.md`。该基线已覆盖 Cloudsmith/Sonatype/JFrog MCP、SLSA/Sigstore 与 GitHub artifact attestations。
- **本次检索：** 2026-08-04 访问 JFrog 产品文档/发布材料、IETF RFC 与 Internet-Draft、CycloneDX/OWASP 官方资料；未使用媒体报道、分析师报告或模型记忆作为事实来源。
- **时间结论：** 本次找到的关键材料的发布日期为 2026-03-18、2026-06-10、2026-06-23 和 2026-06（均早于 7 月 28 日）。它们是对基线的**补强与状态校正**，不是 7 月 28 日之后的新增发布。未来页面不可把它们写为“8 月新发布”。
- **研究流程：** 本轮由专项研究子代理检索，主 Agent 复核关键一手来源、产品状态和结论边界；“未发现七天窗口内新的跨厂商能力跃迁”仍是有限检索结论，不等于业界不存在其他单点更新。

## 2. 主张—证据—边界

| ID | 可进入研究结论的主张 | 一手证据、发布日与状态 | 对 CI/CD 页面的含义 | 不能外推的边界 |
|---|---|---|---|---|
| R1 | **Agent 行为资产开始出现制品级准入链，而不只是“把 Skill/MCP 放进仓库”。** | JFrog Skills Registry 文档把 skill 定义为存于 Artifactory 的、含推理逻辑与 prompts 的版本化包；安装前要求 Xray scan，称 bundle 为 immutable/versioned。发布流程还能按配置签名。文档同时要求 Support 启用该能力，故当前可用范围/GA 状态不能仅凭文档推定。[S01] JFrog 的 **MCP Registry** 则在 2026-03-18 宣布 GA，产品方声称会在连接前阻断未批准 server 并实行 tool-level 权限。[S02] | Agent asset 的最小受管单元应是 `skill/MCP/model/prompt bundle digest + version + scan/policy verdict + installer identity`；“安装”应成为可审计的供应链准入事件。 | [S02] 是厂商自述，不能证明跨厂商普遍采用、拦截率或可替代运行时 authorization。MCP Registry **GA** 与基线所述 **JFrog MCP Server Beta** 是不同产品，不能互相推断状态。Skill 的 Support enablement 表明不应无条件写为全量 GA。 |
| R2 | **机器可验证的供应链证据正在从孤立的签名/SBOM/provenance，扩展为可关联、带透明收据的声明图。** | IETF 在 2026-06 发布 Standards Track **RFC 9943 (SCITT Architecture)**。它定义：签名声明经透明服务的 registration policy 通过后进入不可逆历史并取得 receipt；依赖方可验证声明的记录与完整性。规范明确可承载 SBOM、测试、release approval、deployment record 等异构声明。[S03] | 对 promotion 的证据模型可从“有一份 provenance”升级为 `artifact digest → build/test/scan/policy/approval/deployment statement → issuer → receipt`。这使跨系统证据关联与事后审计成为可设计的接口，而非只堆日志。 | RFC 9943 是**架构标准**，不规定所有 statement predicate、发现机制、存储运营或企业准入策略；透明性不阻止 issuer 说假话，只增加可审计性。它不是现成部署产品，也不取代 GitHub/Sigstore/SLSA 的验证。 |
| R3 | **“Agent action receipt” 已出现面向 SCITT 的可验证证据轮廓，但仍是草案，不能作为交付门禁能力。** | 2026-06-23 的 IETF Internet-Draft `draft-noa-scitt-ai-agent-receipt-00` 描述对 Agent 动作、principal、policy identity 和 recorded verdict 的防篡改、签名、离线可验证收据；明确称 policy replay 为 out-of-scope，且声明不证明 Agent 正确、安全或产生现实结果。[S04] | 可把它当作产品设计方向：高风险 tool call/promotion request 记录 `actor/delegator, tool+version, input digest, policy version+verdict, target digest, approver, receipt`，从而支持独立复核。 | **Internet-Draft / Informational / work in progress，非标准也非跨厂商产品承诺。** 不可称“业界已有统一 Agent 审批/收据标准”，不可让 receipt 代替实际 policy replay、测试、人工批准或生产验证。 |
| R4 | **AI/ML 供应链开始取得与软件制品同构、但范围更大的物料清单表达：模型不仅是权重文件，数据、训练/测试环境、tokenizer、prompt template 和配置也进入可识别谱系。** | OWASP CycloneDX AI/ML-BOM Authoritative Guide First Edition r1 于 **2026-06-10** 更新；它说明 ML-BOM 可表达模型、数据集、训练/部署/托管过程，且 CycloneDX 1.5+ 支持 ML-BOM。指南把 tokenizer 与 prompt/chat template 纳入组件，并给出 `machine-learning-model`、PURL、pedigree 与 CycloneDX 1.7 示例。[S05] CycloneDX 官方能力页也将模型、数据集、配置及训练方法 provenance 纳入 ML-BOM 透明性范围。[S06] | 模型供应链不应只扫描 container/base image：一个可部署的模型版本至少应关联 `model digest/PURL, dataset or fine-tune lineage, tokenizer, prompt template, framework/runtime, evaluation evidence, policy`，并与软件 SBOM、构建 provenance 和部署记录相连。 | 该 Guide 是实施指南，不等于所有数据来源真实、训练可复现或模型安全；ML-BOM 本身不是签名、attestation 或执行时 policy。托管模型 API 的训练数据/权重通常不可由消费方获得，不能伪造为完整谱系。 |

## 3. 与 7 月 28 日基线相比，真正该更新什么

### 3.1 修正状态，而不是夸大时间上的“新”

基线 C3/C5 正确记录了 **JFrog MCP Server Beta** 与 Agent Packages/Skills 的 beta 信息，但遗漏了一个关键区分：JFrog 在 3 月已把**独立的 MCP Registry** 宣布为 GA。[S02] 这不意味着任何 JFrog MCP tool、Skills Registry 或 Agent Package Repository 已 GA；应在材料中并列写成：

```text
JFrog MCP Server：Beta（Agent 调用 JFrog 平台 tool 的接口）
JFrog MCP Registry：厂商宣布 GA（治理被连接的 MCP server）
JFrog Skills Registry：当前文档要求 Support enablement（公开文档不足以断言全量 GA）
```

这个区别很重要：前者讨论 **Agent 如何操作制品平台**，后者讨论 **Agent 要连接的工具本身如何像制品一样被准入**。

### 3.2 形成跨来源的方向，但保持成熟度分层

两条独立信号相互补足，而不是互相证明：

```text
厂商已产品化的 Asset Gate（R1）
  skill/MCP 的版本、扫描、安装前判断、工具粒度权限
                         │
                         ▼
标准化的 Evidence Graph（R2）
  artifact 与 build/test/policy/approval/deploy 的签名声明和透明收据
                         │
                         ▼
仍在探索的 Action Receipt（R3）
  把 agent tool action/policy verdict 也变为可复核的证据对象
```

因此可表述为**“供应链的证据对象正在向 Agent 资产和动作延伸”**，不能表述为**“已有跨厂商统一的 Agent 治理标准”**。R1 是单厂商 GA/受限能力组合，R2 是 Standards Track 架构，R3 是未定稿草案；三者成熟度完全不同。

## 4. 面向企业的控制增量（分析推断）

1. **把 Agent 资产与软件制品同样治理。** 对 `skill/MCP/prompt/model bundle` 固定版本或 digest、来源、发布者、scan verdict、依赖与撤销状态；安装/启用与下载分离。
2. **把 policy 的输入和 verdict 一起留证。** 仅保存“policy passed”无法在模型、规则或例外变化后解释 promotion；至少保存受评估对象 digest、policy/version、issuer、verdict、例外/审批和时间。
3. **让 Agent 只能提交证据与计划，不持有终局权。** 高爆炸半径动作（promote、waive、delete、token/role/OIDC 变更）应由另一 principal 按可复核 evidence 作决定；R3 指向这种记录形式，不能授权 Agent 自裁。
4. **将 ML-BOM 接到既有 CI 证据链。** 模型发布或微调流程将 ML-BOM 的 subject 绑定到模型 artifact digest，再由独立 signer/attestation 与部署 policy 消费；不能以静态 model card 代替持续验证。

## 5. 页面升级候选（供主 Agent 选择；均需回链本刷新）

1. **从“Artifact Repository”升级为“Agent Asset + Evidence Registry”。**
   - 单一主张：*制品仓的新增价值不只是让 Agent 查询包，而是把 Skill、MCP、模型和它们的安装判定纳入同一受控分发面。*
   - 证据：R1；用“厂商已产品化、非行业标准”标签。

2. **将发布绿灯改画成“可验证声明图”。**
   - 单一主张：*下一代交付证据不应是一个 pass，而是一组可关联、可验证、可审计的 build/test/policy/approval/deploy 声明。*
   - 证据：R2（标准）；须标注“架构可用，具体生态集成待验证”。

3. **新增“Agent Action Receipt：方向，不是门禁”的虚线层。**
   - 单一主张：*Agent 的高风险工具动作也应变成独立证据对象，但统一收据协议仍在草案阶段。*
   - 证据：R3；仅作为趋势/路线图，不作为产品能力评分。

4. **把 Model/Prompt/Tokenizer 放入软件供应链对象图。**
   - 单一主张：*模型交付的最小可审计单元不止权重文件，还包含数据/谱系、tokenizer、prompt template、运行时与评测证据。*
   - 证据：R4；明确 ML-BOM 是清单表达，不是质量或安全证明。

## 6. 本轮反例、空白与禁止表述

- 未发现可验证的 2026-07-29 至 2026-08-04 官方跨厂商发布，故不报告“本周行业已转向”。
- 未发现使 Agent 可凭 MCP 自行绕过 promotion、waiver、delete、token/role 管理审批的一手证据；现有基线的受限写入结论保持有效。
- 不得把 JFrog 的产品页承诺外推为其他仓库、IDE 或 MCP client 的默认行为；也不得把 feature availability 与企业已实际部署混同。
- 不得把 RFC 9943 或 Internet-Draft 说成工具、认证计划、完整 provenance schema 或部署准入 policy。
- 不得把 ML-BOM 说成模型权重/训练数据真实性、版权合规、无偏见、安全性或性能的证明；这些要靠具体的 attestation、验证程序与 policy。

## 7. 来源清单（均于 2026-08-04 访问）

| ID | 一手来源 | 发布/更新 | 状态与用途 |
|---|---|---|---|
| S01 | [JFrog Docs — Get Started with the Skills Registry](https://docs.jfrog.com/ai-ml/docs/skills-registry) | 页面未列发布日期；2026-08-04 访问 | 当前产品文档；Support enablement、版本化/不可变 bundle、Xray scan、可选签名发布。 |
| S02 | [JFrog Blog — From Agentic Risk to Agentic Confidence: the JFrog MCP Registry is GA](https://jfrog.com/blog/announcing-general-availability-of-the-jfrog-mcp-registry/) | 2026-03-18 | **厂商宣布 GA**；MCP Registry 的 gate/tool-level permissions。与 MCP Server Beta 是不同产品。 |
| S03 | [IETF RFC 9943 — An Architecture for Trustworthy and Transparent Digital Supply Chains](https://www.rfc-editor.org/rfc/rfc9943.html) | 2026-06 | **Standards Track**；SCITT 签名声明、registration policy、receipt、透明/可审计链及边界。 |
| S04 | [IETF Internet-Draft — A SCITT Profile for AI-Agent Action Receipts](https://www.ietf.org/archive/id/draft-noa-scitt-ai-agent-receipt-00.html) | 2026-06-23 | **Informational Internet-Draft / work in progress**；Agent action receipt 与明确非目标。 |
| S05 | [OWASP CycloneDX — Authoritative Guide to AI/ML-BOM, First Edition r1](https://cyclonedx.org/guides/OWASP_CycloneDX-Authoritative-Guide-to-AI-ML-BOM-en.pdf) | 2026-06-10 | 实施指南；模型、数据、tokenizer、prompt template、PURL/pedigree、CycloneDX 1.7 示例。 |
| S06 | [CycloneDX — Machine Learning Bill of Materials](https://cyclonedx.org/capabilities/mlbom/) | 页面未列发布日期；2026-08-04 访问 | 官方能力页；模型、数据集、配置与训练方法 provenance 的透明性范围。 |
