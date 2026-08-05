---
title: "Dagger 洞察页的替代平台候选：验证计划与远程执行的分层选择（2026-08-03）"
tags:
  - research/cicd
  - company/dagger
  - comparison/platform-selection
status: complete
as_of: 2026-08-03
confidence: medium-high
---

# Dagger 洞察页的替代平台候选：验证计划与远程执行的分层选择（2026-08-03）

> [!warning] 研究输入，不是正式页面
>
> 本文用于为 Dagger 选择比 Buildkite 更具技术辨识度的同页搭档，**不**构成新搭档的 Deep Dive，也不使任何 Presentation `presentation_ready`。链接均于 **2026-08-03** 访问；没有发布日期或正式阶段标记的资料，不能倒推其 GA。所有厂商客户案例和量化结果都保留为厂商自述，不能外推。

## 写作提纲

1. 固定 Dagger 的已核验层次，避免把候选的相邻功能误写成替代；
2. 对 Bazel/EngFlow、Gradle Develocity、Depot、BuildBuddy、Nix 逐项核验能力、状态和采用证据；
3. 用既有 GitHub Agentic Workflows、Harness CI、CI 自愈、基础设施、CLI/MCP 页面做差异检查；
4. 给出首选、第二选择、页面主张及不能说的话。

## 结论先行

**首选：Gradle Develocity（只取 Predictive Test Selection，PTS）。**

可成立的一页主张是：**“当交付动作已能被编程为可组合执行图，验证计划也不应再是一张固定清单：Dagger 先避免对相同输入重复计算；Develocity 再以变更—测试历史选择本轮最有信息量的测试，并将保留的长测按历史时长均衡分发到多个 agent。”**

这比 Dagger + Buildkite 更具技术辨识度，原因不在于“又一个更快的 CI”，而在于它把三道不同的问题顺序并排：Dagger 是**哪些相同输入的交付动作无需重算**；PTS 是**当前变更先应获取哪一组验证证据**；Test Distribution 是**剩余长测如何按历史时长分区并并行执行**。Develocity 的官方手册同时明确 PTS 是概率性取舍，建议在 post-merge、nightly 或 release 前执行 remaining/all tests；这使页面可以自然落到“快反馈不等于放弃完整验证”的控制边界，而不会把它讲成 CI 自愈或 Agent 自动修复。

**第二选择：EngFlow（与 Bazel/REAPI 一起理解，但页面主角是 EngFlow Remote Execution）。**

可成立的一页主张是：**“可编程交付不能只生成 DAG；当单次验证已经是数十万 action 时，平台还要把每个声明输入的 action 送到可隔离、可缓存、可按资源匹配的远程执行网格。”** Dagger 表达函数/容器执行语义，EngFlow 将 build-system 已生成的 action 分发给 REAPI worker；两者可同页但相邻，不可写成相同的 Build Graph。

**不建议 Depot、BuildBuddy 或 Nix 作为本轮 Dagger 的同页搭档。** 它们各自有真实能力，但分别更像既有基础设施页的 runner/cache 叙事、EngFlow 的较窄 Bazel 版本，或与 Dagger 缓存/可复现性距离过近却缺少本轮所需的企业采用证据。尤其 Depot 的现有 Dagger Engine 集成页面已经标为 deprecated，不能把它包装成值得主推的长期组合。

## 共同基线：Dagger 和既有页面的边界

