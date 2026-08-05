---
title: AWS 体系中 LLM/Agent 驱动的软件开发与交付能力核验（不含 AWS DevOps Agent）
date: 2026-08-03
as_of: 2026-08-03
status: research-complete
confidence: high
scope: 仅使用 AWS、Amazon Q、Kiro 官方文档、发布公告、官方博客与 AWS 官方仓库；考察与软件开发、代码评审、测试、构建、现代化和交付直接相连的当前能力；明确排除 AWS DevOps Agent。
---

# AWS 体系中 LLM/Agent 驱动的软件开发与交付能力核验

## 提纲

1. 确定纳入边界与候选能力。
2. 逐项记录产品层级、状态、机制、CI/CD 接点和事实边界。
3. 将可组合路径与“产品已具备的事实”分开。

## 结论先行

截至 **2026-08-03**，AWS 体系内除 AWS DevOps Agent 外，真正能进入代码变更、验证或交付链路的能力集中在六条路径：

1. **Amazon Q Developer**：面向开发者的代码生成、IDE 评审、单测生成、Java 现代化和 GitHub PR 评审；其中 GitHub PR 评审仍为 Preview。
2. **Kiro（GA）及 Kiro CLI**：可在 CI/CD 中 headless 运行，读取/写入代码、运行命令，因而是通用的 Agent 执行面；CLI v3 仍是 Early Access，不能与已 GA 的 Kiro/CLI v2 混称。
3. **AWS Transform custom（GA）**：把组织级代码改造定义为可版本化 transformation，并以 build/validation commands 和 lessons 支撑批量、headless 的 CI/CD 执行。
4. **AWS Transform continuous modernization（Preview）**：跨仓库扫描技术债/安全/现代化机会，按 transformation definition 自动建分支和 PR/MR，并能用 EventBridge 定期重跑。
5. **AWS Transform 的容器化和 mainframe testing agents**：前者覆盖 Docker artifact、镜像 build/test、IaC、测试环境与 cutover；后者对主机现代化生成测试计划、数据采集脚本和自动化脚本。二者是垂直现代化工作流，不是通用 CI runner。
6. **AWS Security Agent（现归入 AWS Continuum 的代码扫描能力）**：对 PR 或全仓做安全评审并可生成修复 PR；代码扫描/全仓评审为 Preview，即使该服务的按需渗透测试已 GA，也不能把整个代码评审链路写为 GA。

**核心判断（分析推断）**：AWS 的重心不是让一个 Agent 取代 CodeBuild、GitHub Actions 或部署系统，而是让 Agent 在既有 Git/PR、build/test 和部署门禁之间生成变更、验证资产或修复 PR。可审阅 PR、显式 validation command、测试环境批准和既有 pipeline 是当前产品组合中的主要控制边界。

## 纳入与排除口径

### 纳入

仅纳入满足至少一项的能力：

- LLM/Agent 直接生成、评审、测试或改造代码；
- 可由 CI/CD 调用，或明确产出 PR/MR、build/test/validation、部署前测试环境；
- 能够改变现代化或安全修复的交付闭环，而不只是提供通用模型、算力或聊天入口。

### 排除

- **AWS DevOps Agent**：按任务要求完全排除；其与 Kiro/CodeBuild 的官方参考架构不在本笔记的候选范围内。
- **AWS CodeBuild、CodePipeline、CodeDeploy、Amazon Bedrock、EventBridge 等**：它们可承载或触发下列能力，但自身不是 LLM/Agent 驱动的开发或交付能力，不能为凑数量独立列入。
- **Amazon Q Business、Q Apps、普通 Console 问答、通用 AWS MCP server**：未证明直接承担代码评审、测试、构建、现代化或交付闭环，排除。
- **Kiro Web**：虽可自主开 PR，但截至观察日为 Preview；其事实仍可由 Kiro CLI 的 headless CI 路径和 Kiro GA 的 IDE/CLI 路径更直接覆盖，因此不单列。

## 候选能力总表

