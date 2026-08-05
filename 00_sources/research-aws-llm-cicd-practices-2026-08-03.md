---
title: AWS、Amazon Q 与 Kiro 的 LLM CI/CD 实践核验
date: 2026-08-03
as_of: 2026-08-03
status: research-complete
confidence: medium-high
scope: 仅使用 AWS/Amazon Q/Kiro 官方文档、AWS 工程博客与 AWS 官方资料；回答“AWS 有没有 LLM 明确参与构建、测试或 CI 的实践”。
---

# AWS、Amazon Q 与 Kiro 的 LLM CI/CD 实践核验

## 结论先行

**AWS 有，而且 AWS DevOps Agent Release Management 是最值得单独讲的 LLM CI/CD 对象。** 它不是传统 ML：AWS FAQ 明确称 DevOps Agent 使用 Amazon Bedrock foundation models。其 Release Management 会先对变更做 release-readiness review，再为该次变更生成、执行 change-specific release test plan；测试运行于客户提供的、已部署的预发布环境，可作为 GitHub Actions/GitLab CI 的一个 stage 回写结果。

但它截至 2026-08-03 仍是 **Preview**（2026-06-17 发布，US East N. Virginia），不是 Amazon 内部生产实践。因此应将它讲作一项前沿产品机制，而非“AWS 已在内部大规模验证的范式”。

AWS 还有两种不同强度的证据：

1. **Amazon 内部的 Q Developer Java 升级 campaign 是已公开的生产实践。** Q Developer 生成升级计划和代码变更，在目标 Java 版本中运行单元、集成测试；Amazon 中央团队选择迁移波次、管理内部 build system，并称六个月升级了超过一半生产应用，79% 自动生成的 code-review 被原样应用。它证明：LLM 生成大批量改造后，CI/build/test 从“检查一次人类提交”变成“验证 Agent 迁移 campaign”。
2. **AWS Transform custom 是面向 Agent 的 CI 改造能力。** Agent transformation definition 可以以 headless/non-interactive 方式进入 CI/CD，调用显式的 build/validation commands，并从执行反馈抽取 lessons 改进后续运行。它是产品能力，不是 AWS 自身内部采用案例。

但如果第六页的核心是“LLM 如何重写测试机制”，**Meta JiTTest 仍优于 AWS**：Meta 在每次 Diff 提交时生成并运行不落库的临时测试；AWS DevOps Agent 则将变更感知的 release test plan 产品化为预发布 CI stage，另有 Q Developer 支撑大规模代码改造进入既有 build/test 验证。两者的问题定义不同。

## 事实分层与候选判断

| 对象 | LLM/Agent 的明确角色 | 公开状态 | 是否是 Amazon/AWS 内部生产实践 | 是否适合替代 Buildkite 与 Dagger 同页 |
|---|---|---|---|---|
| **AWS DevOps Agent Release Management** | Bedrock FMs 做 release-readiness review；为 change 生成、执行 release tests | 2026-06 Preview，US East N. Virginia | 否，产品能力 | **AWS 首选**：讲“LLM 生成发布前验证计划”，不等于自愈 |
| **Amazon Q Developer Java transformation campaign** | 生成升级计划、依赖/API 改动；在目标版本编译并运行 app 的 unit/integration tests | 官方产品与 Amazon 内部 campaign 经验 | **是** | 可作为“大规模 Agent 改造”备选 |
| **AWS Transform custom** | Agent 定义并执行 code transformation；以 build/test command 验证；从执行/反馈提炼 lesson | 当前官方文档能力；本页未核验 GA 标签 | 否，产品能力 | **可作为 AWS 产品页对象**，但不如 Meta 的测试机制新颖 |
| **Kiro CLI + AWS DevOps Agent + CodeBuild** | DevOps Agent 出 mitigation plan；Kiro headless 在 CodeBuild 修改代码、开 PR | 2026-07 官方 sample/reference architecture | 否，样例 CloudFormation 应用 | 不推荐：与既有 CI 自愈/Harness Agent 高度重叠 |
| **Amazon Q Developer for GitHub / unit-test generation** | PR 自动 review/生成可提交 fix；IDE 中生成 unit tests | Unit-test agent GA；GitHub review Preview | 未找到 Amazon 内部 CI 大规模采用证据 | 不推荐：与 GitHub Agentic Workflow 页重叠 |
| **Audible + Q Developer** | 以代码上下文生成 unit/edge-case tests、迁移 JUnit tests | AWS 署名案例 | Amazon 旗下 Audible 的团队实践 | 可做侧证，但生成测试后由开发者审阅整合，非 CI runtime 机制 |