| 对象 | 已核验的中心抽象 | 在本页中应保留的边界 |
|---|---|---|
| Dagger | Function 接受、返回类型化值并可组合；Engine 将 API 请求转成低层执行 DAG，并以缓存优化。 [Functions](https://docs.dagger.io/core-concepts/functions/) [API Internals](https://docs.dagger.io/reference/api/internals/) | 不是通用 CI 控制面；函数缓存的 key 包含 module source、arguments 与 parent object，且默认 TTL 当前是 7 天，故外部可变状态必须被显式处理。 [Function Caching](https://docs.dagger.io/extending/function-caching/) |
| GitHub Agentic Workflows | Markdown 指令 + frontmatter 编译成 hardened `.lock.yml`，作为 GitHub Actions 中的 AI coding-agent 自动化运行；当前为 **public preview**。 [GitHub Docs](https://docs.github.com/en/enterprise-cloud%40latest/copilot/concepts/agents/about-github-agentic-workflows) | 是 repository-agent 任务定义/受限写操作层，不是 PTS 的验证选择模型，也不是 REAPI action 级远程执行。 |
| Harness CI | Pipeline 由 stage/step 构成，Delegate 在客户环境连通 build infrastructure 并向 SaaS Manager 回传数据；页面最后更新为 2025-09-11。 [Harness CI overview](https://developer.harness.io/docs/continuous-integration/get-started/overview) | 是更宽的 CI 平台/控制面；不是本页选择 Develocity 或 EngFlow 的核心技术主题。 |
| 已有“CI 自愈”页 | 该页讨论失败后的诊断、修复提议、授权和复验闭环。 | PTS 只选择要运行的测试，**不**生成修复、批准变更或证明发布安全；REAPI 只调度 action，**不**诊断或修复失败。 |
| 已有“基础设施”页与 CLI/MCP 页 | 前者关注 runner/运行环境、策略和基础设施控制；后者关注人/Agent 的调用界面及工具暴露。 | 本页不讲 runner SKU、自然语言调用或 MCP server。Develocity 虽有 Analytics MCP 文档，若选它也应刻意不使用该能力，否则会与 CLI/MCP 页重叠。 [Develocity MCP](https://docs.gradle.com/develocity/current/integrations/drv-mcp/) |

## 候选比较

| 候选 | 一句话能讲清的突出能力 | 与 Dagger 的同页关系 | 一手资料与采用证据 | 产品状态（本轮能确认） | 结论 |
|---|---|---|---|---|---|
| **Gradle Develocity / PTS + Test Distribution** | **让历史变更—测试结果决定先跑哪些测试，再把余下长测按预期时长均衡分发到多个 agent。** | Dagger 减少“重复动作”；PTS 减少“无关测试”；Distribution 缩短“剩余测试的墙钟时间”。三个层次相邻但不等同。 | 高：当前官方 PTS 手册给出模型、profiles、must-run、relevant/remaining 流程、兼容性和遗漏调查；2026.2 Test Distribution 文档说明多 agent、agent pool、能力匹配与自动伸缩；Pinterest Engineering 证实其团队已连续约一年把 Develocity 数据导出至 Honeycomb。 [PTS manual](https://docs.gradle.com/develocity/predictive-test-selection) [Test Distribution 2026.2](https://docs.gradle.com/develocity/2026.2/administration/build-acceleration/test-distribution/) [Pinterest Engineering](https://medium.com/pinterest-engineering/understanding-develocity-build-data-with-honeycomb-78bc655f654f) | Develocity 2026.2 于 2026-06-30 发布；PTS/Distribution 为当前支持版本的文档化能力，页面未以 GA/Preview 逐项标注，不能擅称 GA。 [Release notes](https://develocity.ai/releases/) [Compatibility](https://docs.gradle.com/develocity/compatibility/) | **首选** |
| **EngFlow / Bazel/REAPI** | **把 action graph 分发到按 OS/CPU/RAM/资源属性匹配且隔离的远程执行网格，结果进入 action cache/CAS。** | Dagger Function DAG 与 EngFlow 的 build-system action graph 是两个粒度；可画成“函数内表达 → action 级并发执行”，但不能宣称原生集成。 | 高：官方文档说明 REAPI v2.0.0、scheduler/worker/CAS/隔离；厂商案例列 Brave、BMW、Wix、Selenium 等。Brave 案例为厂商自述，称从约 2 小时到 15 分钟。 [RE service](https://docs.engflow.com/re/index.html) [What is RE](https://docs.engflow.com/re/what-is-re.html) [Case studies](https://www.engflow.com/caseStudies) [Brave case](https://www.engflow.com/caseStudies/brave) | Remote Execution 为当前文档化产品；CMake RE 在 overview 中明示 **beta**，不应作为本页中心。 [Overview](https://docs.engflow.com/overview.html) | **第二选择** |
| **BuildBuddy** | 基于 Bazel RBE/cache，并以按 Git branch 继承的 Firecracker VM snapshot 复用 warmed Bazel workspace。 | 技术上比一般 runner 有辨识度，但仍是 Bazel 远程执行/缓存故事，与 EngFlow 同层，且和 Dagger 的 “graph + cache” 容易在视觉上挤在一起。 | 中：官方文档详述 RBE、三层缓存、action dedup 与 snapshot；官网列 Spotify、Cisco、Asana 等 logo，但本轮未找到采用方独立工程文章。其 CDC 性能数值是 BuildBuddy 自身 repo 的测量。 [RBE](https://www.buildbuddy.io/docs/remote-build-execution/) [Remote runners](https://www.buildbuddy.io/docs/remote-runner-introduction/) [CDC blog](https://www.buildbuddy.io/blog/remote-cache-cdc) | RBE current documentation；文档未给 GA/Preview 标记。 | 不选：若需要 RE，请选证据与跨 build tool 边界更强的 EngFlow。 |
| **Depot** | 将 Docker build / GitHub Actions runner 放进高带宽、单租户的临时计算与 cache orchestration。 | 与 Dagger 容器/缓存能力能相邻，但主叙事更像“把既有 CI runner 和 Docker build 跑更快”，与基础设施页重复更大。 | 中：官方文档明确 GitHub runner 的 ephemeral single-tenant EC2、同网段 BuildKit 和 repository-scope cache；其 up-to 数字均属厂商自述。 [GitHub runners](https://depot.dev/docs/github-actions/overview) [CI container builds](https://depot.dev/docs/container-builds/how-to-guides/continuous-integration) | 当前文档化服务，未在资料中标 GA/Preview。**Dagger Engine support 页面标为 deprecated 且称将来移除。** [Dagger integration](https://depot.dev/docs/github-actions/integrations/dagger) | 不选：直接组合路径已弃用，且 runner/cache 表达不够独特。 |
| **Nix** | 用 derivation、锁定输入与 pure evaluation 将“构建输入是什么”写成可计算的声明。 | 可与 Dagger 形成“动作 API vs 输入/provenance”对照，且能补足环境可复现性；但与 Dagger 的 content/cache 思路过近，稍不严谨就会混淆“可复现环境、可缓存执行、字节级可复现制品”。 | 中低：Nix 官方资料充分说明 lock、pure evaluation 和可复现构建的条件；本轮找到的企业采用证据不足以支撑面向 CTO 的成熟商业平台搭档。 [Flakes](https://nix.dev/concepts/flakes.html) [Pure evaluation](https://releases.nixos.org/nix/nix-2.32.3/manual/command-ref/conf-file.html) [NixOS reproducibility](https://reproducible.nixos.org/) | `flake` 在官方概念文档仍标 **experimental**；NixOS reproducibility 页面明确 Nix 是良好起点但非充分条件。 | 不选：值得单独做“可复现输入/供应链”专题，不宜替代本页第二公司。 |

## 首选详解：Dagger + Develocity（PTS）

### 可用的页面主张

> **CI 的执行计划不再只有“跑哪些步骤”；它还需要依次决定“哪些动作无需重算、此变更先获取哪些验证证据、余下长测如何并行”。Dagger 将可复用交付动作定义为类型化函数并生成执行 DAG；Develocity PTS 选择当前最相关的测试，Test Distribution 再将合适的剩余测试分区到多个 agent。**

### 逐条事实与对应关系

1. **Dagger 侧（事实）：** Functions 是接受类型化 inputs、通常在 container 中操作、返回类型化 outputs 的计算单元；Dagger 明确说它们可以组合成复杂 workflow。Engine API 请求被转换为低层 operation DAG。 [Functions](https://docs.dagger.io/core-concepts/functions/) [API Internals](https://docs.dagger.io/reference/api/internals/)
2. **Develocity PTS（事实）：** PTS 在测试前请求服务预测哪些测试会对当前 changeset 提供有用反馈；模型使用 code change、测试结果、项目结构和历史，并有 Conservative / Standard / Fast 三种 profile。 [PTS: how it works and profiles](https://docs.gradle.com/develocity/predictive-test-selection)
3. **Develocity Distribution（事实）：** Test Distribution 需要连接额外 agent，按任务要求匹配 agent capabilities；平台把测试分布到多计算资源。其当前文档还说明 agent pool 可由外部计算平台 autoscale，但实际数量仍受底层计算平台限制。 [Test Distribution 2026.2](https://docs.gradle.com/develocity/2026.2/administration/build-acceleration/test-distribution/)
4. **闭环边界（事实）：** PTS 文档明确将其定义为“testing comprehensiveness for faster feedback”的取舍，并建议在 post-merge、nightly 或 ready-for-release 阶段运行 remaining tests 或不开 PTS 的完整测试；遗漏的失败可能在全量测试中被发现。 [PTS: when to use and missed failures](https://docs.gradle.com/develocity/predictive-test-selection)
5. **成熟采用（事实）：** Pinterest Engineering 在 2024-07-15 的文章中称其 Mobile Builds 团队已使用 Develocity API 向 Honeycomb 发送 build data 约一年；这是采用方的一手工程文章，但只证明数据导出/观测使用，**不能**证明其采用 PTS、Distribution 或任何收益。 [Pinterest Engineering](https://medium.com/pinterest-engineering/understanding-develocity-build-data-with-honeycomb-78bc655f654f)
6. **厂商自述（仅作辅助）：** Develocity 客户页列 Netflix、Tableau、Revolut、Microsoft 等故事；这说明可进一步深挖的来源入口，不能直接当作 PTS/Distribution 在这些公司的采用或效果证据。 [Customer stories](https://develocity.ai/customers/)

### 为什么它明显不同于现有五类页面

| 已有主题 | 不重复点 |
|---|---|
| GitHub Agentic Workflows | GitHub 讨论谁依照自然语言、在什么 repository 权限下行动；PTS 不做任务推理或写操作，它在已有测试集内选择验证采样。 |
| Harness CI | Harness 是 pipeline/stage/step 的控制面；PTS 是嵌入 build/test feedback 回路的选择模型，可运行在本地与 CI 的 build 数据上。 |
| CI 自愈 | 自愈是失败后诊断、提出/执行修复和复验；PTS 发生在测试前，且不改变代码。 |
| 基础设施 | 本页不销售更多 runner 或更大的集群，而是利用已有变化/测试历史减少最早阶段的验证集合。 |
| CLI/MCP | 本页不把 Develocity Analytics MCP 作为卖点，避免从“验证计划”滑向“Agent 查询工具”。 |

### 必须画出的控制边界

- **PTS 不等于正确性 oracle。** 它可能遗漏本应失败的测试；必须保留 full/remaining suite、质量门禁、Security scan、artifact/signature 与人工审批各自的独立地位。
- **速度—覆盖率是被显式配置的取舍。** Fast profile 倾向少选测试，Conservative 倾向多选；不得把默认 Standard 或厂商预测模型写成“零漏测”。
- **Distribution 也不是免费/无条件并行。** 它要有接入的 agent、认证和资源能力匹配；远端传输 inputs/outputs 有成本，当前官方手册建议不要把仅需数秒的 test task 也分发。 [Test Distribution user manual](https://docs.develocity.ai/test-distribution/)
- **适用性受限。** 官方文档列出的 PTS/Distribution 适用条件是 JUnit Platform 与 JVM 语言/测试框架，Gradle 5.4+ 或 Maven Surefire/Failsafe 等；不能外推为 polyglot 或任意 Dagger module 的通用测试选择器。 [PTS compatibility](https://docs.gradle.com/develocity/predictive-test-selection) [Distribution compatibility](https://docs.develocity.ai/test-distribution/)
- **组合关系只是架构推断。** Dagger 与 Develocity 的公开材料分别证明各自机制，尚未发现一手证据证明二者有联合产品、联合客户或可相加的性能收益。

### 建议的页面结构（尚未授权进入 PPTX）

```text
输入：变更集 + 受锁定的工具/依赖 + 历史验证数据
                 │
        ┌────────┴────────┐
        │                 │
Dagger：重复动作避免  Develocity：验证选择与分发
类型化 Function        相关测试 / remaining tests
容器化依赖             profiles + must-run
缓存/复用              分区 + agent capabilities
        │                 │
        └────────┬────────┘
                 ↓
早期快速反馈 ──→ 后续全量验证 / 独立门禁
```

这张图的主张是“双计划”，不是“Dagger 调度 Develocity”，也不是“PTS 证明 Dagger 产物安全”。

## 第二选择详解：Dagger + EngFlow

### 可用的页面主张

> **“可编程交付的下一道门槛不是再写一个 YAML 模板，而是让声明完整输入的 action 能被可靠地拆开、隔离并投递到匹配资源：Dagger 负责把交付意图表达为函数图，EngFlow 负责把 build-system 的 action 图转成可弹性执行的计算网格。”**

### 逐条事实与对应关系

1. **EngFlow（事实）：** Remote Execution Service 实现 open-source Remote Execution API v2.0.0；scheduler 协调 worker，worker 运行 action 并维护磁盘 CAS，action 按 OS、CPU、RAM 和额外软硬件属性匹配 executor。 [RE service](https://docs.engflow.com/re/index.html)
2. **隔离与 cache（事实）：** EngFlow 的 action environment 文档说明 isolation 用于避免 actions 互相干扰、non-deterministic outputs 与 cache poisoning；其 CAS 可使用 worker disks、复制存储或外部存储。 [Action environment](https://docs.engflow.com/re/config/action-env.html) [CAS](https://docs.engflow.com/re/config/cas.html)
3. **成熟采用（厂商自述）：** EngFlow 公开案例称 Brave 将 Chromium builds/tests 从约 2 小时降至 15 分钟，并覆盖 Android/Linux/macOS/Windows；这证明有具名大规模采用线索，**不**成为跨厂商性能基准。 [Brave case](https://www.engflow.com/caseStudies/brave)
4. **重要反例/约束（事实）：** Remote persistent workers 可能引入 non-determinism、non-hermeticity，官方建议 release builds 关闭它并隔离 cache silo；这比无边界地宣传“远程 warm worker”更适合平台受众。 [Remote persistent workers](https://docs.engflow.com/re/client/remote-persistent-workers.html)

### 为什么只是第二选择

- 它在“单个 build 内的 action 级远程执行”上非常有辨识度，也有较强案例密度；但与 Dagger 都会谈 DAG、缓存、容器/隔离，叙事容易变成复杂的 build-system 基础设施课。
- 它与既有“基础设施”页的邻接性高于 Develocity；必须把核心放在 **action graph 的可隔离执行语义**，而非 runner 数量或 cost claim。
- Build system 必须先产生足够 hermetic 的 actions。Dagger 的 Functions 不自动成为 REAPI actions；没有集成证据时，不能画成直接调用链。

## 不选候选的精确理由

### BuildBuddy：机制有趣，但本轮不胜过 EngFlow

BuildBuddy 的 remote runners 对 Linux 使用 Firecracker VM snapshots，并按 Git branch、base branch、default branch 尝试复用 warmed Bazel workspace；其 RBE 支持 custom Docker image、three-tier cache、action dedup/merging、auto-scaling。 [Remote runners](https://www.buildbuddy.io/docs/remote-runner-introduction/) [RBE](https://www.buildbuddy.io/docs/remote-build-execution/)

不选不是说能力不足，而是：

- 仍是 Bazel-first 的 RBE/cache；若这就是要讲的层次，EngFlow 对 REAPI、异构客户端和 action isolation 的一手解释更完整；
- 官方网站的客户 logo 和自测指标不是采用方独立工程证据；
- 用 Git branch snapshot 讲故事会与 Dagger cache 混淆：前者保留 warm workspace/VM state，后者以 module/function inputs 处理结果缓存，不能合并为同一正确性保证。

### Depot：与 Dagger 的现成路径反而给出淘汰信号

Depot 将 GitHub Actions job 放在 webhook 驱动的临时单租户 EC2，结束即终止；其 runner 可和远程 BuildKit builder 位于同一私网，并使用 repository-scope cache。 [GitHub runners](https://depot.dev/docs/github-actions/overview)

但官方 Dagger 页面明确写着 Dagger Engine support **deprecated and will be removed in a future update**。 [Dagger integration](https://depot.dev/docs/github-actions/integrations/dagger) 因此不得将 “Dagger on Depot” 画作长期推荐架构。即使忽略该弃用状态，故事也主要是更快 Docker build/runner/cache，和基础设施页重叠过大。

### Nix：适合另立“可复现输入”专题，而不是这页的公司搭档

Nix flake 的 `flake.lock` 锁定输入依赖图，且 Flakes 默认 pure mode；但官方概念页仍把 flakes 标为 **experimental**。 [Flakes](https://nix.dev/concepts/flakes.html) `pure-eval` 会限制访问未声明/未哈希文件与网络并禁用部分 impure constants。 [Nix reference](https://releases.nixos.org/nix/nix-2.32.3/manual/command-ref/conf-file.html) 同时 NixOS reproducibility 站点明确说 Nix derivation 的确定依赖和 sandbox 是良好起点，却“不足以”保证 reproducible builds，时间戳等非确定性仍会破坏结果。 [NixOS reproducible builds](https://reproducible.nixos.org/)

因此不能把 Nix 与 Dagger 的两类 cache/graph 简化成“都可复现”。在本轮缺乏足够采用方工程案例的情况下，选择它也会使页面从“交付动作/验证计划”发散到 package management、dev environment 和 supply-chain provenance。

## 来源台账与状态审计

| 来源 | 发布/最后更新（若页面提供） | 产品状态或证据标签 | 访问 |
|---|---:|---|---:|
| [Dagger Functions](https://docs.dagger.io/core-concepts/functions/)；[API internals](https://docs.dagger.io/reference/api/internals/)；[Function caching](https://docs.dagger.io/extending/function-caching/) | Functions 搜索结果约两周前；caching 页面约四周前；未见正式发布日期 | 当前官方文档；Dagger Cloud 与单项云能力不在本研究中判阶段 | 2026-08-03 |
| [Develocity PTS manual](https://docs.gradle.com/develocity/predictive-test-selection)；[Test Distribution 2026.2](https://docs.gradle.com/develocity/2026.2/administration/build-acceleration/test-distribution/)；[Release notes](https://develocity.ai/releases/) | Develocity 2026.2：2026-06-30；PTS 文档版本 2026.2 | 当前支持版本的文档能力；不擅称 GA。PTS 明确包含 selection profile 和 remaining tests，Distribution 有 agent/capability/autoscale 边界 | 2026-08-03 |
| [Pinterest Engineering](https://medium.com/pinterest-engineering/understanding-develocity-build-data-with-honeycomb-78bc655f654f) | 2024-07-15 | 采用方一手工程文章；只证明 API/观测采用，不证明 PTS | 2026-08-03 |
| [EngFlow RE service](https://docs.engflow.com/re/index.html)；[overview](https://docs.engflow.com/overview.html) | 未标发布日期 | 当前文档化 Remote Execution；CMake RE 明示 beta，不作中心证据 | 2026-08-03 |
| [EngFlow Brave case](https://www.engflow.com/caseStudies/brave) | 未标发布日期 | 供应商案例；具名客户与数字均属厂商自述 | 2026-08-03 |
| [BuildBuddy RBE](https://www.buildbuddy.io/docs/remote-build-execution/)；[remote runners](https://www.buildbuddy.io/docs/remote-runner-introduction/) | 文档编辑日期约 2026-07-22（页面显示）；未见正式发布日期 | 当前文档能力；客户标志/自测不是独立采用证据 | 2026-08-03 |
| [Depot GitHub runners](https://depot.dev/docs/github-actions/overview)；[Dagger integration](https://depot.dev/docs/github-actions/integrations/dagger) | 未标发布日期 | 前者当前文档；后者明确 deprecated，将来移除 | 2026-08-03 |
| [Nix flakes](https://nix.dev/concepts/flakes.html)；[pure evaluation](https://releases.nixos.org/nix/nix-2.32.3/manual/command-ref/conf-file.html)；[reproducible builds](https://reproducible.nixos.org/) | Nix reference 2.32.3；其余未标发布日期 | flakes experimental；可复现性不是自动保证 | 2026-08-03 |

## 进入正式汇报前的门禁

1. 以首选或第二选择创建/补齐对应 Deep Dive，并逐主张回链；本文不替代该工作。
2. 若选 Develocity，确认目标企业的 JVM/JUnit Platform 覆盖、至少 `50 executions / 14 days` 的 PTS 数据阈值、must-run 规则及 remaining/full-suite 的实际门禁；再确认 Test Distribution 的 agent、能力匹配、远端 test resource 与传输开销。不能用 PTS 或 Distribution 代替 release gate。 [PTS FAQ](https://docs.gradle.com/develocity/predictive-test-selection) [Test Distribution](https://docs.develocity.ai/test-distribution/)
3. 若选 EngFlow，确认 action hermeticity、release build 是否禁用 remote persistent workers、cache silo、worker image 与隔离边界；不把厂商案例速度数字当作客户基线。
4. 未取得 Dagger 与任一候选的联合实施一手证据前，页内只称“分层可组合的架构推断”，不使用“已集成”“共同客户”或可相加 ROI。

## 供主 Agent 整合（≤150字）

选 **Dagger + Develocity PTS + Test Distribution**：Dagger 减少重复动作，PTS 让当前变更的验证集合由历史证据动态选择，Distribution 再并行剩余长测；必须保留后续 full/remaining tests，不能讲成自愈或自动发布。备选 **Dagger + EngFlow**：函数图与 REAPI action 级远程执行网格相邻，但都谈 DAG/cache，基础设施重叠更高。Depot 的 Dagger 集成已 deprecated；BuildBuddy 同层但证据弱于 EngFlow；Nix 更适合独立可复现性专题。
