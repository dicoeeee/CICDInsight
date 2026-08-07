---
title: GitHub 与 Microsoft CI/CD 变化趋势研究（2024 至今，截至 2026-08-07）
tags:
  - research/agentic-cicd
  - evidence/primary-source
  - company/github
  - company/microsoft
  - trend-update
status: complete
as_of: 2026-08-07
accessed: 2026-08-07
confidence: high-for-official-changelog-and-docs; medium-for-changelog-list-page-titles; low-for-microsoft-repo-migration
---

# GitHub 与 Microsoft CI/CD 变化趋势研究（2024 至今，截至 2026-08-07）

## 研究范围与口径

- **任务性质**：趋势更新。仓库既有结论（GitHub Agentic Workflows、Azure DevOps MCP、Microsoft 仓库迁移、Artifact Attestations/SLSA、Copilot 进 CI/CD）已核实，重点补充 2024 以来新变化与状态更新。
- **访问时间**：全部链接于 2026-08-07 核验；无法核验项显式标记 `unverified`。
- **一手来源**：github.blog/changelog、github.github.io/gh-aw、docs.github.com、learn.microsoft.com（Azure DevOps release notes / features-timeline）、devblogs.microsoft.com。
- **证据分级**：`事实` / `分析推断` / `待验证假设`。

## 结论先行

1. **GitHub 的叙事是"Agent 进入 Actions 控制面"，而非再造 CI 引擎。** gh-aw（GitHub Agentic Workflows）官方文档明确自述为"augment your existing, deterministic CI/CD with Continuous AI capabilities"，并明确"pull requests are never merged automatically"。Copilot 正从"助手"变为"CI/CD 内的一等执行者"（Copilot CLI 在 Actions 内免 PAT、Copilot cloud agent 修 check、Agentic Autofix）。
2. **供应链安全在 2024-2026 成为 GitHub 的稳定主线**：Artifact Attestations（attest@v4，默认 SLSA v1.0 Build Level 2、可复用工作流达 Level 3）、持久 commit 签名验证 GA、只读 Actions cache 防投毒、恶意 workflow 暂扣待审批、私有网络区域等。
3. **Microsoft 的方向是"双引擎编排"**：GitHub 作为开源/协作侧引擎 + Azure DevOps 作为既有企业平台侧，两端都注入 Copilot 与 GHAzDO（GitHub Advanced Security for Azure DevOps）；Azure Pipelines 在 runner 规格层向 GitHub Actions 看齐（"GitHub-hosted Agents" 命名）。
4. **Azure DevOps 的 AI/安全能力 2026 年密集落地但多处于 Limited/Public Preview**：Copilot Code Reviews for Azure Repos（Limited Public Preview，2026 Q2）、Copilot Autofix for Code Scanning（Limited Public Preview）、Remote Azure DevOps MCP Server（Public Preview，2026-03-31）、Entra workload identity service connection（Preview）。GHAzDO 三件套（Secret/CodeQL Code/Dependency Scanning）已 GA。

---

## 一、GitHub

### 事实表：AI / Agentic 自动化