## 一、AWS DevOps Agent Release Management：AWS 的首选页面对象

### 已核验的 LLM 与机制

- **LLM 明确参与。** AWS FAQ 明确写明 DevOps Agent 使用 Amazon Bedrock foundation models；官方工程博客说明其构建在 Bedrock AgentCore 上，而非只是在日志页面叠加聊天界面。[AWS DevOps Agent FAQ](https://aws.amazon.com/devops-agent/faqs/)；[AWS DevOps Blog](https://aws.amazon.com/blogs/devops/leverage-agentic-ai-for-autonomous-incident-response-with-aws-devops-agent/)

- **Release readiness review。** Agent 连接 repository 与 CI/CD pipeline 后，索引代码、构建跨 repository/cloud dependency knowledge graph；在代码生成、PR/MR 或 CI/CD 中评估变更及其依赖影响。该层可以先在 AWS-managed isolated environment build/run 代码并做 lightweight user-journey checks，但不能等同于完整测试或自动合并。[Release management 文档](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-release-management-index.html)；[AWS 发布公告](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-release-management/)

- **Release testing。** 与静态回归套件不同，agent 根据该次 code change 或用户指定 test intent **生成 test plan**，在客户提供、已部署的 web/API 应用环境运行：Web 路径含浏览器交互和 visual inspection，API 路径含 HTTP、schema 与 error handling；结果包括失败、受影响功能、复现步骤和建议修复。支持从 IDE、chat 或 CI/CD 触发。[Release testing 文档](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-testing.html)

- **进入 CI 的形式。** 官方 `aws-actions/devops-agent-release-testing@v1` GitHub Action 可在 staging 部署后触发 Agent，并把结果回写为 GitHub Check Run；文档也声明可作为 GitLab CI pipeline stage。它将 LLM 的动态测试计划纳入既有 pipeline，而不是替代 Runner 或 CI control plane。[Release testing 文档](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-testing.html)；[Working with DevOps Agent](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent.html)

### 状态与边界

Release Management 于 **2026-06-17** 发布为 Preview，release-readiness review 与 autonomous release testing 当前仅在 **US East (N. Virginia)** 预览且 preview 期间无额外费用。它提供的是 AWS 产品能力，未发现 AWS 公开其内部采用规模、成功率或持续生产结果的证据。[AWS What’s New](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-release-management/)；[Release history](https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html)

### 它和 Meta JiTTest 的差异

| 维度 | Meta JiTTest | AWS DevOps Agent Release Testing |
|---|---|---|
| LLM 生成的测试对象 | 针对 Diff 的 mutant 与临时 catching test | 针对 change 的 release test plan 与 UI/API exploratory tests |
| 运行位置 | Meta 内部 CI，比较 parent 和 Diff | 客户提供的、已部署的预发布应用环境 |
| 验证重点 | 在代码落地前捕获特定 Diff 引入的回归 | 在合入/发布前验证 user journey、integration 与 API behavior |
| 公开状态 | Meta 已披露的内部 production workflow | **Preview 产品**，尚无公开大规模生产效果 |
| 适合的叙事 | “测试成为按 Diff 临时生成的运行时工件” | “发布前验证从静态 suite 变成 change-specific agent plan” |

因此，两者都不是传统 ML：Meta 的优势是已有内部生产实证；AWS 的优势是把动态验证计划产品化为 CI stage，并面向真实部署环境。

### Dagger 同页主张与选择建议

若要优先选择一个 **AWS 产品**，可以用：

> **固定的 Build/Test 图仍由 Dagger 复用；AWS DevOps Agent 则用 Bedrock FMs 为每次变更生成发布前验证计划，并在预发布环境执行。CI 从运行固定测试，扩展为按变更生成验证。**

建议标题：

> **LLM 开始生成发布前的验证计划，而不只生成代码**

这是一个能力页，不要把重点落在安全/可信。唯一需要保留的产品边界是：Release Management 为 Preview，且它生成/执行测试并回写 Check Run，不证明自动发布、自动合并或 AWS 内部大规模采用。

**最终选择：**

- 要“公开大公司内部实践 + 论文级效果”→ 选 **Meta JiTTest**；
- 要“可借鉴、当前有产品接口、能进入 CI stage 的 Agent 能力”→ 选 **AWS DevOps Agent Release Management**。

## 二、Amazon 内部 Q Developer 改造实践：最强 AWS 内部采用证据

### 已核验事实

- Amazon Q Developer code-transformation agent 面向 Java 8/11→Java 17 升级，先生成 transformation plan，识别库升级和 deprecated code replacement，再提出代码变更；官方表述的目标是使变换后应用在 Java 17 编译成功。[AWS DevOps Blog，2024-10-03](https://aws.amazon.com/blogs/devops/accelerate-application-upgrades-with-amazon-q-developer-agent-for-code-transformation/)

- 同一官方文章明确说明：Q Developer 在目标版本编译应用时，**运行应用已有的 unit tests 与 integration tests**。这不是 LLM 自己充当测试 oracle；LLM 生成改造，现有可执行测试是校验器。[AWS DevOps Blog](https://aws.amazon.com/blogs/devops/accelerate-application-upgrades-with-amazon-q-developer-agent-for-code-transformation/)

- Amazon 内部使用 Q Developer 进行 company-wide Java upgrade campaigns。中央团队管理 Amazon 内部 build system、划分 migration wave、估计成功可能性与剩余工程工作；官方称 Amazon 在六个月内升级超过 50% 的生产应用，并称 79% 的 auto-generated code reviews 被无改动应用。数值是 AWS 厂商自述，不能外推为行业平均或任意 Agent 的通过率。[同上](https://aws.amazon.com/blogs/devops/accelerate-application-upgrades-with-amazon-q-developer-agent-for-code-transformation/)

### 能力变化

```text
传统 CI
开发者提交一个变更 → build / test / review

Amazon Q 升级 campaign
迁移波次选择 → Agent 生成改造计划与代码变更
                → 内部 build system 编译 + 单元/集成测试
                → 人与团队处理剩余适配工作
```

这意味着 CI 的价值不再只是逐 PR gate，而成为 **让 Agent 在大量异构应用上重复执行、并以同一 build/test 合同度量改造质量的验证底座**。这是从资料得出的架构推断；AWS 没有公开称其内部 build system 使用 Dagger，也没有公开 Q Developer 的完整模型、提示词或编排实现。

### 适合与 Dagger 共页的主张（AWS 版本）

> **LLM 让一次变更变成一场代码改造 campaign；Dagger 将 build/test 写成可复用执行图，Amazon Q 证明大规模 Agent 改造必须回到同一套可执行验证合同。**

或更短：

> **当 Agent 批量改代码，CI 从 PR Gate 变成改造质量的验证工厂。**

这是 Dagger 和 Amazon Q 的互补架构，不是产品集成。Dagger 的 Module/Function DAG 可以承载可复用 build/test 逻辑；Amazon Q 的公开事实是生成改造并运行既有测试，不等于 Q 可调用 Dagger Function。

### 与 Meta JiTTest 的取舍

| 维度 | Meta JiTTest | Amazon Q 内部 Java Campaign |
|---|---|---|
| LLM 改变的对象 | 每个 Diff 的测试工件 | 大量应用的代码改造计划与补丁 |
| CI/test 的作用 | 临时生成、运行、筛选针对该 Diff 的 catching tests | 编译与运行既有 unit/integration tests，验证 Agent 生成的改造 |
| 最适合讲的能力 | 从静态测试集到按 Diff 生成验证图 | 从 PR gate 到可规模化的 Agent 改造验证 campaign |
| 公开生产证据 | Meta 论文记录 production workflow | AWS blog 记录 Amazon 内部 campaign |
| 与 Dagger 的叙事张力 | “执行图稳定、验证图动态生成” | “可复用执行图，承载大规模 agentic transformation 验证” |

**优先级判断：** 若页面目标是“最具技术新意、且直接关联 LLM 测试”，选 Meta；若听众更在意 AWS/大型企业如何运营 Agent coding 迁移，Amazon Q campaign 是可信备选。

## 三、AWS Transform custom：将 Agent 直接接入 CI/CD 的产品化机制

AWS Transform custom 把自然语言、文档和样例代码变成 transformation definition；可在多个 codebase 上运行，并把开发者反馈和 transformation 中遇到的 code issues 异步抽成 lessons，供后续执行使用。[AWS Transform Custom 文档](https://docs.aws.amazon.com/transform/latest/userguide/custom.html)；[Workflows 文档](https://docs.aws.amazon.com/transform/latest/userguide/custom-workflows.html)

它的关键机制不是“模型一次性改代码”，而是把生成式 Agent 放在一个可重复运行的变换闭环中：

1. `atx custom def exec ... -x -t` 支持 non-interactive/headless execution，官方明确标为用于 **CI/CD integration 和 bulk execution**；
2. transformation 可接收 `buildCommand` 和 `validationCommands`，例如 `mvn clean install`、`pytest`、`npm test`；官方建议显式提供可返回失败的 validation command 来改善变换质量；
3. 默认从每次 transformation 的开发者反馈和 code issues 提炼 lessons，后续运行自动使用；可用 `-d` 禁用学习。

因此，AWS 的产品化改变可以概括为：

> **CI 的输入从固定源码提交扩展为 Agent transformation；测试命令既是质量门，也是 Agent 下一轮改造的反馈数据。**

边界：`-x -t` 会自动信任工具，官方也提示它绕过多数 guardrails；因此不能把 headless 模式描述成无需人工或无需独立验证的自动发布。该功能在当前文档中存在，但本文未检索到适用于所有 Transform custom 能力的一致 GA/Preview 标记，页面应写“当前官方文档能力”，不擅自标 GA。

### AWS Transform 的其他明确测试能力

- Source-code containerization 中，AI agent 分析 source code、生成 Docker artifacts；服务 build/test container image、生成 IaC，并部署 test infrastructure 进行 validation。[Source code containerization 文档](https://docs.aws.amazon.com/transform/latest/userguide/transform-containers.html)
- AWS Transform for mainframe 于 2025-12-23 发布 AI-powered testing capabilities 的官方博客：首个 GA release 聚焦 batch workload，能自动生成、优先排序自然语言 functional test plan，并生成 test-data collection 与 test-automation scripts；这些测试可继续用于 integration/non-functional testing 和最终 regression testing。[AWS Migration & Modernization Blog](https://aws.amazon.com/blogs/migration-and-modernization/accelerating-mainframe-modernization-testing-with-aws-transform/)

这些适合“LLM/Agent 如何为现代化改造重建测试资产”的专题，但它们偏 migration，不是通用 CI 替代。

## 四、Kiro CLI + AWS DevOps Agent + CodeBuild：明确，但只能称官方参考架构

AWS 2026-07-14 的官方博客给出了完整串联：CloudWatch alarm → AWS DevOps Agent 调查并给出 mitigation plan → EventBridge/Lambda/SQS → **CodeBuild headless 运行 Kiro CLI** → Kiro 根据 steering file 修改 CloudFormation/application code → 建 feature branch 和 CodeCommit PR → 人工批准合并后才触发 deployment pipeline。[AWS DevOps Blog](https://aws.amazon.com/blogs/devops/automated-incident-remediation-with-aws-devops-agent-and-kiro-cli/)

LLM/Agent 的明确部分是：

- Kiro 是基于 Amazon Bedrock、使用多个 foundation models 的 agentic coding service；其 agent 可生成代码、文档和测试。[Kiro 官方概览](https://aws.amazon.com/documentation-overview/kiro/)
- 在该 CodeBuild 样例中，Kiro CLI 2.0 以 API key 在 headless mode 运行；AWS 说明其拥有与交互模式相同的 tools、agents 和 capabilities。
- `--trust-tools=read,write,grep,shell` 允许其在没有终端人工确认时用这些类别的工具执行修复。

但它明确是“we demonstrate ... using a **sample CloudFormation application**”的 reference architecture，不能说成 AWS 自己在生产中已通过 CodeBuild/Kiro 大规模实现 CI 自愈。它也与本 deck 的 CI 自愈、Harness Agent 主题同构：**告警/失败 → Agent 调查 → Agent 修改 → PR → 人工批准 → 部署**。不建议作为 Dagger 的第二对象。

## 五、Amazon Q Developer：有明确 LLM 测试与 PR 能力，但不宜作为这一页的主对象

### 可核验能力与状态

- Unit test generation agent 在 VS Code 和 JetBrains 中于 **2024-12-03 GA**；用户以 `/test` 触发，Q 利用项目知识生成并添加 tests，添加前征求同意。[AWS What’s New](https://aws.amazon.com/about-aws/whats-new/2024/12/amazon-q-developer-automatic-unit-test-generation/)
- Amazon Q Developer for GitHub 能在新建或重新打开的 PR 自动 review，输出 threaded findings 和可选择提交的 fixes；也可用 `/q` 要其直接在 source branch 创建 commit。但官方当前仍标为 **Preview**，且后续 commit 不自动再触发 review，需通过 `/q review` 重新发起。[AWS 文档](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/github-code-reviews.html)
- Amazon Q Developer agentic coding experience 的 AWS 公告称其可改文件、生成 diff 和运行命令，2025-05 公告所述体验由 Claude Sonnet 3.7 驱动；该模型信息属于当时公告，不能外推到所有 Q 特性或当前版本。[AWS What’s New](https://aws.amazon.com/about-aws/whats-new/2025/05/amazon-q-developer-agentic-coding-experience-ide/)

这些能力已是明确 LLM/Agent 参与，但若以它们作为 Dagger 同页第二对象，会重复本 deck 的 GitHub Agentic Workflow（PR 中的 review/fix/commit）与 CLI/MCP（agent 在终端/工具中运行）的已有主题。

## 六、Amazon 旗下 Audible 的测试生成实践：有用的旁证

Audible 的 AWS 署名工程案例称其用 Q Developer 以 Java 代码上下文生成 unit、edge-case 和 exception tests，并在人审后整合到 codebase；该案例报告 10+ key packages 获得单元测试覆盖、每个 test class 约节省一小时、5,000+ JUnit4 tests 迁移至 JUnit5、JDK8→17 迁移节省 50+ 小时。[AWS DevOps Blog，2025-10-10](https://aws.amazon.com/blogs/devops/boosting-unit-test-automation-at-audible-with-amazon-q-developer/)

这是 Amazon 生态内真实团队的实践，但测试由开发者在 IDE 中提示、审阅和整合，不是像 Meta JiTTest 那样在 Diff 提交时即时生成并运行的 CI runtime mechanism。它可作为“LLM 补齐存量测试资产”的佐证，不能替代 Meta 的页面主机制。

## 与现有页面的去重结论

## Comparison clarification：不能把 AWS 与 Meta 简化为“为 AI 生成的代码再生成测试”

**结论：不应这样表述。** 两者都使用 foundation-model/LLM agent 为**代码变更**生成验证，但公开资料都没有把输入限定为“由 AI/LLM 生成的代码”。AWS 的发布材料把 AI coding 导致的 PR 增长作为产品背景；Meta 的工程博客把 agentic development 作为 JiTTesting 的动机。二者的实际触发对象仍是一般的 code change / Diff。[AWS 发布公告](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-release-management/)；[Meta Engineering](https://engineering.fb.com/2026/02/11/developer-tools/the-death-of-traditional-testing-agentic-development-jit-testing-revival/)

| 比较维度 | AWS DevOps Agent Release Management | Meta JiTTest |
|---|---|---|
| **触发点** | Release readiness 可在 code generation、PR/MR、CI/CD 或 on-demand 触发；release testing 通常在部署到 test/staging 后，通过 IDE/chat 或 CI/CD stage 触发 | 官方博客描述为 Diff/PR 提交时；论文的生产 workflow 实际在夜间对前一天被高风险 targetter 选中的 Diff 运行，非每个 Diff 同步阻塞 |
| **输入** | code change 或用户给定 test intent、已连接 repository/pipeline 的上下文、test profile（已部署应用的 target URL 和 test type） | Diff 代码及其 parent；intent-aware 版本还使用 Diff 的 title/summary 来推断意图与风险，并利用类和既有测试构成生成上下文 |
| **LLM/Agent 生成物** | Bedrock FM 驱动的 agent 生成 **change-specific release test plan**，再在应用上进行 UI/API exploratory tests；公开资料没有说明其生成并保存独立的测试源代码文件 | LLM 推断变更风险，生成 mutants 与能区分 parent/mutant 的 catching test code；规则与 LLM assessor 再筛选失败信号 |
| **测试运行位置** | 客户提供、已经部署的 Web/REST API 预发布环境；UI 走浏览器/视觉检查，API 走 HTTP/schema/error handling | Meta 内部 CI（论文称其 Diff CI 为 Phabricator）；比较 parent 与 Diff。公开论文未披露具体 runner/环境拓扑 |
| **是否限定 AI-generated code** | **否。** 文档对象是 code changes；“AI code volume”是采用背景，不是输入过滤条件 | **否。** 文档对象是 Diff；“agentic development”是动机，不是 Diff 来源限制 |
| **测试工件是否保存** | 文档明确保留/呈现 execution details，如 timeline、test cases、UI screenshots、结果与建议修复；**未证明**生成 test plan 或脚本会提交进 repository，也未证明它必然在运行后丢弃 | **明确不落库。** Catching test 故意在当前 Diff 上失败，不能与该 Diff 一起 landing；工程博客说明 JiTTests 不 reside in codebase。它会被丢弃或由工程师处理其失败信号 |

因此，最严谨的一句对照是：

> **Meta 在内部 CI 中为高风险 Diff 生成临时、代码级的 catching tests；AWS 在预发布环境为代码变更生成并执行行为级 release test plans。二者都受 AI coding 需求推动，但并不只测试 AI 生成代码。**

适合 PPT 的能力主张应是“**为变化生成验证**”，不要写成“**为 AI 生成代码生成测试**”。

关键来源：[AWS Release Testing 文档](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-testing.html)、[AWS Release Management 文档](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-release-management-index.html)、[Meta JiTTest 论文](https://arxiv.org/pdf/2601.22832)、[Meta JiTTest 工程博客](https://engineering.fb.com/2026/02/11/developer-tools/the-death-of-traditional-testing-agentic-development-jit-testing-revival/)。

| 现有主题 | AWS 对象是否重叠 | 处理方式 |
|---|---|---|
| GitHub Agentic Workflow | Q for GitHub auto review、fix、commit 高度重叠 | 不选 Q for GitHub 做主对象 |
| Harness CI / Worker Agent | Kiro 在 CodeBuild 中 headless tool-use、产 PR 接近 agent runtime | 不选 Kiro CodeBuild 做主对象 |
| CI 自愈 | AWS DevOps Agent + Kiro 是从 incident 到 deployed fix 的闭环 | 保留为自愈页例证 |
| 基础设施 | CodeBuild 的 on-demand compute 只是 Kiro 运行位置 | 不把 CodeBuild 当核心创新 |
| CLI/MCP | Kiro Power 用 MCP，Transform custom 是 CLI | 不讲接口本身，讲“Agent transformation + executable validation contract” |

## 页面选型建议

1. **仍首选 Dagger + Meta JiTTest。** 主题最集中，且有 Meta 内部 production workflow 实证：静态测试资产 → 基于 Diff 的 LLM 临时验证资产。
2. **如果采用 AWS，首选 Dagger + AWS DevOps Agent Release Management；其次才是 Amazon Q Developer transformation campaign。** Kiro + CodeBuild 仍不适合作为主对象。

推荐 AWS 版本标题：

> **LLM 开始生成发布前的验证计划，而不只生成代码**

推荐主张：

> **Dagger 将 build/test 编译为可复用执行图；AWS DevOps Agent 用 Bedrock FMs 针对每次变更生成、执行预发布验证计划。CI 将固定检查扩展为变更感知的验证。**

不得写：

- “Kiro CodeBuild 是 AWS 内部大规模生产实践”；它是官方 sample/reference architecture。
- “Amazon Q 自动通过 CI/CD 发布”；公开流程仍包含人审、既有测试与部署 pipeline。
- “AWS Transform 所有能力 GA”；不同子能力和文档口径不同，本文只确认 mainframe testing 的 first GA release。
- “Dagger 已和 Q/Kiro/Transform 原生集成”；没有这项公开证据。

## 时间、状态与证据强度

| 资料 | 发布/状态 | 访问时间 | 证据强度 |
|---|---|---|---|
| Q Developer Java transformation / Amazon internal campaign | 2024-10-03 工程博客 | 2026-08-03 | 高：官方对内部实践的具体陈述；效果为厂商自述 |
| AWS DevOps Agent Release Management | 2026-06-17 Preview；US East (N. Virginia) | 2026-08-03 | 高：官方产品机制与 Bedrock FM 证据；低：内部生产规模未披露 |
| Q unit-test generation | 2024-12-03 GA | 2026-08-03 | 高：官方发布 |
| Q for GitHub code review | 当前文档标 Preview | 2026-08-03 | 高：官方文档，产品状态明确 |
| AWS Transform custom workflow | 当前官方文档；未在本研究内核验统一 GA 状态 | 2026-08-03 | 高：官方机制；中：生产采用证据缺口 |
| AWS Transform mainframe testing | 2025-12-23，首个 GA release 聚焦 batch | 2026-08-03 | 高：官方发布，但领域垂直 |
| Kiro + DevOps Agent + CodeBuild | 2026-07-14 官方博客 / sample | 2026-08-03 | 高：架构机制；低：内部/客户生产规模未披露 |
| Audible + Q test automation | 2025-10-10 AWS 署名案例 | 2026-08-03 | 中：Amazon 旗下团队案例，指标不外推 |
