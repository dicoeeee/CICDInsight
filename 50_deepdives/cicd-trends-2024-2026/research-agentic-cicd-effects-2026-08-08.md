---
title: 五项智能体 CI/CD 能力的比较性效果证据
date: 2026-08-08
as_of: 2026-08-08
status: research-input
confidence: medium
tags:
  - research/agentic-cicd
  - evidence/effects
  - cicd-trends
---

# 五项智能体 CI/CD 能力的比较性效果证据

> [!abstract]
> 本笔记把“运行次数、拉取请求数量”等降为采用规模，核心只记录可比的“基线或对照 → 使用后 → 变化”。五项直接能力是 GitHub Agentic Workflows、AWS DevOps Agent、Harness CI、GitHub Copilot cloud agent 与 Nx Cloud Self-Healing CI。GitHub Copilot 与 Harness 完整平台的数字单列为**公司级辅助比较**：它们说明同一公司或平台的相邻能力已有结果，不能反推为某个新智能体的直接收益。

## 口径、来源与读取方式

- **直接比较效果**：被比较的产品或能力本身在前后、A/B、对照组或同仓基准中的结果。
- **公司级辅助比较**：同一公司的 Copilot 或同一 Harness 平台组合带来的结果；不写成 Agentic Workflows、Cloud Agent 或 Worker Agents 的单独效果。
- **厂商自述**：包括厂商内部基准和厂商发布的客户案例；本笔记保留其原始口径，不评判准确性。
- **报告计算**：只对原始数字作算术换算；例如 45→12 分钟为下降约 73.3%，83%→90%为提高 7 个百分点（相对约 8.4%）。
- 除非另有说明，下列链接均于 **2026-08-08** 访问。正式可用、预览、测试版、早期试用等状态只按官方材料记录；没有声明时写“未见生命周期标签”，不自行补全。

## 一、五项重点能力的直接比较效果