| 事实 | 一手来源 URL | 发布时间 | 产品状态 | 访问时间 | 证据强度 |
|---|---|---|---|---|---|
| GitHub Agentic Workflows 进入 Public Preview：以 Markdown 定义自动化，编译为标准 Actions YAML，在 Actions 内运行编码 Agent（Copilot/Claude/Codex/Gemini），默认只读、sandbox 容器 + Agent Workflow Firewall、safe outputs 校验、独立 threat detection 作业 | https://github.blog/changelog/2026-06-11-github-agentic-workflows-is-now-in-public-preview | 2026-06-11 | Public Preview | 2026-08-07 | 高（changelog 正文） |
| gh-aw 开源仓库 github/gh-aw；文档站 github.github.io/gh-aw；支持自托管 & ARC runner、microVM 隔离、零密钥 Agent、AI 信用额度成本控制（max-ai-credits）、OpenTelemetry；"Developed by GitHub and Microsoft"（GitHub Next & Microsoft Research） | https://github.github.io/gh-aw/ | 2026-06-11 前后（文档实时） | Public Preview | 2026-08-07 | 高（官方文档） |
| gh-aw 起源：Technical Preview 公告，明确为 "a collaboration between GitHub, Microsoft Research, and Azure Core Upstream"；定位为增强而非替代确定性 CI/CD（Continuous AI 概念）；Markdown + frontmatter，生成 .ldk.yml 锁文件 | https://github.blog/ai-and-ml/automate-repository-tasks-with-github-agentic-workflows/ | 2026-02-13 | Technical Preview（当时）；现 Public Preview | 2026-08-07 | 高（官方博客） |
| Microsoft Aspire 团队生产使用 gh-aw：跨仓库文档自动化（microsoft/aspire → aspire.dev），2026-05-03~06-02 共 396 次运行、82 个文档 PR 全部合并、合并中位数 44.8 小时、draft-only 永不自动合并、按工作流分配 scope 的 GitHub App | https://github.blog/ai-and-ml/github-copilot/automating-cross-repo-documentation-with-github-agentic-workflows/ | 2026-07-08 | 生产案例（厂商第一方） | 2026-08-07 | 高（官方博客，Microsoft 作者） |
| Copilot Code Review：Agent 化代码评审 Agent Skills + MCP 能力进入 GA | https://github.blog/changelog/2026-07-29-copilot-code-review-agent-skills-and-mcp-now-generally-available | 2026-07-29 | GA | 2026-08-07 | 中（changelog 列表页条目） |
| Copilot code review agent 随微软 50 周年公告 GA（Agent Mode + MCP 面向全体 VS Code 用户） | https://github.blog/news-insights/product-news/github-copilot-agent-mode-activated/ | 2025-04-04 | GA | 2026-08-07 | 中（博客条目） |
| Copilot CLI 在 GitHub Actions 中不再需要 PAT（自动化身份打通） | https://github.blog/changelog/2026-07-02-copilot-cli-no-longer-needs-a-personal-access-token-in-github-actions | 2026-07-02 | Improvement | 2026-08-07 | 中（列表页条目） |
| Copilot cloud agent 可修复失败的 Actions checks（GitHub Mobile 入口） | https://github.blog/changelog/2026-07-23-github-mobile-fix-failing-actions-checks-with-copilot-cloud-agent | 2026-07-23 | Improvement | 2026-08-07 | 中（列表页条目） |
| Agentic autofix for Code Scanning alerts 进入 Public Preview | https://github.blog/changelog/2026-07-10-agentic-autofix-for-code-scanning-alerts-in-public-preview | 2026-07-10 | Public Preview | 2026-08-07 | 中（列表页条目） |
| MCP 移交 Linux Foundation（供应链/生态信号） | https://github.blog/open-source/maintainers/mcp-joins-the-linux-foundation-what-this-means-for-developers-building-the-next-era-of-ai-tools-and-agents/ | 2025-12-09 | 组织治理事件 | 2026-08-07 | 中（博客标题） |

### 事实表：构建 / Runner / 平台能力（2024-2026）

