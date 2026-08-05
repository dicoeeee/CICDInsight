---
title: "Google / AWS 与 Dagger 同页的 build/test 候选：依赖驱动验证优于算力卖点（2026-08-03）"
tags:
  - research/cicd
  - company/google
  - company/aws
  - comparison/platform-selection
status: complete
as_of: 2026-08-03
confidence: medium-high
---

# Google / AWS 与 Dagger 同页的 build/test 候选：依赖驱动验证优于算力卖点（2026-08-03）

> [!warning] 研究输入，不是正式页面
>
> 本文回答“Google、AWS 有没有比 Develocity 更适合与 Dagger 同页的突出 CI/build/test 平台或内部实践”。所有链接均于 **2026-08-03** 访问；资料没有明确标示 `GA`、`Preview` 或其他阶段时，仅记为“当前文档可见”，不倒推产品阶段。Google 的内部实践以公开论文/演讲为证据，**不等于可采购、可部署或已与 Dagger 集成的产品**；AWS 的客户采用、速度和成本数字没有取得独立同口径核验时不用于排名。

## 写作提纲

1. 固定 Dagger、Develocity 与已有页面的层次，避免把构建图、测试选择、CI 控制面和算力混为一谈；
2. 核验 Google Bazel/Blaze、Remote Execution 和 TAP 的 affected-test / 大规模实践；
3. 核验 AWS CodeBuild 的 Fleets、Lambda、batch graph、test fanout/cache，及可公开核验的内部交付实践；
4. 判断谁能形成一页独立主张，谁只是既有页面的实现选项；
5. 明确成熟采用证据与不能外推的边界。

## 结论先行