| 能力 | 比较对象或基线 | 使用后 | 变化幅度 | 样本与窗口 | 发布日期 / 产品状态 | 来源类型 | 归因边界 |
|---|---|---|---|---|---|---|---|
| GitHub Agentic Workflows：令牌优化 | 优化前的同一生产工作流有效令牌（ET） | Auto-Triage -62%；Daily Compiler Quality -19%；Daily Community Attribution -37%；Security Guard -43%；Smoke Claude -59% | -19%～-62%；Auto-Triage 累计约省 780 万 ET | 12 个生产工作流中 9 个获得建议改动；仅纳入优化前后各至少 8 次运行的工作流；Auto-Triage 109 次优化后运行 | 2026-05-07（05-13 更新）；数据发布时为技术预览，2026-06-11 进入公开预览 | GitHub 内部生产前后测量 | ET 是按模型成本加权的运行成本代理，不是交付工时或正确性；见 [官方测量](https://github.blog/ai-and-ml/github-copilot/improving-token-efficiency-in-github-agentic-workflows/)（访问：2026-08-08） |
| GitHub Agentic Workflows：反例 | Contribution Check 优化前的 ET 与任务分布 | 优化后平均 ET 上升 5% | +5%；作者归因于工作负载变重 | 优化前小拉取请求 41%、大拉取请求 39%；优化后小拉取请求 9%、大拉取请求 65% | 同上；数据发布时为技术预览 | GitHub 内部前后测量 | 不能把某次 ET 上升直接归为优化失败，也不能把下降直接视为质量不变；[原文解释](https://github.blog/ai-and-ml/github-copilot/improving-token-efficiency-in-github-agentic-workflows/)（访问：2026-08-08） |
| AWS DevOps Agent：生产调查 | Rapyder 人工调查与解决平均 45 分钟 | 12 分钟；根因判断准确率 92% | 时间下降约 73.3%（报告计算） | 50 次调查；500+ AWS 账户、每月 150+ 事件是其托管范围 | 生产运维 2026-03-31 GA | AWS 客户前后比较 | 只对应事件调查与解决，不证明 Release Management 或生产放行效果；[Rapyder 客户页](https://aws.amazon.com/devops-agent/customers/)（访问：2026-08-08） |
| Harness CI Intelligence：历史同仓基准 | GitHub Actions / 另一 CI 工具，在 Kafka、RocketMQ、ZooKeeper 的同一拉取请求构建 | Harness Kafka 5 分钟、RocketMQ 3 分18秒、ZooKeeper 6 分钟 | 相对 GitHub Actions 的 19 分钟，分别约快 3.8 倍、5.8 倍、3.2 倍（报告计算）；中位数称比最近竞品快 2～5 倍 | 每个仓库 50+ 拉取请求；默认配置；Harness 组合使用缓存智能、测试智能与托管构建 | 2022-11-16；**历史能力基线**，非 2026 新智能体效果 | Harness 厂商同仓基准 | 不能拆分为测试智能、缓存或云执行任一单项的收益，也不是独立审计；[基准与方法](https://www.harness.io/blog/fastest-ci-tool)（访问：2026-08-08） |
| Harness CI Intelligence：费用模型 | 一名客户有 90 名工程师、每月构建 19,524 次、平均 15 分钟；按另一家 CI 与 GitHub Actions 的公开分钟价格估算 | Harness 年费用 14,057 美元；另一家 CI 28,115 美元；GitHub Actions 42,172 美元 | Harness 分别低约 50% 和 66.7%（报告计算） | 年构建 234,288 次；模型假设 Harness 构建速度是两个对手的 2 倍 | 2022-11-16；**历史估算模型** | Harness 厂商费用模型 | 这是按使用量、速度假设和公开价格推算的费用，不是客户实际发票；[模型与原始数值](https://www.harness.io/blog/fastest-ci-tool)（访问：2026-08-08） |
| Harness CI：测试智能 | Harness-Core 未启用测试智能的平均单元测试 75 分钟 | 启用后 25 分钟 | 少 50 分钟，约 -66.7%（报告计算） | 3,000 个拉取请求；时间窗未披露 | 页面 2026-07-02 更新；当前文档列为可用能力，部分语言/框架仍为测试版 | Harness 内部前后测量 | 仅单元测试时间，不是全流水线墙钟时间；并行步骤的“智能节省”也不等于用户等待时间；[CI Intelligence](https://developer.harness.io/3k-docs/continuous-integration/use-ci/harness-ci-intelligence/)（访问：2026-08-08） |
| GitHub Copilot cloud agent：仓库记忆 | 同一 Cloud Agent 未启用仓库记忆的拉取请求合并率 83% | 启用记忆后 90% | +7 个百分点；相对约 +8.4%（均为报告计算）；`p < 0.00001` | GitHub 对照试验；未披露样本量、窗口和任务分布 | 2026-01-15；记忆当时为公开预览、可选启用；Cloud Agent 主体已正式可用 | GitHub 第一方对照试验 | 只证明“仓库记忆”对 Cloud Agent 的增量；不是 Cloud Agent 相对人工的收益；[对照结果](https://github.blog/ai-and-ml/github-copilot/building-an-agentic-memory-system-for-github-copilot/)（访问：2026-08-08） |
| GitHub Copilot cloud agent：启动 | 优化前同一智能体的启动时间 | 官方称“开始工作”快 50% | +50% | 未披露绝对时间、样本或任务完成指标 | 2026-03-19；Copilot coding agent 当时已正式可用，后更名 Cloud Agent | GitHub 产品版本前后声明 | 只表示启动性能，不能换写为任务完成、拉取请求合并或发布快 50%；[更新公告](https://github.blog/changelog/2026-03-19-copilot-coding-agent-now-starts-work-50-faster/)（访问：2026-08-08） |
| Nx Cloud Self-Healing CI | 失败拉取请求的人工处理 / 平台组合的既有状态 | 约三分之二失败拉取请求获得“有效修复”；大型单一代码库约 60% 修复被接受 | 无公开的统一前后基线；是接受/有效比例，不是节省比例 | 代码库数、绝对拉取请求数、窗口与“有效”定义未披露 | 2025-06-23 早期试用；未检索到单独正式可用公告 | Nx 厂商汇总 | “有效修复”与“接受”分母不同，均不等于完整必需检查通过；[2025 总结](https://nx.dev/blog/wrapping-up-2025)（访问：2026-08-08） |

### 1. GitHub Agentic Workflows：直接效果与采用规模分开记录

- **能力与状态：** GitHub Agentic Workflows（`gh aw`）于 2026-06-11 进入**公开预览**；本笔记不把它写为正式可用。[官方公告](https://github.blog/changelog/2026-06-11-github-agentic-workflows-is-now-in-public-preview/)（访问：2026-08-08）。
- **直接比较：** GitHub 在 `gh-aw`、`gh-aw-firewall` 的生产工作流上部署审计与优化器。ET 对不同模型、输入、缓存和输出按成本权重归一；因此“ET 降 10%”按作者定义表示成本代理降 10%，不是原始 token 数的简单相减。[方法与结果](https://github.blog/ai-and-ml/github-copilot/improving-token-efficiency-in-github-agentic-workflows/)（访问：2026-08-08）。
- **采用规模（非收益）：** Aspire 场景在 2026-05-03 至 06-02 的 30 天中运行 396 次，产出 82 个文档草稿拉取请求；82 个均合并，中位合并时间 44.8 小时。CI Doctor 的 13 个修复拉取请求中有 9 个合并。这些说明工作流已持续使用，但没有人工基线，不能当作“提速”。[Aspire 文章](https://github.blog/ai-and-ml/github-copilot/automating-cross-repo-documentation-with-github-agentic-workflows/)、[CI Doctor 文章](https://github.github.com/gh-aw/blog/2026-01-13-meet-the-workflows-quality-hygiene/)（访问：2026-08-08）。
- **边界：** GitHub 明确说明，稳定的轮次、工具完成率等只是过程信号，没有输出正确性的可靠标准。故上述 -19%～-62% 不支持“质量不变”“缺陷下降”或“交付更快”的结论。

### 2. AWS DevOps Agent：保留的直接效果

- **状态：** 生产运维能力于 2026-03-31 **正式可用**；发布管理于 2026-06-17 在 `us-east-1` 进入**预览**。两类效果不可混算。[正式可用公告](https://aws.amazon.com/about-aws/whats-new/2026/03/aws-devops-agent-generally-available/)、[预览公告](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-release-management/)（访问：2026-08-08）。
- **补充客户结果：** Audible 报告事件解决中位数 8.5 分钟、81% 情况快于人工、每起节省超过 100 分钟、根因准确率 84%；样本未披露。Deriv 称发布评审从数小时、偶尔一整天缩至数分钟；样本、成功率与回滚率未披露。[AWS 客户页](https://aws.amazon.com/devops-agent/customers/)（访问：2026-08-08）。
- **边界：** Rapyder、Audible 是事件调查；Deriv 是预览阶段的发布评审。它们均不能证明智能体自动完成恢复或提升生产放行成功率。

### 3. Harness CI Intelligence：直接效果不只写单元测试

- **能力层级与状态：** CI Intelligence 是 Harness CI 中的缓存、测试选择/拆分和构建执行能力集合；TI 只是其中一项。2022 基准写作“历史能力基线”，2026 页面只记录当前文档支持状态，不把新旧能力混为一个生命周期。
- **同仓基准：** Kafka、RocketMQ、ZooKeeper 各 50 多次拉取请求构建，GitHub Actions 三者均为 19 分钟；Harness 平均分别为 5 分钟、3 分18秒、6 分钟。Kafka 中，Harness 称测试选择把约 16,000 项测试缩至约 700 项并并行执行。该结果是缓存智能、测试智能和托管构建的组合。[原始方法](https://www.harness.io/blog/fastest-ci-tool)（访问：2026-08-08）。
- **费用模型：** 同一材料按一名客户 90 名工程师、每月 19,524 次构建、每次平均 15 分钟估算，Harness 年费用为 14,057 美元，另一家 CI 为 28,115 美元，GitHub Actions 为 42,172 美元；按公开值计算分别低约 50% 和 66.7%（报告计算）。该数字依赖“Harness 快 2 倍”和公开分钟价格假设，不是客户实际发票。[模型与原始数值](https://www.harness.io/blog/fastest-ci-tool)（访问：2026-08-08）。
- **客户前后比较：** Qrvey 的 Jenkins 构建原为 90 分钟至数小时，迁移至 Harness CI 后平均 12 分钟；同时从 8 个线程改为 30+ 云端线程，迁移耗时 3 个月。因此“8 倍”是 CI、云执行和基础设施替换的合成效果。[Qrvey 案例](https://www.harness.io/case-studies/qrvey-saves-hours-on-build-time-leading-to-happy-engineers)（访问：2026-08-08）。
- **边界：** 同仓基准没有公布独立第三方复测；客户案例没有受控分组。两者能说明构建等待变化，不能证明缺陷逃逸率、代码质量或 Worker Agents 的效果。

### 4. GitHub Copilot cloud agent：局部增量，不是公司级 Copilot 总收益

- **状态：** Copilot coding agent 于 2025-09-25 **正式可用**；2026-04-01 更名为 Copilot cloud agent。仓库记忆在其 2026-01-15 文章中为**公开预览**、默认关闭且需选择启用。[正式可用公告](https://github.blog/changelog/2025-09-25-copilot-coding-agent-is-now-generally-available/)、[更名公告](https://github.blog/changelog/2026-04-01-research-plan-and-code-with-copilot-cloud-agent/)、[记忆文章](https://github.blog/ai-and-ml/github-copilot/building-an-agentic-memory-system-for-github-copilot/)（访问：2026-08-08）。
- **第三方观察（非比较效果）：** AIDev-pop 研究观察到 100 星以上公开仓库的 4,970 个 Copilot 拉取请求中有 2,139 个合并（43.04%）；CI、文档、功能、测试、性能任务合并率分别为 63%、61%、38%、37%、27%。它没有人工对照，不能和记忆对照试验的 90% 比较。[原始论文](https://arxiv.org/abs/2601.15195)（访问：2026-08-08）。
- **边界：** Cloud Agent 的拉取请求仍在其云端环境中运行测试并提交；合并、必需检查和生产发布仍由仓库规则与人控制。启动提速也不能替代端到端交付指标。[当前文档](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-cloud-agent)（访问：2026-08-08）。

### 5. Nx Cloud Self-Healing CI：保留结果与缺口

- **状态：** 2025-06-23 进入**早期试用**；当前有正式文档，但未检索到单独、明确的正式可用公告。[首次发布](https://nx.dev/blog/nx-self-healing-ci)、[当前文档](https://nx.dev/docs/features/ci-features/self-healing-ci)（访问：2026-08-08）。
- **平台组合结果：** Nx 另称自动处理不稳定任务与 Self-Healing 等能力可使 CI 失败最多减少 40%；结合缓存、分布式执行和任务拆分后，大型项目进入绿色状态时间降低 20%～50%。这是平台组合，不能写成 Self-Healing 单项效果。[原文](https://nx.dev/blog/pr-review-is-the-bottleneck)（访问：2026-08-08）。
- **写回边界：** 只有白名单、高置信和原失败任务重跑验证等条件满足时，建议才可自动写入拉取请求分支；受保护分支不生成修复。原任务重跑通过也不等于完整必需检查、审查或发布批准。[当前文档](https://nx.dev/docs/features/ci-features/self-healing-ci)（访问：2026-08-08）。

## 二、公司级辅助比较效果

### GitHub：公司级 Copilot 的对照结果

> [!warning]
> 下表是 GitHub Copilot 的公司级编码辅助证据，不是 Agentic Workflows 或 Copilot cloud agent 的直接效果。其价值在于补足 GitHub 产品体系的“提高/降低”对照数据。

| 能力层级 | 基线或对照 | 使用后 | 变化幅度 | 样本与窗口 | 发布日期 / 产品状态 | 来源类型与边界 |
|---|---|---|---|---|---|---|
| Copilot 早期代码补全 | 无 Copilot 组：任务完成率 70%，平均 2 小时41分 | Copilot 组：78%，平均 1 小时11分 | 完成率 +8 个百分点（报告计算）；官方报告时间快 55% | 95 名专业开发者随机分两组，同一 JavaScript HTTP 服务器任务 | 2022-09-07 发布、2024-05-21 更新；历史研究，文章未为该实验单列产品状态 | GitHub 随机对照；只针对单一受控任务，不代表 Cloud Agent 或真实发布流程。[原始研究](https://github.blog/news-insights/research/research-quantifying-github-copilots-impact-on-developer-productivity-and-happiness/)（访问：2026-08-08） |
| Copilot 代码质量 | 无 AI 工具组 | Copilot 组通过全部 10 个单测的可能性更高；盲审代码错误更少 | 通过全部测试的可能性 +53.2%；每个可读性错误对应代码行数 +13.6%；可读性/可靠性/可维护性/简洁性 +3.62%/+2.94%/+2.47%/+4.16%；批准可能性 +5% | 202 名、至少五年经验开发者，随机分配；同一 Web API 任务 | 2024-11-18 发布、2025-02-06 更新；公司级 Copilot 研究 | GitHub 随机对照；“可能性”不是绝对通过率，不能外推为生产缺陷率。[原始研究](https://github.blog/news-insights/research/does-github-copilot-improve-code-quality-heres-what-the-data-says/)（访问：2026-08-08） |
| Copilot Business（Accenture） | 200 名未获 Copilot 的对照组 | 450 名 Copilot 使用组的研发运维度量数据 | 拉取请求数量 +8.69%；拉取请求合并率 +15%；成功构建 +84% | 450 名使用者、200 名对照者；企业随机对照试验，文章未披露观察窗 | 2024-05-13；Copilot Business 公司级产品 | GitHub/Accenture 随机对照试验；指标是公司级 Copilot 使用结果，不能归给任何智能体；成功构建也不是生产成功率。[研究](https://github.blog/news-insights/research/research-quantifying-github-copilots-impact-in-the-enterprise-with-accenture/)、[样本说明](https://github.com/customer-stories/accenture)（访问：2026-08-08） |

### Harness：AI 编排与完整交付平台的辅助结果

> [!warning]
> 下表覆盖 Harness AI、AI Test Automation、CI/CD 模板、云执行与治理等不同产品。它们只支持“完整交付平台可出现何种收益”的观察，不支持把所有数字归因于 Harness CI Intelligence 或 2026 Worker Agents。

| 层级 / 对象 | 基线或对照 | 使用后 | 变化幅度 | 样本与窗口 | 发布日期 / 产品状态 | 来源类型与边界 |
|---|---|---|---|---|---|---|
| Harness AI：内部统一智能体 | 初代 20 多个子智能体，响应超过 40 秒、准确性不一致 | 统一智能体 + 知识图谱 + 工具目录，响应约 20 秒 | 响应约快 2 倍；令牌消耗降低 90%；准确性只称“更高” | Harness 内部；未披露请求数、评测集或窗口 | 2026-04-06；工程文章未声明正式可用或预览状态 | Harness 内部架构复盘；不是客户生产对照，也没有准确率绝对值。[原文](https://www.harness.io/blog/building-enterprise-ai)（访问：2026-08-08） |
| Harness AI：平台支持 | 需要提交平台支持工单 | 官方称某大型金融机构通过 Harness AI 分流 95% 平台支持工单 | 95% 分流 | 客户匿名；只称“随后数月”、未披露工单基数和对照期 | 2026-03-25；文章未声明生命周期 | Harness 客户案例；不代表 CI/CD 故障率、Worker Agents 修复率或所有客户效果。[原文](https://www.harness.io/blog/harness-ai-helps-scale-platform-wide-support)（访问：2026-08-08） |
| Harness AI Test Automation：Wasimil | Playwright：约 50% 测试失败、维护约 2 小时/日、每周发布两次 | Harness AI：失败率 <10%、维护 45～60 分钟/日、每日发布 | 失败率至少少 40 个百分点、维护约少 50%～62.5%（均为报告计算）；发布从每周 2 次到约每周 5 次 | 单一酒店平台客户；窗口未披露 | 案例页未标发布日期或生命周期；当前页标产品为 AI Test Automation | Harness 客户前后案例；“生产 bug 下降”没有绝对值，不能量化或外推。[案例](https://www.harness.io/case-studies/wasimil)（访问：2026-08-08） |
| 完整平台：United | 原 CI 流水线构建近 22 分钟；新流水线由初级研发运维工程师创建需数天至数周 | 同一代码构建低于 5 分钟；10 条流水线约 50 秒生成 | 客户页将整体概括为部署时间快 75%；流水线生成由数天或数周降至 50 秒 | 单一客户；CI、CD、模板、并行执行、治理和云迁移共同变化；窗口未披露 | 案例页未标发布日期或生命周期；使用 CI 与 CD/GitOps | Harness 客户前后案例；不能归因给智能体或单个人工智能能力。[案例](https://www.harness.io/case-studies/united-airlines-accelerates-deployments-harness)（访问：2026-08-08） |
| 完整平台：The Warehouse Group | 变更交付 120 小时；管道接入约 6 周 | 变更交付 1 小时；新服务约 30 分钟有构建部署到开发/测试环境 | 交付时间约 -99.2%（报告计算）；接入时间从 6 周到约 30 分钟 | 单一客户；CD、ServiceNow、Backstage、模板和 Golden Path 共同作用 | 案例页未标发布日期或生命周期；使用 CD/GitOps | Harness 客户前后案例；不是 AI 或 Worker Agent 效果。[案例](https://www.harness.io/case-studies/warehouse-group-decreases-lead-time-changes-99-percent)（访问：2026-08-08） |

### Worker Agents 的直接效果缺口

- Harness 已发布 [Autonomous Worker Agents](https://www.harness.io/blog/introducing-autonomous-worker-agents) 的介绍材料（访问：2026-08-08），但本次核验**未找到**公开的首次修复成功率、人工接管率、平均恢复时间、拉取请求接受率、缺陷逃逸率、生产发布结果或可比成本数据。
- 因此，不能以 Harness CI 基准、Harness AI 支持分流或客户 CI/CD 迁移收益替代 Worker Agents 的直接结果；该缺口应保留在主报告中。

## 三、证据边界与可成立的横向结论

1. **比较性收益已覆盖多个环节，但单位不同。** GitHub 有编码任务的随机对照和 Agentic Workflows 的成本前后比较；Harness 有构建、测试维护、平台支持和交付时延的前后比较；AWS 有事件调查时长；Nx 有接受/有效比例。它们不能汇成一个跨厂商投资回报排名。
2. **最清楚的直接收益在确定性、可复验的工作中。** Agentic Workflows 降低了可观测的智能体运行成本；Harness CI 降低构建或测试等待；AWS 缩短调查；Nx 通过重跑原失败任务约束写回。生产放行、长期质量与自动恢复仍缺同口径对照。
3. **平台收益不能替代新智能体证据。** GitHub 公司级 Copilot 实验不证明 Cloud Agent 或 Agentic Workflows 的端到端效果；Harness 完整平台客户案例不证明测试智能、Harness AI 或 Worker Agents 任一单项的效果。
4. **建议企业按层建立自身对照。** 同一类任务至少同时记录触发量、接受/合并率、完整门禁通过率、触发到合并或恢复的中位时长，以及返工、回滚或缺陷逃逸。只有同一组织、同一任务类型、同一门禁条件下的前后或对照数据，才适合用于本组织投资判断。

## 来源清单

- GitHub Agentic Workflows：[公开预览](https://github.blog/changelog/2026-06-11-github-agentic-workflows-is-now-in-public-preview/)、[令牌效率测量](https://github.blog/ai-and-ml/github-copilot/improving-token-efficiency-in-github-agentic-workflows/)、[Aspire 实践](https://github.blog/ai-and-ml/github-copilot/automating-cross-repo-documentation-with-github-agentic-workflows/)、[CI Doctor](https://github.github.com/gh-aw/blog/2026-01-13-meet-the-workflows-quality-hygiene/)。
- GitHub Copilot：[95 人随机试验](https://github.blog/news-insights/research/research-quantifying-github-copilots-impact-on-developer-productivity-and-happiness/)、[202 人随机试验](https://github.blog/news-insights/research/does-github-copilot-improve-code-quality-heres-what-the-data-says/)、[Accenture RCT](https://github.blog/news-insights/research/research-quantifying-github-copilots-impact-in-the-enterprise-with-accenture/)、[Cloud Agent 记忆 A/B](https://github.blog/ai-and-ml/github-copilot/building-an-agentic-memory-system-for-github-copilot/)、[启动提速](https://github.blog/changelog/2026-03-19-copilot-coding-agent-now-starts-work-50-faster/)、[Cloud Agent 文档](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-cloud-agent)。
- AWS：[DevOps Agent GA](https://aws.amazon.com/about-aws/whats-new/2026/03/aws-devops-agent-generally-available/)、[Release Management Preview](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-release-management/)、[客户案例](https://aws.amazon.com/devops-agent/customers/)。
- Harness：[历史 CI 基准](https://www.harness.io/blog/fastest-ci-tool)、[CI Intelligence](https://developer.harness.io/3k-docs/continuous-integration/use-ci/harness-ci-intelligence/)、[企业 AI 架构](https://www.harness.io/blog/building-enterprise-ai)、[平台支持分流](https://www.harness.io/blog/harness-ai-helps-scale-platform-wide-support)、[Qrvey](https://www.harness.io/case-studies/qrvey-saves-hours-on-build-time-leading-to-happy-engineers)、[United](https://www.harness.io/case-studies/united-airlines-accelerates-deployments-harness)、[Warehouse Group](https://www.harness.io/case-studies/warehouse-group-decreases-lead-time-changes-99-percent)、[Wasimil](https://www.harness.io/case-studies/wasimil)。
- Nx：[首次发布](https://nx.dev/blog/nx-self-healing-ci)、[当前文档](https://nx.dev/docs/features/ci-features/self-healing-ci)、[2025 总结](https://nx.dev/blog/wrapping-up-2025)、[验证阶段文章](https://nx.dev/blog/pr-review-is-the-bottleneck)。