| 事实 | 一手来源 URL | 发布时间 | 产品状态 | 访问时间 | 证据强度 |
|---|---|---|---|---|---|
| Actions 定价调整：2026-01-01 起 GitHub-hosted runner 降价最高 39%；原计划 2026-03-01 起对自托管 runner 收取 $0.002/分钟云平台费——因社区反馈暂停，保留开源讨论；96% 客户账单不变 | https://github.blog/changelog/2025-12-16-coming-soon-simpler-pricing-and-a-better-experience-for-github-actions | 2025-12-16 | 已生效（降价）/ 已撤回（自托管收费） | 2026-08-07 | 高（changelog 正文含 update） |
| Actions steps 支持并行执行 | https://github.blog/changelog/2026-06-25-actions-steps-can-now-be-run-in-parallel | 2026-06-25 | Improvement | 2026-08-07 | 中（列表页条目） |
| 对 GitHub-hosted runner 更多控制（自托管 API 迁移信号） | https://github.blog/changelog/2026-06-25-more-control-over-your-github-hosted-runners | 2026-06-25 | Release | 2026-08-07 | 中（列表页条目） |
| 自定义镜像构建（Build custom images from custom images） | https://github.blog/changelog/2026-06-18-actions-build-custom-images-from-custom-images | 2026-06-18 | Improvement | 2026-08-07 | 中（列表页条目） |
| 新 runner 镜像：RHEL 镜像 Public Preview、Xcode 27 镜像 Public Preview | github.blog/changelog/2026-06-25-red-hat-enterprise-linux-runner-images-are-now-in-public-preview；github.blog/changelog/2026-07-16-xcode-27-runner-image-now-in-public-preview | 2026-06-25 / 2026-07-16 | Preview | 2026-08-07 | 中（列表页条目） |
| Actions 细粒度权限对自定义仓库角色 GA | https://github.blog/changelog/2025-06-26-github-actions-fine-grain-permissions-are-now-generally-available-for-custom-repository-roles | 2025-06-26 | GA | 2026-08-07 | 中（列表页条目） |
| Actions 新增 Azure 私有网络区域 | https://github.blog/changelog/2025-06-19-github-actions-new-azure-private-networking-regions-are-available | 2025-06-19 | Improvement | 2026-08-07 | 中（列表页条目） |
| Actions cache 单仓库上限超过 10 GB | https://github.blog/changelog/2025-11-20-github-actions-cache-size-can-now-exceed-10-gb-per-repository | 2025-11-20 | Improvement | 2026-08-07 | 中（列表页条目） |
| OIDC token 新增 check_run_id claim（门禁/审计更精细） | https://github.blog/changelog/2025-11-13-github-actions-oidc-token-claims-now-include-check_run_id | 2025-11-13 | Improvement | 2026-08-07 | 中（列表页条目） |
| Actions Runner Controller (ARC) 0.12.0 发布 | https://github.blog/changelog/2025-06-13-actions-runner-controller-0-12-0-release | 2025-06-13 | Release | 2026-08-07 | 中（列表页条目） |

### 事实表：供应链安全（检查/门禁/制品）

| 事实 | 一手来源 URL | 发布时间 | 产品状态 | 访问时间 | 证据强度 |
|---|---|---|---|---|---|
| Artifact Attestations 生产能力：attest@v4 action 生成签名 provenance（二进制/容器镜像/SBOM），默认 SLSA v1.0 Build Level 2，可复用工作流可达 Level 3；公库用 Sigstore Public Good Instance，私有仓库用 GitHub 自有 Sigstore 实例（无透明日志）；gh CLI 可离线/在线核验 | docs.github.com/en/actions/security-guides/using-artifact-attestations-to-establish-provenance-for-builds；docs.github.com/en/actions/concepts/security/artifact-attestations | 文档实时（attest@v4） | 生产可用（GA 口径，文档无 preview 标注） | 2026-08-07 | 高（官方文档） |
| Artifact Attestations 支持多 subject | https://github.blog/changelog/2024-12-04-artifact-attestations-now-support-multiple-subjects | 2024-12-04 | Release | 2026-08-07 | 中（列表页条目） |
| 持久 commit 签名验证 GA（签名→门禁） | https://github.blog/changelog/2024-12-10-persistent-commit-signature-verification-is-generally-available | 2024-12-10 | GA | 2026-08-07 | 中（列表页条目） |
| 用 CodeQL 扫描 Actions workflow 漏洞（Public Preview） | https://github.blog/changelog/2024-12-17-find-and-fix-actions-workflows-vulnerabilities-with-codeql-public-preview | 2024-12-17 | Public Preview | 2026-08-07 | 中（列表页条目） |
| 对不可信触发器使用只读 Actions cache（防投毒） | https://github.blog/changelog/2026-06-26-read-only-actions-cache-for-untrusted-triggers | 2026-06-26 | Improvement | 2026-08-07 | 中（列表页条目） |
| 疑似恶意 workflow 暂扣待审批（供应链防护） | https://github.blog/changelog/2026-07-28-github-actions-holds-potentially-malicious-workflows-for-approval | 2026-07-28 | Improvement | 2026-08-07 | 中（列表页条目） |
| 更安全的 pull_request_target checkout 默认值；控制触发 Actions workflow 的主体/条件；bot 创建 PR 获批后可运行 workflow | github.blog/changelog/2026-06-18-safer-pull_request_target-defaults-for-github-actions-checkout；github.blog/changelog/2026-06-18-control-who-and-what-triggers-github-actions-workflows；github.blog/changelog/2026-06-11-bot-created-pull-requests-can-run-workflows-if-approved | 2026-06 | Release/Improvement | 2026-08-07 | 中（列表页条目） |
| 与 Microsoft Defender for Cloud 的 code-to-cloud 制品风险可见性（Public Preview） | https://github.blog/changelog/2025-11-18-unified-code-to-cloud-artifact-risk-visibility-with-microsoft-defender-for-cloud-now-in-public-preview | 2025-11-18 | Public Preview | 2026-08-07 | 中（列表页条目） |