**Google 最强的新候选是 ICST 2026 的 CI/CD Variants + Target Comprehensive（TC）+ Late-Stage Testing（LST），不是单独的 Bazel/RBE。** 论文有公开全文（HTML 与 PDF），并将机制、44K projects 的生产数据、阶段与反例写得足够明确：平台自动从用户配置生成带 Bazel flags 的 variant CI projects；高频 TC cycle 保证每个 target 至少在一个架构运行一次；release 前以 LST 执行此前跳过的其余架构测试。它将“减少早期重复验证”与“release 前补齐配置覆盖”联成一个控制边界，既比单讲 Bazel/远程 worker 更接近验证计划，也比 PTS 的单一测试选择叙事更完整。 [ICST 2026 conference record](https://conf.researchr.org/details/icst-2026/icst-2026-industry/4/Taming-the-Variants-Multi-Architecture-Continuous-Testing-at-Google) [full preprint](https://hackthology.com/taming-the-variants-multi-architecture-continuous-testing-at-google.html)

**选择取决于页面类型。** 若页面必须是可采购、可部署的平台搭档，Develocity 仍优于 Google：Google 的 TAP/Rapid/Urfin/Forge 均为内部系统，公开材料不能证明外部可得性、Dagger 联合实施或可复制收益。若允许“公司工程实践”页，**Google Variants 比 Develocity 更适合作为 Dagger 的对照对象**：Dagger 解决交付动作如何以代码表达和复用；Variants 将跨架构验证配置生成为可治理的派生对象，TC/LST 将“早反馈”与“发布前完整配置覆盖”显式分段。它不应与 Bazel/RBE 同页再讲一遍，后两者只是该实践的 build/执行基座。参见已核验的 [Dagger + Develocity 候选研究](research-dagger-alternative-platform-candidates-2026-08-03.md)。

**AWS 没有发现比 Develocity 更适合的候选。** CodeBuild 的 batch build graph、fleets、Lambda 和 cache 是成熟的托管 CI 执行能力，却主要回答“把已定义 job 放到何种 AWS 算力上跑”。这会分别撞上既有的 GitHub、Harness、基础设施页；其 test fanout 是显式并行/分片，不是由代码依赖或历史构成的 affected analysis。AWS 对内部部署实践有公开的原则性材料，但本轮未找到可逐项核验、可作为同页主角的公开内部 build/test 系统名称、接口或体系结构。 [CodeBuild batch buildspec](https://docs.aws.amazon.com/codebuild/latest/userguide/batch-build-buildspec.html) [CodeBuild compute](https://docs.aws.amazon.com/codebuild/latest/userguide/build-env-ref-compute-types.html)

## 共同基线：页面不要重复什么

| 已有主题 | 已有中心问题 | Google / AWS 候选在同页时必须避开的重复 |
|---|---|---|
| Dagger | 将可组合的 Function/容器操作编排为执行 DAG，并以输入为基础复用结果。 [Dagger Functions](https://docs.dagger.io/core-concepts/functions/) [API internals](https://docs.dagger.io/reference/api/internals/) | 不把 Bazel target/action graph、CodeBuild batch graph 写成 Dagger Engine 的原生子图，也不能宣称互相调用。 |
| GitHub Agentic Workflows | repository 内 Agent 工作定义、权限边界和 hardened workflow。 | CodeBuild 托管 GitHub Actions runner 是运行器承载选择，不是新的 Agent 任务模型。 |
| Harness CI | pipeline/stage/step 的宽 CI 控制面。 | CodeBuild build graph / matrix 仍是 CI job 编排，应避免作为另一个“全栈 CI 平台”介绍。 |
| CI 自愈 | 失败后的诊断、授权修复和复验闭环。 | Bazel/TAP 的影响分析与 CodeBuild fanout 均不诊断、修复或证明发布安全。 |
| 基础设施 | runner、运行环境、策略与容量治理。 | RBE worker、CodeBuild Fleets/Lambda 的机器规格、VPC、cache 属于该层；只有“验证计划”或“声明式 action 语义”足够强时才可进入新页。 |
| CLI/MCP | 人或 Agent 的工具调用界面。 | 本轮不把 Bazel/CodeBuild CLI 或 API 当作卖点。 |

## 候选比较

| 候选 | 一句话能力 | 与 Dagger 的关系 | 成熟采用证据 | 可用页面主张与边界 | 结论 |
|---|---|---|---|---|---|
| **Google CI/CD Variants + TC + LST（ICST 2026）** | **集中生成跨架构 variant CI projects；高频 cycle 让每个 target 至少在一个配置运行，release 前补跑其余配置。** | Dagger 负责将交付动作表达/复用；Variants 将“同一项目在不同执行配置的验证义务”结构化、集中治理。与 Dagger 形成“执行图 + 验证矩阵”的互补，较少重复 Bazel/RBE 的 action/cache 层。 | 强，但为 Google 内部：全文报告 44K projects 的 real production data；该文称其全部功能在 Google 内部 **GA**。没有外部产品、联合客户或 Dagger 集成证据。 [ICST 2026](https://conf.researchr.org/details/icst-2026/icst-2026-industry/4/Taming-the-Variants-Multi-Architecture-Continuous-Testing-at-Google) [full text](https://hackthology.com/taming-the-variants-multi-architecture-continuous-testing-at-google.html) | **“当一个变更必须在多架构/多配置上成立时，CI 不应把 pipeline 复制一遍；它应集中生成可审计的 variant，并把高频验证与 release 前的剩余覆盖分层。”** 不能称为可购买平台，也不能将 25% 节省外推到非 Google。 | **公司实践页首选；商用产品页不替代 Develocity。** |
| **Google Bazel/Blaze + TAP affected-test 实践** | **把完整 BUILD 依赖转为本次变更的 affected test-target 集合；规模过大时以 milestone 聚合，并可进一步利用测试历史决定工作量。** | 互补点是“Dagger 表达交付函数，Bazel/Blaze 以已声明依赖计算验证覆盖范围”；重叠点是两者都依赖明确输入、图和 cache，不能合称同一执行图。 | 强，但为历史/内部证据：ICSE 2017 论文报告 TAP 规模；Bazel FAQ 称内部规则服务于每天数百万 builds。当前外部产品采用或 Dagger 联合采用未核验。 [ICSE 2017](https://research.google/pubs/taming-google-scale-continuous-testing/) [Bazel FAQ](https://cr.bazel.build/faq.html) | **“CI 的测试计划首先应由依赖证据界定，而不是把全量测试固定写进 YAML。”** 必须保留：TAP 是内部实践；affected 不等于足够的发布验证，论文也说明规模、延迟和资源约束。 | **Variants 的基础证据；不单独作为同页主角。** |
| **Google Bazel Remote Execution + remote cache** | **将显式 inputs/outputs 的 build/test actions 分发到多机器执行，并用 action cache/CAS 复用结果。** | Dagger 和 Bazel/RBE 都涉及执行图与缓存；可用来补充“从函数图到 action 级远程执行”，但比 Develocity 更靠近构建基础设施。 | 强：Bazel 是公开开源项目，当前文档覆盖 stable 8.6—9.2；Google FAQ 称内外共享大部分代码、内部规则用于数百万 builds/日。 [RBE](https://bazel.build/remote/rbe) [Remote cache](https://bazel.build/remote/caching) | **“声明完整 inputs/outputs，才能把 action 可靠地送往共享 cache 与远程执行。”** 必须保留：Remote Execution 要满足强制配置约束；没有 Dagger→REAPI 集成证据，不能画直接调用。 | **不替换 Develocity；如需基础设施深页可选。** |
| **AWS CodeBuild batch graph / fanout + Fleets** | **在托管 CI 中声明 job 依赖、矩阵或测试分片，并选 on-demand、reserved fleet 或具体环境执行。** | 与 Dagger 的 function DAG 同属“工作如何展开”，但粒度是 CI task；与 Harness/Buildkite/GitHub 的重叠高。 | 中：当前官方产品文档和 2024 document history 可证明持续演进；本轮无独立的大规模采用或效果证据。 [batch buildspec](https://docs.aws.amazon.com/codebuild/latest/userguide/batch-build-buildspec.html) [history](https://docs.aws.amazon.com/codebuild/latest/userguide/history.html) | 可说“在 AWS 私网、多 OS/CPU 或固定低启动延迟约束下，CI job 能按 fleet/compute 分派”。不得说是 affected testing、content-addressed build graph 或自动的最佳测试选择。 | **不选：基础设施/CI 控制面重叠。** |
| **AWS CodeBuild Lambda compute** | **用 Lambda 运行短时 build/test，换取较低启动延迟和自动扩缩。** | 与 Dagger 不构成新的验证/构建语义，只是执行环境选择。 | 中：AWS 2023-11-09 官方公告和当前计算文档可核验。 [announcement](https://aws.amazon.com/blogs/devops/aws-codebuild-adds-support-for-aws-lambda-compute-mode/) [compute docs](https://docs.aws.amazon.com/codebuild/latest/userguide/build-env-ref-compute-types.html) | 仅适合“短、轻量、Lambda 相同 base image 的 CI”；15 分钟上限，且不支持 local cache、batch 或 Docker image builds。 | **不选：基础设施页材料。** |
| **AWS 公开的内部交付实践** | **Builder's Library / Well-Architected 强调自动化 build、test、deploy、validation 和安全 rollout。** | 落在已有 CD/自愈/治理页面，而非 Dagger 旁边的 build/test 执行语义。 | 有第一方实践说明；但不是一份可实现的公开内部 build 平台规范。 [Builders' Library announcement](https://aws.amazon.com/about-aws/whats-new/2020/06/new-abl-article-automating-safe-hands-off-deployments/) [Well-Architected](https://docs.aws.amazon.com/wellarchitected/latest/framework/ops_dev_integ_build_mgmt_sys.html) | 只能作为安全交付控制边界的佐证；**本轮没有找到可以公开核验的 Amazon/AWS 内部 build/test 系统，不能给它起名、描绘架构或列为候选产品。** | **证据不足，不选。** |

## Google：为什么值得保留

### ICST 2026 Variants：有公开全文，机制足以成为独立主张

**全文与状态。** 2026-05-20 的 ICST 2026 Industry 条目链接了作者公开的 [HTML 全文与 PDF](https://hackthology.com/taming-the-variants-multi-architecture-continuous-testing-at-google.html)。会议记录明确称其为对 Google 44K projects 的真实生产数据案例研究，并称文中功能已在 Google 内部 **Generally Available**。这里的 GA 只适用于 Google 内部能力，不能写成面向外部客户的 GA 产品。 [ICST 2026 record](https://conf.researchr.org/details/icst-2026/icst-2026-industry/4/Taming-the-Variants-Multi-Architecture-Continuous-Testing-at-Google)

**机制。** 论文披露的最小可复述机制是：

1. **Variants：** 平台从用户已有 CI Project/Blueprint 自动生成派生 project，并统一添加 Bazel flags（例如 `--cpu=arm`）；每个 variant 有 `DISABLED`、`SHADOW`、`RELEASE` 模式。`SHADOW` 可在无需用户改配置的情况下收集全库测试结果，准备好后以顶层字段切换为 release。 [full text, Variants](https://hackthology.com/taming-the-variants-multi-architecture-continuous-testing-at-google.html)
2. **TC（Target Comprehensive）：** 依靠这些 centrally defined、audited variants，高频 TAP cycle 保证 target **至少在一个** configuration（x86、Arm 或其他 variant）运行一次，而非在每个架构重复跑。它不是“完全不测 Arm”，而是把每轮的配置覆盖作为一个显式取舍。 [full text, TC](https://hackthology.com/taming-the-variants-multi-architecture-continuous-testing-at-google.html)
3. **LST（Late-Stage Testing）：** 在 production push 前执行 TC 略过的剩余配置测试；对 Urfin 管理的 release，平台能识别生产改动时点并将补测延后至接近 production push。Rapid Native 因不能可靠识别这一时点，仍要较早地完成传统测试。 [full text, release integration](https://hackthology.com/taming-the-variants-multi-architecture-continuous-testing-at-google.html)
4. **控制边界与结果：** 论文报告 total machine cost 约节省 25%，但代价是少量延迟的 breakage detection；论文也记录 Rapid Native 出现潜在约 0.1% failure-rate regression，而 Urfin 的 late-stage failures 明显下降。其作者明确说组织、项目的 platform-specific behavior 不同，结果不保证可泛化。 [full text, results and threats](https://hackthology.com/taming-the-variants-multi-architecture-continuous-testing-at-google.html)

这比现有 Bazel/基础设施页多出三层不可替代的内容：**派生配置的治理对象、早期跨配置覆盖的明确保证、与 release 阶段 remaining-test 补偿相连的控制边界。** Bazel 仍是 target/action build system，Forge/RBE 仍是远程执行集群；它们是实现基座，不应占据该页的主图。亦因此它不会与“更多 runner/Fleet”的基础设施页重合。

建议的单页主张为：

> **当软件要跨架构交付，CI 的难题不是把一条 pipeline 复制两遍，而是把“同一验证义务的多个配置”生成为可治理对象：Dagger 把交付动作表达为可复用函数；Google Variants 让测试配置集中派生，TC 争取早反馈，LST 在 release 前补齐其余配置覆盖。**

这仅是对两种已公开机制的**分层推断**；未发现联合实施材料，不得绘制 Dagger 调用 TAP/Rapid/Urfin，或声称 Google 的 25% 节省可相加到 Dagger。

### 1. Bazel/Blaze 将“受影响测试”建立在依赖事实，而非概率猜测上

Google 的公开 Bazel FAQ 说明：BUILD 文件要求库、测试和二进制完整列出直接依赖，因此可预测一次改动影响哪些程序和测试，并在提交前进行验证；同页说明 Bazel 只重编译所需内容，也可跳过未变的测试。 [Bazel FAQ](https://cr.bazel.build/faq.html)

ICSE-SEIP 2017 的 Google/学术团队论文将这一机制披露为 TAP：从 changelist 的修改文件出发，结合 BUILD dependencies 和隐式语言依赖建立 reverse dependency structure，得到直接或间接依赖修改文件的 **AFFECTED** test targets。论文还明确该集合的粒度取决于 BUILD 声明、依赖关系和测试组织，不是“只要文件路径相近就跳过测试”。 [paper PDF](https://research.google.com/pubs/archive/45861.pdf)

这提供了一个比“智能选择一些测试”更严格的页面主张：

> **CI 的执行计划不应只问“有什么步骤”；在大型代码库中，它首先要把变更映射为必须获得的验证证据。Dagger 将交付动作表达为可复用函数；Google 的 Bazel/Blaze 实践把已声明依赖推导为 affected test targets，再把可隔离 action 扩展到远程执行。**

这是**架构推断**，不是 Dagger、Bazel 或 Google 的联合产品声明。

### 2. 公开规模证据足够强，但不可当作目标企业基线

- TAP 论文所报告的历史样本为平均每天逾 13K code projects、800K builds、150M test runs；当时系统平均每秒约一个 commit，milestone 峰时约每 45 分钟切分。 [ICSE 2017 paper](https://research.google/pubs/taming-google-scale-continuous-testing/)
- 论文还披露，某些 milestone 可经由 changed files 的 reverse dependencies 选择到 4.2M tests；即使拥有大量算力，逐 change 回归并不可持续。 [paper PDF](https://research.google.com/pubs/archive/45861.pdf)
- 2021 年 Google authors 的 *Smart Build Targets Batching Service* 继续报告其 monolithic codebase 有数千万 build targets、build service 每天运行数百万 builds，且针对 build 内存/截止期实施 target batching。 [preprint](https://arxiv.org/abs/2102.09098)
- 2025 年 Google Research 的 *Multiarch Testing at Google* 说明 TAP 有数十万 projects，正在通过结构化 variants 和历史数据支持多架构、机器学习驱动的测试调度；这证明实践仍在演进，但它是报告/演讲，不能推断外部 SDK 或通用产品可得性。 [Google Research](https://research.google/pubs/multiarch-testing-at-google/)

### 3. Remote Execution 给出的不是“更多 runner”，而是 action-level 约束

Bazel 当前 RBE 文档说，默认 build/test 在本机运行，Remote Execution 可把 build/test actions 分发到多台机器；其目的包括并行动作的扩展、团队一致执行环境和 outputs 复用，底层使用开源 gRPC protocol。 [Remote Execution Overview](https://bazel.build/remote/rbe)

远程 cache 文档进一步限定了正确性语义：action 显式声明 inputs、output names、command line 与 environment；cache 包括 action-hash→result metadata 映射和 outputs 的 CAS。 [Remote Caching](https://bazel.build/remote/caching)

因此 RBE 只有在页面需要解释“为何完整的 action 声明让远程化仍可复用/可验证”时才值得出现；不要把它讲为笼统的云 runner 扩容。

### Google 的产品状态、边界与 Develocity 对比

- **状态：** Variants/TC/LST 在论文中为 **GA at Google**，不是外部产品。Bazel 为公开开源项目；本轮当前文档列出 Nightly 与 8.6—9.2 版本，RBE 页面最后更新 **2026-07-14**。资料未在本轮将 RBE 单列 GA/Preview，故只记“当前文档化能力”。 [ICST 2026](https://conf.researchr.org/details/icst-2026/icst-2026-industry/4/Taming-the-Variants-Multi-Architecture-Continuous-Testing-at-Google) [RBE](https://bazel.build/remote/rbe)
- **成熟度：** Google 的强证据是内部长期采用与公开论文；它不自动等于一般企业无需改造即可采用。Bazel FAQ 也说其更适合大型、多编译语言、多平台且测试密集项目；few long sequential steps 的收益较小。 [Bazel FAQ](https://cr.bazel.build/faq.html)
- **与 Develocity 的本质差异：** Bazel/Blaze affected analysis 是以声明依赖给出“影响边界”；Develocity PTS 是使用变更、结构与历史在测试集合内做概率性优先/选择。两者都可减少早期验证工作，但不能写成同一算法，也不能暗示后者继承前者的完整性保证。
- **必须保留 full/remaining tests 与独立门禁：** Google 论文说明受规模、资源和延迟限制，测试策略会聚合 commits 与延后部分 affected tests；affected 集合只表示依赖影响，不是 security、发布安全、生产行为或人工审批的 oracle。 [paper PDF](https://research.google.com/pubs/archive/45861.pdf)
- **组合缺口：** 未找到 Dagger 与 Bazel/Blaze/RBE 的官方联合产品、正式 adapter、联合客户或可叠加性能收益；正式页面只能称“分层架构推断”。

## AWS：真实能力为何仍不适合本页

### CodeBuild batch：有 graph 和 fanout，但它是 CI task 编排

CodeBuild 的当前 `buildspec` 支持 `build-graph`（task 依赖，task 在依赖完成后运行）、`build-list`、`build-matrix` 和 `build-fanout`。fanout 可把一个 task 拆为并行 test builds；官方示例以 `codebuild-tests-run` 和 `equal-distribution` 分片。 [batch buildspec](https://docs.aws.amazon.com/codebuild/latest/userguide/batch-build-buildspec.html)

这足以证明 AWS 有 CI job graph 与 parallel test sharding；**不足以证明**它根据 source-to-test dependency、coverage 或历史失败率自动形成 affected-test 集合。故不能把 CodeBuild 写成 Google TAP 或 Develocity PTS 的替代。

### Fleets、cache 与 Lambda：执行面条件，不是新验证模型

- 当前文档说明 EC2 compute 可用 on-demand 或 reserved fleets；reserved fleet 可选择 vCPU、memory、disk，让 CodeBuild 匹配支持的 instance type。Linux x86/Arm/GPU、Windows 和 macOS 都有相关环境选择，具体可用性依 Region。 [compute docs](https://docs.aws.amazon.com/codebuild/latest/userguide/build-env-ref-compute-types.html)
- reserved fleet 支持跨 projects 使用；同时 AWS 明确警示同一 account 内其他项目可访问 fleet instance 上的 source、Docker layers 和 buildspec 指定 cached directories。这是重要的 tenancy/cache 边界，不能把 persistent fleet cache 误作内容寻址或跨租户隔离保证。 [fleets](https://docs.aws.amazon.com/codebuild/latest/userguide/fleets.html)
- CodeBuild cache 只有 Amazon S3 或 local；local 包括 source、Docker layer、custom cache。Docker layer cache 仅 Linux 且要求 privileged mode，后者会让容器访问所有设备。 [cache docs](https://docs.aws.amazon.com/codebuild/latest/userguide/build-caching.html)
- Lambda compute 的官方公告为 **2023-11-09**；它针对短、轻量 CI 的较低启动延迟，但最长 15 分钟，且不支持 local cache、batch builds 或 Docker image builds。 [AWS announcement](https://aws.amazon.com/blogs/devops/aws-codebuild-adds-support-for-aws-lambda-compute-mode/)
- AWS CodeBuild history 显示 reserved fleets 在 **2024-08-19** 支持 macOS、**2024-11-06** 支持 attribute-based compute、**2024-11-12** 支持 ARM/Linux/Windows 的 non-container builds。这证明能力持续更新，不能反推各功能都同一时点 GA。 [document history](https://docs.aws.amazon.com/codebuild/latest/userguide/history.html)

### AWS 内部实践：能核验的仅是交付原则，不是可复用 build 平台

AWS 将 Builders' Library 描述为由 Amazon senior technical leaders/engineers 撰写、解释 Amazon.com 与 AWS 软件如何架构、发布和运行的材料；其 2020-06-18 公告提及 Amazon 可自动化到每年逾 150M deployments。该数字是第一方历史自述，且针对部署，不证明 CodeBuild 的采用、Bazel 类的构建图或测试选择机制。 [announcement](https://aws.amazon.com/about-aws/whats-new/2020/06/new-abl-article-automating-safe-hands-off-deployments/)

AWS Well-Architected 的公开建议是使用 CodeBuild、CodePipeline、CodeDeploy 等将 code check-in、build、test、deploy、validation 自动化；这是一套公共产品/方法框架，并未公开 Amazon 内部 build/test orchestration 的名称或协议。 [Well-Architected](https://docs.aws.amazon.com/wellarchitected/latest/framework/ops_dev_integ_build_mgmt_sys.html)

因此本轮结论是 **evidence gap**：可以引用 AWS 的安全交付原则，但不能把未具名的 Amazon 内部 pipeline 当作与 Dagger 同页的平台，也不能据此宣称它优于 Develocity。

## 推荐顺序与进入正式页面前的门禁

1. **保持 Dagger + Develocity 为产品页首选。** 它以“执行复用 + 验证选择/分发”形成较清晰的双计划，不进入 RBE/runner 基础设施细节。
2. **若需要 Google 作为公司实践页，选 Bazel/Blaze + TAP。** 页面必须聚焦“dependency-derived affected tests”，RBE 仅作为下层执行条件；标题不应写成“Dagger 集成 Bazel”。
3. **AWS CodeBuild 不作为本轮搭档。** 它可在基础设施页以部署条件/执行环境出现，或在 GitHub Actions on AWS 的运行器选择中出现；不新建与 Dagger 的对比页。
4. **正式 Presentation 前：** 为选中的 Google 或 AWS 对象建立/补齐 `presentation_ready: true` 的 Deep Dive；逐一核验受众是否已有 BUILD target discipline、hermetic action、完整依赖声明、远端执行环境、full-suite/release gates 与 cache tenancy 边界。缺任一项，页面应保持 `blocked-by-deep-dive`。

## 来源与状态记录

| 来源 | 发布/更新日期 | 能证明什么 | 产品/证据状态 | 访问日期 |
|---|---:|---|---|---:|
| [Bazel Remote Execution Overview](https://bazel.build/remote/rbe) | 最后更新 2026-07-14 | RBE 分发 build/test actions、共享 output、开源 gRPC protocol、需要配置约束 | 当前文档化能力；页面未将 RBE 单列 GA/Preview | 2026-08-03 |
| [Bazel Remote Caching](https://bazel.build/remote/caching) | 本轮页面未见发布日期 | action 的显式 inputs/outputs、action cache 与 CAS | 当前文档化能力 | 2026-08-03 |
| [Taming the Variants: Multi-Architecture Continuous Testing at Google](https://hackthology.com/taming-the-variants-multi-architecture-continuous-testing-at-google.html)；[ICST 2026 record](https://conf.researchr.org/details/icst-2026/icst-2026-industry/4/Taming-the-Variants-Multi-Architecture-Continuous-Testing-at-Google) | full text 2026-03-19；会议 2026-05-20 | 自动派生 variant、TC、LST、44K projects、machine/human-cost 评估与反例 | 作者公开 preprint；会议称所有功能 **GA at Google**，不是外部产品 GA | 2026-08-03 |
| [Bazel FAQ](https://cr.bazel.build/faq.html) | 页面未标日期 | Google 内部的 Bazel/Blaze 采用、完整依赖、affected tests、适用边界 | 历史/背景资料；不以此判断当前版本状态 | 2026-08-03 |
| [Taming Google-Scale Continuous Testing](https://research.google/pubs/taming-google-scale-continuous-testing/) | ICSE-SEIP 2017 | TAP、affected targets、历史规模、milestone 和局限 | 公开原始研究，历史内部实践 | 2026-08-03 |
| [Smart Build Targets Batching Service at Google](https://arxiv.org/abs/2102.09098) | 2021-02-18 preprint；ICSE-SEIP 2021 | 数千万 targets、数百万 builds/day、资源预测/批处理 | 公开原始研究，内部实践 | 2026-08-03 |
| [Multiarch Testing at Google](https://research.google/pubs/multiarch-testing-at-google/) | 2025 | TAP variants 与历史数据/ML 调度 | 公开研究演讲；不证明外部产品 | 2026-08-03 |
| [CodeBuild batch buildspec](https://docs.aws.amazon.com/codebuild/latest/userguide/batch-build-buildspec.html) | 本轮页面未见发布日期 | graph/list/matrix/fanout、依赖和 equal-distribution sharding | 当前文档化能力；未见阶段标签 | 2026-08-03 |
| [CodeBuild fleets](https://docs.aws.amazon.com/codebuild/latest/userguide/fleets.html)；[compute](https://docs.aws.amazon.com/codebuild/latest/userguide/build-env-ref-compute-types.html) | 本轮页面未见发布日期 | reserved/on-demand、OS/CPU、VPC、共享 fleet cache 边界 | 当前文档化能力；未见阶段标签 | 2026-08-03 |
| [CodeBuild Lambda announcement](https://aws.amazon.com/blogs/devops/aws-codebuild-adds-support-for-aws-lambda-compute-mode/) | 2023-11-09 | Lambda compute 的定位和 15 分钟/cache/batch/Docker 限制 | 官方公告；当前可用性仍以 docs 为准 | 2026-08-03 |
| [CodeBuild document history](https://docs.aws.amazon.com/codebuild/latest/userguide/history.html) | 2024-08—11 条目 | macOS、attribute-based compute、non-container fleet 的更新时间 | 更新记录，不等于全部功能 GA 结论 | 2026-08-03 |
| [AWS Builders' Library announcement](https://aws.amazon.com/about-aws/whats-new/2020/06/new-abl-article-automating-safe-hands-off-deployments/)；[Well-Architected](https://docs.aws.amazon.com/wellarchitected/latest/framework/ops_dev_integ_build_mgmt_sys.html) | 2020-06-18；后者未标 | Amazon/AWS 的安全自动化交付原则 | 第一方实践/指导；非公开内部 build 平台规范 | 2026-08-03 |

## 供主 Agent 整合（≤150字）

首选 Google **ICST 2026 Variants + TC + LST** 实践页：自动生成跨架构测试配置，高频 cycle 每 target 至少跑一种配置，release 前补齐其余配置；44K projects 的生产案例称内部 GA，但不是外部产品。它比单讲 Bazel/RBE 更少撞基础设施页，且与 Dagger 可形成“执行图 + 验证矩阵”分层；不能宣称集成或外推 25% 节省。若要可采购产品，仍选 Develocity。AWS CodeBuild 仍偏 CI 执行/基础设施，未发现可核验的 AWS 内部 build/test 平台，不入选。