| 候选能力 | 产品层级 / 阶段（截至观察日） | 核心机制（来源明确陈述） | CI/CD 接点 | 生命周期与事实边界 | 结论 |
|---|---|---|---|---|---|
| Amazon Q Developer：IDE 代码评审、`/test`、Java transformation | Q Developer 基座于 2024-04-30 GA；自动单测在 VS Code/JetBrains 于 2024-12-03 GA；Java transformation 是当前 Q IDE/CLI 文档能力 | IDE 评审同时使用 generative AI 和规则检测；`/test` 生成并在征得同意后加入测试；Java transformation 先 build，生成 diff，用户验证/接受 | 开发者在 PR 前运行；Java transformation 在本地/CLI build 中运行测试并输出或提交到新 branch | Java transformation 要求 Maven 项目、支持的 JDK 与可用 build；访问私网资源的单测会失败。Q 的生成或变换不是发布授权 | **强相关，但开发者前置能力为主** |
| Amazon Q Developer for GitHub | Q Developer 的 GitHub 子能力；官方当前标 **Preview** | 新建/重开 PR 自动评审，给 threaded findings 和可选 commit 的 fixes；`/q review` 重评审；`/q` 可请求在 source branch 改动 | GitHub PR 的 review/fix 位置；后续 commits 不会自动重审 | 不执行 merge、build、test 或 deploy；自动评审不因同一 PR 的后续 commit 自动重跑 | **直接相关；状态必须写 Preview** |
| Kiro IDE + Kiro CLI v2 | Kiro 于 2025-11-17 GA；Kiro CLI 随 GA 发布。CLI v3 是 2026-06-17 的 Early Access，不代表 v2 退役或全产品 Preview | CLI Agent 可读写代码、运行 Bash、使用 MCP、steering/custom agents/hooks；headless 使用 API key、prompt 与预授信工具 | `kiro-cli chat --no-interactive` 明确用于 CI/CD，可做 code review、生成/运行测试、分析 build failure；可用 exit code、`--require-mcp-startup` | `--trust-all-tools` 会自动批准工具，最小权限应使用 `--trust-tools`；headless 无中途人工输入，官方未承诺自动 merge/deploy | **最直接的通用 Agent 执行面** |
| AWS Transform custom | AWS Transform 的 custom code modernization 子服务，**GA**（2025-12-01） | 用自然语言、文档和代码样例生成 transformation definition（`SKILL.md`、references、scripts）；在多仓库执行，自动从轨迹、开发者反馈和代码问题提取 transformation-specific lessons | 非交互 `atx custom def exec ... -x -t` 专为 CI/CD/bulk execution；可指定 `buildCommand`/validation command（如 Maven、pytest、npm test） | `-t` 绕过多数 guardrail；validation command 是质量反馈，不等于独立发布批准；lessons 不跨 transformation 或客户账户共享 | **最强的“可重复 Agent 改造 + 可执行验证”路径** |
| AWS Transform continuous modernization | AWS Transform 的 portfolio 子能力，**Preview**（2026-06-15） | 跨 GitHub/GitLab/Bitbucket/local repo 做技术债、安全、agentic-readiness、modernization-readiness 或 custom 分析；可按 finding 的 transformation definition 生成修复 | 自动建 branch 和 GitHub PR/GitLab MR/Bitbucket PR；可由 EventBridge Scheduler 周期扫描；修复后的常规 CI/审批仍由客户系统负责 | Preview，仅 US East (N. Virginia) 和 Europe (Frankfurt)；分析和 remediation 在客户 AWS 账户与凭证下运行；创建 PR 需要源端 write 权限 | **最完整的组合式持续现代化控制面，但仍是 Preview** |
| AWS Transform source-code containerization | AWS Transform 的 VMware migration job 中的 standalone/end-to-end containerization 工作流；当前文档未给该子能力单独 GA/Preview 标签 | Agent 分析源码、生成 Docker artifacts；build/test image 并扫描；生成 EKS Helm 或 ECS Terraform；部署 test infrastructure，再由用户验证后 cutover | 从 repo 输入延展到 image build/test、IaC validation、安全扫描、测试部署和生产 cutover | 每个关键输出须 review/approve；用户自己验证测试部署；仅面向尚未容器化应用，且入口属于 VMware migration job | **强相关但垂直，不是通用 pipeline 产品** |
| AWS Transform for mainframe testing | AWS Transform for mainframe 的 testing agents；首个面向 batch workload 的 testing release 为 **GA** | 生成、排序自然语言功能测试计划；生成 JCL test-data collection 与测试自动化脚本；比较工具验证现代化应用与 reference data | 测试计划/脚本可进入 functional equivalence、integration、non-functional 和最终 regression testing；可与外部工具整合 | 领域限定为 mainframe modernization，首个 GA 聚焦 batch；生成的数据采集脚本不负责提交 mainframe job 或传输数据，仍由系统程序员控制 | **强相关但仅适合主机现代化路径** |
| AWS Security Agent / AWS Continuum code scanning | AWS Security Agent 已归入 AWS Continuum；全仓 code review 和 PR-level code review/scan 为 **Preview**；按需渗透测试 GA 是另一条能力 | 全仓代码或 PR 评审安全问题和组织要求；可对私有 GitHub 自动生成修复 PR；可选 simulated validation 在隔离环境尝试利用漏洞 | PR comments/PR fixes；Transform continuous modernization 的 `security` 分析类型可调用该服务 | 全仓 review 发布公告明确为 Preview；自动 remediation 对私有 GitHub 才开 PR，公开 GitHub 只给 diff；simulated validation 仅限自包含、可 Docker 化应用 | **强相关的安全评审/修复路径；绝不能概括为一般代码质量评审或 GA** |