### 定位变化（GitHub）

- **事实**：gh-aw 官方文档自述"augment your existing, deterministic CI/CD"、"don't use agentic workflows as a replacement for GitHub Actions YAML workflows"、"pull requests are never merged automatically"；Copilot 正从"助手"变为"CI/CD 内的一等执行者"（Copilot CLI 免 PAT、Copilot cloud agent 修 check）。
- **推断（I-G1）**：Actions 从"确定性 build 运行器"升级为"AI 工作负载调度层"——把 Agent 运行时、配额（AI credits）、审计统一收敛到 Actions 的权限/日志/沙箱体系。
- **假设（H-G1）**：GitHub 是否会把 Agentic Workflows 主推为"平台工程"正式产品（而非 GitHub Next 实验）——需看 Universe 2026 及 roadmap 后续标注。

---

## 二、Microsoft（Azure DevOps / Azure / GitHub 联动）

### 事实表：AI / Agent 进入 Azure DevOps

| 事实 | 一手来源 URL | 发布时间 | 产品状态 | 访问时间 | 证据强度 |
|---|---|---|---|---|---|
| Copilot Code Reviews for Azure DevOps：面向 Azure Repos 的 PR 级 AI 评审（limited public preview 落地于 2026 Q2，官方 roadmap 计划 2026 Q3 转公开预览并支持 Copilot Instructions、自动评审、Managed DevOps Pools） | https://learn.microsoft.com/en-us/azure/devops/release-notes/features-timeline | 2026-08-05（页面更新） | Limited Public Preview（2026 Q2）；公开预览计划 2026 Q3 | 2026-08-07 | 高（官方 Roadmap） |
| Copilot Code Reviews for Azure Repos（limited public preview）随 Sprint 275 发布；组织可申请后启用 | https://learn.microsoft.com/en-us/azure/devops/release-notes/2026/sprint-275-update | 2026-06-17 | Limited Public Preview | 2026-08-07 | 高（Release Notes 正文） |
| GitHub Copilot Autofix for Code Scanning（GHAzDO 内）limited public preview：为 CodeQL 告警生成修复 PR；roadmap 计划 2026 Q3 转公开预览，未来支持依赖扫描告警修复 | release-notes/2026/sprint-275-update；release-notes/features-timeline | 2026-06-17 | Limited Public Preview | 2026-08-07 | 高 |
| Azure Boards 编码 Agent 模型可选（Copilot coding agent 生成 work item→PR 时选模型） | release-notes/2026/sprint-275-update | 2026-06-17 | Release | 2026-08-07 | 中 |

### 事实表：MCP / 平台互联

| 事实 | 一手来源 URL | 发布时间 | 产品状态 | 访问时间 | 证据强度 |
|---|---|---|---|---|---|
| Remote Azure DevOps MCP Server 进入 Public Preview：托管端点 https://mcp.dev.azure.com/{org}，无需本地部署；当前支持 VS / VS Code，Microsoft Foundry、Copilot Studio 等客户端"coming soon" | release-notes/2026/sprint-271-update；learn.microsoft.com/en-us/azure/devops/mcp-server/remote-mcp-server | 2026-03-31 | Public Preview；Roadmap 目标 2026 Q3 GA | 2026-08-07 | 高 |
| 过期 PAT 不可再被修改/续期（凭据治理） | release-notes/2026/sprint-271-update | 2026-03-31 | Released | 2026-08-07 | 中 |
| 以 Entra workload identity（service principal / managed identity）访问 Azure DevOps 的新 service connection（Preview）：免 PAT、免持久密钥、审计日志 | release-notes/2026/sprint-275-update | 2026-06-17 | Preview | 2026-08-07 | 高 |
| WIF（workload identity federation）Azure DevOps issuer 将退役（2027 Q2）——迁移到更安全的身份方案 | release-notes/features-timeline | 2026-08-05（页面更新） | Roadmap（2027 Q2） | 2026-08-07 | 中 |