## 逐项事实与边界

### 1. Amazon Q Developer：开发阶段的生成、审查、测试与 Java 改造

#### 来源事实

- Amazon Q Developer 基座在 2024-04-30 GA；公告列出 IDE conversational coding、inline code generation、software-development agent 和 code transformation agent。[AWS What’s New，2024-04-30](https://aws.amazon.com/about-aws/whats-new/2024/04/amazon-q-developer-generally-available/)
- Q 的 IDE code reviews 检查代码安全和质量，结合 generative AI 与规则式 automatic reasoning；默认可依当前文件的 `git diff` 审查，若无 diff 则审查整个文件/项目。测试代码和开源代码在过滤中被排除。[Amazon Q Developer 文档，发布日期未列出；访问 2026-08-03](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/code-reviews.html)
- `/test` 在 VS Code 与 JetBrains 已 GA：Q 使用项目知识生成并添加 tests，添加前请求用户同意。[AWS What’s New，2024-12-03](https://aws.amazon.com/about-aws/whats-new/2024/12/amazon-q-developer-automatic-unit-test-generation/)
- Java transformation 当前支持 Maven Java 8/11/17 到 Java 17/21 的指定组合；Q 先 build，完成后提供 diff，由用户 verify/accept。命令行模式可用 `--no-interactive`，并把结果提交到 Q 创建的新 branch；Q 在 build 时运行项目 unit tests，因此私网数据库等外部依赖会使 transformation 失败。[Java transformation 文档](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/code-transformation.html)；[CLI transformation 文档](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/run-CLI-transformations.html)；[故障排查文档](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/troubleshooting-code-transformation.html)（均为动态文档，发布日期未列出；访问 2026-08-03）。

#### CI/CD 接点与事实边界

Q 将评审、单测生成和 Java 改造置于开发或变更产生阶段；Java transformation 的 build/test 和 branch 输出可接入既有 PR/CI。**来源没有证明 Q Developer 会自行批准 PR、绕过测试，或自动发布。** 因而它是 agentic change author/reviewer，不是 CI orchestration 或 deployment control plane。

### 2. Amazon Q Developer for GitHub：PR 评审的 Preview 子能力

#### 来源事实

- 新建或重开的 GitHub PR 会触发自动 review，产生 summary、threaded findings 与可提交的 fix；用户可在 PR 中用 `/q` 请求 Agent 修改 source branch，或用 `/q review` 主动重新评审。
- 官方明确将该能力标为 **preview release**；后续 commit 不会自动重触发 review。

来源：[Reviewing code with Amazon Q Developer in GitHub](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/github-code-reviews.html)（动态文档，发布日期未列出；访问 2026-08-03）。

#### CI/CD 接点与事实边界

它落在 GitHub PR 的 review/fix 环节，且可以用项目根目录 `.amazonq/rules` 的 Markdown 规则约束审查。但 merge、测试、构建和部署仍不由该文档承诺；将它描述为“自动 CI gate”或“自动交付”均超出证据。

### 3. Kiro：GA 的 Agent 编程面，CLI 可作为 CI 任务运行

#### 来源事实

- Kiro 在 2025-11-17 GA；公告同时发布 Kiro CLI，说明 CLI 与 IDE 共用 steering、MCP 和订阅。Kiro CLI 可读写本地文件、调用 API、运行 Bash；GA 公告只说明当时包含的模型与功能，不能外推成当前所有 surface 的模型承诺。[Kiro 官方公告，2025-11-17](https://kiro.dev/blog/general-availability/)
- Kiro CLI headless mode 明确可作为 CI/CD pipeline 的一部分，使用 `kiro-cli chat --no-interactive`；可授予 `--trust-tools` 或 `--trust-all-tools`，官方例子包括 PR security review、写并运行测试、解释 build failure，并建议以 exit code 处理结果。[Kiro CLI 文档，页面更新 2026-06-04；访问 2026-08-03](https://kiro.dev/docs/cli/headless/)
- CLI v3 为 2026-06-17 的 Early Access opt-in；这只约束 v3，不改变上面 Kiro/CLI v2 的 GA 结论。[Kiro changelog，2026-06-17](https://kiro.dev/changelog/cli/2-8/)

#### CI/CD 接点与事实边界

Kiro 是此范围中最直接可嵌入 CI 的通用 coding agent：`diff/log/prompt → headless agent → test/review/fix → pipeline exit code/后续 gate`。但 `--trust-all-tools` 代表运行者预先放宽工具确认；它不是测试 oracle，也没有默认的 merge、环境提升或部署授权。应将最小工具权限、独立 test/lint/scan、分支保护和人工 merge 留给 pipeline/SCM policy。

### 4. AWS Transform custom：GA 的可版本化改造与验证闭环

#### 来源事实

- AWS Transform custom 于 2025-12-01 GA，面向组织级 version/runtime/framework/API/language 等代码现代化。[AWS What’s New，2025-12-01](https://aws.amazon.com/about-aws/whats-new/2025/12/transform-custom-organization-wide-modernization/)
- transformation definition 是 `SKILL.md` 加可选 references/scripts；可草稿、测试和发布到账号内 registry。agent 从执行轨迹、开发者反馈与遇到的代码问题抽取 lessons；lessons 可审阅、归档或删除，且不跨 transformation/账户共享。[AWS Transform custom 文档，发布日期未列出；访问 2026-08-03](https://docs.aws.amazon.com/transform/latest/userguide/custom.html)
- non-interactive execution 专为 CI/CD integration 和 bulk execution；`-t` 自动信任工具并绕过多数 guardrail。`build-command`/validation command 可指定 `mvn clean install`、`pytest`、`npm test` 等，失败输出是 continual learning 的关键质量输入。[Workflows](https://docs.aws.amazon.com/transform/latest/userguide/custom-workflows.html)；[command reference](https://docs.aws.amazon.com/transform/latest/userguide/custom-command-reference.html)（动态文档，发布日期未列出；访问 2026-08-03）。

#### CI/CD 接点与事实边界

这是一条可复用的“Agent 改造—可执行验证—经验回流”路径。**分析推断**：它最适合把固定 build/test contract 作为 Agent 大规模改造的收敛条件，而非让 LLM 直接决定质量或发布。官方也明确警告 `-t` 的风险；CI headless 只意味着无需交互，不意味着可绕开独立检查、审批或变更管理。

### 5. AWS Transform continuous modernization：Preview 的跨仓库扫描和 PR 修复控制面

#### 来源事实

- 该 Preview 于 2026-06-15 发布，能自动发现、排序、修复企业软件组合中的技术债，也可评估 agentic-readiness/modernization-readiness；官方明确写有 human oversight，区域为 US East (N. Virginia) 与 Europe (Frankfurt)。[AWS What’s New，2026-06-15](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-transform-continuous-modernization/)
- 当前文档支持 GitHub、GitLab、Bitbucket、local source，含 quick/comprehensive tech debt、security、agentic-readiness、modernization-readiness、custom analysis；remediation 会基于 finding 的 transformation definition 自动创建 branch 与 PR/MR，EventBridge Scheduler 可周期运行 analysis。[continuous modernization 文档](https://docs.aws.amazon.com/transform/latest/userguide/continuous-modernization.html)；[working with continuous modernization](https://docs.aws.amazon.com/transform/latest/userguide/ct-working-with.html)（动态文档，发布日期未列出；访问 2026-08-03）。
- AWS Transform agents 可由 Kiro Power、agent plugins 与 AWS Transform MCP server 进入 Kiro、Claude、Cursor、Codex；MCP server 提供 workspace/job/HITL/artifact 等工具。此为 Transform 的接入面，不是一项独立 CI/CD 产品。[AWS What’s New，2026-05-14](https://aws.amazon.com/about-aws/whats-new/2026/04/aws-transform-developer-tools/)；[developer tools 文档](https://docs.aws.amazon.com/transform/latest/userguide/developer-tools.html)（动态文档，发布日期未列出；访问 2026-08-03）。

#### CI/CD 接点与事实边界

链路是 `portfolio scan → finding → transformation remediation → branch/PR/MR → 既有 CI 与审批`。**来源事实**只到 PR/MR/branch 的生成；“自动 merge/deploy”没有被证明。因为它仍是 Preview，任何成熟度、成功率或大规模自治结论都应阻塞在客户试点验证之后。

### 6. AWS Transform：容器化和主机测试是完整但垂直的现代化工作流

#### Source-code containerization

- Agent 从 GitHub/Bitbucket/GitLab/zip 源码生成 Docker artifacts，build/test image 并发布至 ECR，生成 EKS Helm 或 ECS Terraform，自动 validation/security scan；随后部署 test infrastructure，用户验证，再生产 cutover。每一个关键输出在流程中需要 review/approve。[Source code containerization](https://docs.aws.amazon.com/transform/latest/userguide/transform-containers.html)；[test deployment](https://docs.aws.amazon.com/transform/latest/userguide/transform-containers-step-test-deploy.html)（动态文档，发布日期未列出；访问 2026-08-03）。
- 它只能经 AWS Transform 的 VMware migration job 启动，适用于尚未容器化的应用；不是任意已有容器工作负载的 CI/CD 替代。

#### Mainframe testing

- AWS Transform for mainframe 的 AI-powered testing 于 2025-12-01 公告；官方工程文随后明确首个 GA release 聚焦 batch workload，覆盖功能测试计划、test-data collection scripts 和 test automation scripts。functional tests 可作为后续 integration/non-functional 与最终 regression testing 的基础。[AWS What’s New，2025-12-01](https://aws.amazon.com/about-aws/whats-new/2025/12/transform-mainframe-testing-automation/)；[AWS Migration & Modernization Blog，2025-12-23](https://aws.amazon.com/blogs/migration-and-modernization/accelerating-mainframe-modernization-testing-with-aws-transform/)
- 生成 data collection script 不等于自动提交 mainframe job 或搬运数据；后两项仍在 mainframe system programmer 控制下。此边界由同一官方博客明确说明。

#### CI/CD 接点与事实边界

二者都把 Agent 产物接到实际 build/test/validation/deploy 任务，但目标是迁移/现代化项目，而非日常任意 PR 的 CI runner。可以用于“Agent 生成现代化交付工件，传统验证和批准决定能否 cutover”的叙事，不能泛化成 AWS 已提供全自动通用 delivery agent。

### 7. AWS Security Agent / AWS Continuum：安全代码评审与可审阅修复

#### 来源事实

- AWS Security Agent 的 full repository code review 于 2026-05-12 发布为 Preview：分析全仓架构、信任边界和数据流，并为发现生成定位到文件/行的 code remediation。[AWS What’s New，2026-05-12](https://aws.amazon.com/about-aws/whats-new/2026/05/aws-security-agent-full-repository-code-review/)
- 其当前文档还说明：可对连接仓库做 automated PR analysis，在 PR/MR comments 给 findings；私有 GitHub 仓库可自动建修复 PR，公开 GitHub 为避免披露漏洞只附 diff；simulated validation 只对 self-contained、dockerizable app 适用。[AWS Security Agent 文档](https://docs.aws.amazon.com/securityagent/latest/userguide/what-is.html)；[code review 过程](https://docs.aws.amazon.com/securityagent/latest/userguide/perform-code-review-scan.html)（动态文档，发布日期未列出；访问 2026-08-03）。
- 2026-06-17 后，AWS Security Agent 归入 AWS Continuum；公告将 code scanning 明确标为 Preview，同时说明 durable fixes 仍通过客户自己的 review/deployment process 路由。[AWS What’s New，2026-06-17](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-continuum/)
- Transform continuous modernization 的 `security` analysis 调用该服务，AWS 托管策略变更记录了获取 scan artifacts/findings 的权限，说明这是受支持的产品间集成，而非推测。[AWS Transform managed policies 文档，2026-06-30](https://docs.aws.amazon.com/transform/latest/userguide/security-iam-awsmanpol.html)

#### CI/CD 接点与事实边界

它能成为 PR security review/PR remediation 的一环，也可在 Transform 的 portfolio remediation 路径中被调用。**分析推断**：其差异化不在替代普通 lint/SAST，而在以应用上下文与（可选）隔离模拟验证提升发现和修复的可执行性。该推断不应掩盖事实：代码扫描 Preview、私有 GitHub 才可自动开修复 PR、最终 merge/deploy 仍交给客户流程。

## 可用路径（分析推断，非 AWS 对某一组合的产品承诺）

```text
开发者前置：
Amazon Q Developer（生成 / IDE review / /test / Java transform）
  → branch 或 PR → 常规 CI build/test/scan → 人审与部署

通用 Agent CI：
CI runner → Kiro CLI headless（最小工具信任）
  → 生成/修复/运行测试 → exit code + 独立 gate → PR/merge policy

持续现代化：
AWS Transform continuous modernization（scan / finding）
  → Transform custom remediation → branch + PR/MR
  → 现有 CI/审批 → 合并与发布

现代化交付：
AWS Transform containerization / mainframe testing
  → 生成工件 + build/test/validation → 人工 review/approve
  → test environment → cutover

安全修复：
AWS Security Agent / Continuum code scanning
  → PR comment 或 remediation PR → 常规 CI/安全 gate → 人审部署
```

## 候选优先级

| 优先级 | 适用问题 | 首选能力 | 选择理由 | 不应说成 |
|---|---|---|---|---|
| 1 | 大规模技术债、依赖/框架升级、跨仓库修复 | **AWS Transform continuous modernization + Transform custom** | 具备 portfolio analysis、定期扫描、版本化 transformation、PR/MR 输出和 validation command | 已 GA 的自治交付系统，或可自动 merge/deploy |
| 2 | 在现有 CI 中运行 coding agent | **Kiro CLI v2 headless** | 官方直接把该模式定位为 CI/CD；工具信任、MCP 启动和 exit code 可配置 | 自带可靠测试判定、默认安全或自动发布 |
| 3 | 日常开发、IDE review、补单测、Java 升级 | **Amazon Q Developer** | Q 基座和 `/test` GA，PR review/branch 输出明确 | Q for GitHub 已 GA，或它接管 CI control plane |
| 4 | 安全代码评审与修复 PR | **AWS Security Agent / Continuum code scanning** | 支持 PR-level/full-repo review、remediation PR 和受限 simulated validation | 普通质量 review、全能力 GA、自动安全发布 |
| 5 | 应用容器化或 mainframe 现代化中的验证重建 | **AWS Transform containerization / mainframe testing** | Agent 产物直接连接 image build/test、IaC、test infra 或 functional equivalence testing | 通用日常 CI/CD 平台 |

## 官方来源与时间核对

| 来源 | 发布日期 / 页面更新时间 | 访问日期 | 用途 |
|---|---:|---:|---|
| [Amazon Q Developer GA](https://aws.amazon.com/about-aws/whats-new/2024/04/amazon-q-developer-generally-available/) | 2024-04-30 | 2026-08-03 | Q 基座与产品层级 |
| [Q unit test generation](https://aws.amazon.com/about-aws/whats-new/2024/12/amazon-q-developer-automatic-unit-test-generation/) | 2024-12-03 | 2026-08-03 | `/test` GA 与同意边界 |
| [Q GitHub code reviews](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/github-code-reviews.html) | 未列出（动态文档） | 2026-08-03 | Preview、自动触发和重审边界 |
| [Q IDE code reviews](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/code-reviews.html) | 未列出（动态文档） | 2026-08-03 | generative AI + rules、`git diff` 范围 |
| [Q Java transformation](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/code-transformation.html) | 未列出（动态文档） | 2026-08-03 | 支持矩阵、build/diff/accept |
| [Kiro GA](https://kiro.dev/blog/general-availability/) | 2025-11-17 | 2026-08-03 | Kiro/CLI GA |
| [Kiro CLI headless](https://kiro.dev/docs/cli/headless/) | 页面更新 2026-06-04 | 2026-08-03 | CI/CD、信任工具、exit code |
| [Kiro CLI v3 EA](https://kiro.dev/changelog/cli/2-8/) | 2026-06-17 | 2026-08-03 | v3 仅为 Early Access |
| [Transform custom GA](https://aws.amazon.com/about-aws/whats-new/2025/12/transform-custom-organization-wide-modernization/) | 2025-12-01 | 2026-08-03 | custom 状态 |
| [Transform custom/workflows](https://docs.aws.amazon.com/transform/latest/userguide/custom-workflows.html) | 未列出（动态文档） | 2026-08-03 | CI headless、validation、trust 边界 |
| [Transform continuous modernization Preview](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-transform-continuous-modernization/) | 2026-06-15 | 2026-08-03 | Preview、区域和 human oversight |
| [Continuous modernization guide](https://docs.aws.amazon.com/transform/latest/userguide/continuous-modernization.html) | 未列出（动态文档） | 2026-08-03 | analysis/remediation/PR/EventBridge 机制 |
| [Transform developer tools](https://docs.aws.amazon.com/transform/latest/userguide/developer-tools.html) | 未列出（动态文档） | 2026-08-03 | Kiro Power、plugins、MCP、HITL tools |
| [Transform containerization](https://docs.aws.amazon.com/transform/latest/userguide/transform-containers.html) | 未列出（动态文档） | 2026-08-03 | 生成、build/test、IaC、test infra、审批 |
| [Transform mainframe testing announcement](https://aws.amazon.com/about-aws/whats-new/2025/12/transform-mainframe-testing-automation/) | 2025-12-01 | 2026-08-03 | 自动测试能力与区域 |
| [Transform mainframe testing blog](https://aws.amazon.com/blogs/migration-and-modernization/accelerating-mainframe-modernization-testing-with-aws-transform/) | 2025-12-23 | 2026-08-03 | first GA、batch 范围与执行边界 |
| [Security Agent full-repo review](https://aws.amazon.com/about-aws/whats-new/2026/05/aws-security-agent-full-repository-code-review/) | 2026-05-12 | 2026-08-03 | Preview 与代码 remediation |
| [AWS Continuum](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-continuum/) | 2026-06-17 | 2026-08-03 | 归属变更、code scanning Preview、review/deployment 交给客户流程 |
| [Security Agent code review guide](https://docs.aws.amazon.com/securityagent/latest/userguide/perform-code-review-scan.html) | 未列出（动态文档） | 2026-08-03 | 自动 remediation 与 simulated validation 约束 |

## 不能从现有证据得出的结论

- 没有证据证明 Amazon Q Developer、Kiro、AWS Transform 或 AWS Security Agent 可替代客户的 CI runner、branch protection、测试 oracle、人工审批或 deployment policy。
- 没有证据证明 Kiro headless、Transform remediation 或 Security Agent remediation 默认自动合并或自动部署。
- 不能用 Kiro CLI v3 Early Access 降级 Kiro/CLI v2 的 GA，也不能用 AWS Security Agent 的 on-demand penetration-testing GA 升级其 Preview 的 code-scanning/code-review 能力。
- AWS Transform continuous modernization 和 Security Agent code scanning 仍是 Preview；任何生产成熟度、效果指标和自治等级都需要客户侧试点评测，不能由产品文档补全。