### 事实表：供应链安全（GHAzDO）

| 事实 | 一手来源 URL | 发布时间 | 产品状态 | 访问时间 | 证据强度 |
|---|---|---|---|---|---|
| GitHub Advanced Security for Azure DevOps（GHAzDO）：Secret Scanning、CodeQL Code Scanning、Dependency Scanning 三件套，作为独立许可叠加在 Azure DevOps 上 | release-notes/features-timeline | 2026-08-05（页面更新） | GA（能力已落地） | 2026-08-07 | 高 |
| Advanced Security 状态检查（AdvancedSecurity/NewHighAndCritical 等）作为分支策略/门禁 GA | release-notes/2026/sprint-275-update | 2026-06-17 | GA | 2026-08-07 | 高 |
| CodeQL default setup 在 GHAzDO 完成公共预览 rollout（免手工 pipeline 配置开启扫描） | release-notes/2026/sprint-275-update | 2026-06-17 | GA（rollout 完成） | 2026-08-07 | 高 |
| 移除 build identity 读取告警的权限（2026-07-01~15 分阶段），强制转向状态检查门禁 | release-notes/2026/sprint-275-update | 2026-06-17 | Breaking change（时间表明确） | 2026-08-07 | 中 |

### 事实表：Pipelines / 平台定位

| 事实 | 一手来源 URL | 发布时间 | 产品状态 | 访问时间 | 证据强度 |
|---|---|---|---|---|---|
| Managed DevOps Pools GA：VM Scale Set agent pool 的演进，微软官方建议从 VMSS 迁移 | release-notes/features-timeline | 2026-08-05（页面更新） | GA | 2026-08-07 | 高 |
| Apple Silicon macOS pipeline agents（pay-as-you-go preview）：官方明确 "Starting with Apple Silicon, we will bring some of the same sizes available in GitHub Actions over to Azure Pipelines"，使用 "GitHub-hosted Agents" pool 命名与 GitHub 相同的 vmImage 标签（macos-26-arm64） | release-notes/2026/sprint-275-update | 2026-06-17 | Public Preview（按量付费） | 2026-08-07 | 高 |
| YAML pipelines 与 classic release pipelines 的 CD 功能对齐仍在推进（Checks 是主要补齐项；Roadmap 2027 Q1 为 stage 级 traceability、按需 out-of-order stage 执行） | release-notes/features-timeline | 2026-08-05（页面更新） | Roadmap（2027 Q1 等） | 2026-08-07 | 中 |
| Sprint 271：YAML 部署可见性改进（artifact ID 展示、Stages 部署历史视图） | release-notes/2026/sprint-271-update | 2026-03-31 | Released | 2026-08-07 | 中 |
| GitHub 仓库触发的 PR 验证运行可分别配置 fork vs 同仓库的评论门槛（防未授权 pipeline 使用） | release-notes/2026/sprint-275-update | 2026-06-17 | Improvement | 2026-08-07 | 中 |

### 定位变化（Microsoft）

- **事实**：官方明确推荐 YAML > classic，且"all our security improvements have been for YAML pipelines"；Azure Pipelines 开始以 "GitHub-hosted Agents" 命名托管池并把 GitHub Actions 的镜像规格引入；GHAzDO 把 GitHub 系安全能力（CodeQL、Advanced Security、Copilot Autofix）移植进 Azure DevOps。
- **推断（I-M1）**：微软的编排是"GitHub 作为 open-source/协作侧引擎 + Azure DevOps 作为既有企业平台侧"，两头都注入 Copilot 与 GHAzDO；Azure Pipelines 在 runner/spec 层向 GitHub Actions 看齐，说明两者共享同一套 agent 基础设施趋势。
- **假设（H-M1）**：Azure DevOps 是否进入"维护模式"（仅增量安全/合规投入，无重大新 CI/CD 引擎创新）——官方未声明，roadmap 中 pipelines 侧多为部署可见性/身份治理类增量，需持续观察。

---

## 三、既有结论核实（状态更新）

| 既有结论 | 核实结果 | 状态标注 |
|---|---|---|
| GitHub Agentic Workflows 2026-06-11 进入 Public Preview（github.com/gh-aw 开源） | **成立**。补充：2026-02-13 技术预览起即由 GitHub + Microsoft Research + Azure Core Upstream 联合开发，为 GitHub↔Microsoft 联合项目 | 状态更新（补充联合开发与时间线） |
| Microsoft 2025-2026 仓库迁移到 GitHub 的实践 | **未能以一手来源复核**。旁证：Aspire 团队生产使用 gh-aw（2026-07-08）、Azure Pipelines 引入 GitHub-hosted Agents（2026-06-17）、Copilot 进入 Azure Repos——方向一致但不足以证明"迁移"结论 | 证据缺口，标记 unverified；建议回查仓库既有 Source Brief 链接 |
| Azure DevOps MCP Server 开源 | **部分更新**：官方最新为 Remote Azure DevOps MCP Server（托管、免部署），2026-03-31 Public Preview，Roadmap 2026 Q3 GA；"开源仓库"属性本次未能核验 | 状态更新 + 证据缺口（开源属性 unverified） |
| GitHub Artifact Attestations / SLSA | **成立且深化**：docs 显示 attest@v4 生产可用，默认 SLSA v1.0 Build Level 2、可复用工作流达 Level 3；2024-12 起支持多 subject；公/私仓库采用不同 Sigstore 实例 | 状态更新（补充 Level 3 路径与 Sigstore 细节） |
| GitHub Copilot / AI 进入 CI/CD | **成立且大幅扩展**：Copilot code review agent GA（2025-04-04）；Copilot CLI 在 Actions 内免 PAT（2026-07-02）；Copilot cloud agent 修 Actions check（2026-07-23）；Agentic Autofix（2026-07-10）；Copilot Code Reviews 进入 Azure Repos（2026-06-17） | 状态更新（大量新增证据） |

---

## 四、证据缺口清单

1. **Microsoft 仓库迁移到 GitHub**：无一手官方来源复核（官方博客/文档搜索无果）。需回查仓库既有 Source Brief 的原始链接，或等待微软官方工程博客公告。
2. **Azure DevOps MCP Server 的"开源"属性**：官方文档只描述托管 Remote MCP Server；本地/开源实现未核验。
3. **Artifact Attestations 的正式 GA 公告日期**：本次以 docs 生产状态（attest@v4、无 preview 标注）+ changelog 演进条目代替，未逐项定位 GA 公告原文。
4. **2026 年 changelog 多条条目**（steps 并行、RHEL 镜像、恶意 workflow 暂扣等）仅从列表页采集标题与日期，未逐条打开正文核对细节。
5. **Copilot 在 Actions / gh-aw 的计费细节**（每 run 两个 premium requests 等）仅来自 gh-aw 官方文档与 2026-02-13 博客，未交叉验证。
6. **Azure DevOps 产品线长期定位**（维护模式 vs 持续创新）无官方表态，属分析推断。

---

## 五、事实 / 推断 / 假设 分层提示

- **事实**：上文事实表全部条目（均带 URL 与时间）。
- **推断**：① Actions 从确定性构建器升级为"AI 工作负载调度层"；② Microsoft"双引擎"编排（GitHub 协作 + Azure DevOps 企业平台 + 两端注入 Copilot/GHAzDO + runner 规格趋同）；③ "Agent 写入值通过 tiny verifiable pipeline"的安全模式（safe-outputs）成为 Agent 化 CI/CD 的公共设计。
- **待验证假设**：① gh-aw 是否转正式产品；② "Microsoft 仓库迁移"具体范围/时间表；③ Azure DevOps 是否进入维护模式。

## 治理说明

本报告为业界洞察研究成果，按 AGENTS.md 归入 `00_sources/`。全部状态、日期来自 2026-08-07 实际访问的官方页面；`unverified` 项已显式标记。changelog 列表页条目（证据强度"中"）只采集标题与日期，未逐条打开正文；如需进入 `90_report/` 或 Presentation 的关键主张，须先补逐条正文核验。