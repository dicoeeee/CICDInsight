---
title: CI/CD 问题自愈深度研究：一手证据底稿
aliases:
  - CI/CD Self-Healing Research Evidence
tags:
  - cicd
  - agent
  - self-healing
  - deep-dive
status: complete
as_of: 2026-07-15
topic_id: cicd-self-healing
topic_type: scenario
source_policy: primary-only
time_window: 2025-07-01/2026-07-15
confidence: medium-high
---

# CI/CD 问题自愈深度研究：一手证据底稿

> 本文是 [[50_deepdives/cicd-self-healing/90_report|专题报告]] 的一手证据底稿，仅收录厂商官方文档、官方工程文章、官方开源仓库及研究论文。厂商披露的效果数字均标注为“厂商自报”，不等同于独立评测。

## 0. 摘要结论

1. **“发现问题并给建议”不等于自愈。** 截至 2026-07-15，市场上大多数 agentic CI/CD 产品默认停留在诊断、生成补丁或创建 PR；能在隔离环境验证并受控写回的较少，能在生产环境形成无人工逐次确认的闭环更少。
2. **真正可落地的是“有界自愈”，不是通用自治。** 当前最成熟的闭环集中在低歧义、可逆、有强判定器的场景：格式化/静态检查、部分构建失败、依赖或漏洞修复、已知瞬态基础设施故障、GitOps 漂移回归、基于 SLO 的金丝雀终止与回滚。
3. **Agent 负责提出候选，独立控制器负责判定。** 候选修复必须由 agent 无法修改的独立 oracle 验证；“流水线变绿”只证明既有检查通过，不证明功能、性能、安全或业务语义正确。
4. **应拆成快慢两条环。** 快环处理恢复服务或恢复流水线，例如重试、重新调度、回滚、回归声明态；慢环做根因分析、代码修复、测试补强和 PR。把两者混成一个万能 agent，会放大权限、延迟和误修风险。
5. **真正的 SH4 只发生在局部边界内。** Nx 对白名单任务可“生成—验证—推送”自动闭环；Harness 描述了 PR 分支上“修复—重跑—限次迭代”；Argo CD、Flagger/Argo Rollouts 在声明态或指标阈值内执行确定性闭环。它们都不等于“agent 自动合并并发布到生产”。
6. **公开基准仍显示通用修复很难。** 2026 年 CI-Repair-Bench 的 567 个真实 CI 故障中，最佳受测模型仅修复 18.9%；格式/静态检查相对容易，环境、依赖和配置问题明显更难。这与厂商演示中的高成功率并不矛盾，后者往往针对已筛选场景、内部数据或“提出 PR”而非“长期无回归的真实修复”。

## 1. 研究口径

### 1.1 自愈成熟度

| 等级 | 能力边界 | 典型输出 | 是否应称为闭环 |
|---|---|---|---|
| SH0 感知 | 检测、聚合上下文、告警 | 事件、告警、证据包 | 否 |
| SH1 诊断 | 分类、归因、给出置信度 | RCA、建议、运行手册步骤 | 否 |
| SH2 候选修复 | 生成可审阅变更 | Patch、PR、配置变更计划 | 否 |
| SH3 验证修复 | 在隔离环境执行并由独立 oracle 验证，写入受限目标 | 已验证 PR、临时环境结果、需审批动作 | 半闭环/人机闭环 |
| SH4 有界闭环 | 在预授权范围内自动检测、执行、观察，失败时回滚或熔断 | 白名单自动修复、声明态回归、指标驱动回滚 | 是，但只在声明边界内 |

判级看**默认产品行为和完整执行链**，不看宣传中的“autonomous”“resolve”等词。能够创建 PR 但仍需要人工审阅、合并和发布，最高通常是 SH2 或 SH3。

### 1.2 证据强度

| 等级 | 含义 |
|---|---|
| A | 可检查的官方技术文档、配置、开源实现或带原始方法/数据的研究 |
| B | 官方工程实践，架构和边界较清楚，但效果数据由厂商自行统计 |
| C | 官方发布/营销表述，缺少样本、基线、失败定义或独立复现 |

### 1.3 五类问题必须分开评估

1. **CI/构建修复**：格式、lint、编译、测试、流水线配置、runner、缓存和外部服务失败。
2. **代码/安全/依赖修复**：漏洞、升级、破坏性 API 变化、静态分析告警。
3. **测试自愈**：测试定位器、测试代码和测试数据的维护，不等于产品缺陷被修复。
4. **部署/GitOps 恢复**：声明态漂移、发布失败、金丝雀指标异常、配置回滚。
5. **发布后生产恢复**：事件诊断、降级/回滚/扩缩容等恢复动作，以及后续 fix-forward。

跨域比较时，不能把“自动重跑测试”“自动回滚发布”和“自动修复业务代码”混成一个成功率。

## 2. 可审计闭环模型

| 环节 | 最低输入/输出 | 必须的安全条件 | 失败时动作 |
|---|---|---|---|
| 1. 检测 | 绑定 commit、artifact、pipeline、环境和时间窗的事件 | 去重、抑制风暴；保留原始状态 | 合并重复事件或停止 |
| 2. 证据收集 | 完整日志、diff、测试、拓扑、近期变更、历史修复 | 日志和仓库内容均视为不可信输入；敏感信息脱敏 | 证据不足则只诊断 |
| 3. 分类/归因 | 失败类别、候选责任域、置信度、反证 | 多假设竞争；区分代码、基础设施、瞬态、外部依赖 | 低置信度升级人工 |
| 4. 候选修复 | 最小 patch、runbook 参数或回滚计划 | 禁止修改自身权限、验证器、门禁和审计策略 | 无可逆方案则停止 |
| 5. 策略/风险门 | 变更类型、目标、身份、环境、爆炸半径、预算 | 白名单动作；生产写操作默认审批；OPA/策略即代码 | 拒绝或降级为 PR |
| 6. 隔离执行 | 临时分支、沙箱、一次性环境、短期凭据 | 最小权限、网络出口限制、资源和时间预算 | 销毁环境并记录 |
| 7. 独立验证 | 原始失败检查、回归集、安全/性能/策略检查 | oracle 与修复 agent 权限分离；修复不能改测试来“过关” | 失败反馈一次或限次迭代 |
| 8. 受控应用 | 已签名/可追溯的 patch、PR、GitOps commit 或批准动作 | 分支保护、双人审批/责任人、制品与源码绑定 | 不满足条件不应用 |
| 9. 观察/回滚 | SLO、错误率、日志、业务 KPI、回滚点 | 观察窗、自动中止、补偿动作、熔断器 | 回滚/降级/停止新尝试 |
| 10. 学习 | 结果、人工反馈、复发情况、误修原因 | 只把经长期验证的结果入库；版本化、可撤销、设过期时间 | 隔离错误记忆 |

核心状态机应是：

`Detected → Classified → Proposed → PolicyAllowed → Sandboxed → IndependentlyVerified → Applied → Observing → Resolved`

并允许从任何写入后的状态进入 `RolledBack` 或 `Escalated`。不能用“agent 自己宣布成功”作为终态。

## 3. 问题分类与处理路由

| 故障类别 | 首选做法 | 可以自动化到何处 | 不应做什么 |
|---|---|---|---|
| 格式、lint、生成文件不同步 | 确定性 fixer → 重跑原检查 | 白名单内可 SH4 写回 PR 分支 | 让 LLM 任意重写代码 |
| 编译/类型错误 | 定位最小影响集 → patch → 编译与相关测试 → PR | 通常 SH3；低风险仓库可限次 SH4 | 只凭错误文本直接合并 |
| 单测/集成测试失败 | 复现 → 判断产品缺陷/测试缺陷/环境故障 → 修复 → 原测试+回归集 | 通常 SH2–SH3 | 删除、skip、放宽断言来变绿 |
| Flaky test | 多次/跨执行器复现 → 归因 → 修复；短期 quarantine 必须有 owner 和到期日 | 重跑可 SH4，但“重跑通过”仅恢复吞吐 | 无限重试或把 flaky 当修复完成 |
| 依赖/漏洞 | 专用分析器 → agent 生成候选 → 同一分析器复验 → 全量功能测试 → PR | SH2–SH3 | 只验证漏洞告警消失 |
| 流水线/构建配置 | 在复制的 CI 环境回放；策略检查；PR | SH3 | 在主分支直接实验配置 |
| Runner、缓存、网络、外部服务 | 识别瞬态信号 → 有预算的重试/换节点/清缓存/降级 | 已知动作可 SH4 | 修改业务代码“适配”短暂故障 |
| GitOps 声明态漂移 | 控制器把 live state 拉回 Git desired state | 确定性 SH4 | 让 agent 在集群手改且不回写 Git |
| 金丝雀指标异常 | 指标阈值控制器停止推进并回滚 | 确定性 SH4 | 让 LLM 自行解释指标后决定继续放量 |
| 生产事件 | 快环执行预批准的回滚/降级/扩容；慢环 RCA → fix-forward PR | 快环可局部 SH4，代码修复通常 SH2–SH3 | 允许通用 agent 以集群管理员身份自由操作 |

## 4. 案例对比：实际能力而非宣传标签

| 案例 | 领域 | 默认闭环/人工边界 | 成熟度判断 | 关键证据与限制 |
|---|---|---|---|---|
| GitHub CI Doctor | CI 诊断 | 失败后读日志、分类、查相似问题，创建诊断 issue/评论 | SH1，偶尔衔接 SH2 | 官方 workflow 是 `read-all` 加 safe outputs，并不直接修改代码；官方博客的 13 个建议中 9 个 PR 合并是早期内部样本、自报 |
| GitLab Fix CI/CD Pipeline | CI 修复 | 分析失败，生成 inline suggestion 或新 MR；人审合并 | SH2 | 只处理日志末尾 150 KiB；沙箱中依赖安装不一定能验证；AGENTS.md 不保证遵循 |
| CircleCI Chunk | CI/测试/配置 | UI 给分析并由用户决定是否开 PR；已知瞬态故障可按 prompt 预授权重跑 | 代码修复 SH2；瞬态重跑局部 SH4 | “90% flaky 分析可开 PR”“约 60% 修复”均是私测/客户自报，缺少方法；当前文档仍标 Beta |
| Harness CI Autofix / Worker Agents | CI 修复 | 读失败日志、改 PR 分支、重触发构建，限次迭代；合并/发布可加审批门 | SH3；PR 分支内可局部 SH4 | 有沙箱、短期 scoped token、OPA、审计和 max-turn；2026-06 发布材料，缺少跨客户独立效果数据 |
| Nx Self-Healing CI | CI 修复 | 默认建议审批；对白名单任务、高置信且已验证的修复可自动推送 PR 分支 | 默认 SH3；局部 SH4 | 中央配置任务白/黑名单；保护分支不生成修复；可 revert；适合格式、lint、编译等有强检查的任务 |
| Buildkite AI PR fixer 示例 | CI 修复 | 标签触发，MCP 取日志，容器内修复，推新分支/PR，等待 CI 并迭代；人工合并 | SH3 | 可检查的官方实践示例，不是默认平台能力或效果基准 |
| Snyk Agent Fix | 安全修复 | 多候选 → 静态引擎过滤/复验 → 推荐修复；用户必须审阅 | SH2–SH3 | 安全 oracle 能证明该漏洞不再命中，不能证明业务逻辑；旧文档称暂不支持跨文件修复；新架构指标为自评 |
| Dependabot + coding agent | 依赖/安全 | 分析 advisory 和依赖使用，创建 draft PR 并尝试修测试；人审 | SH2 | GitHub 明确警告建议可能不完整、错误或引入新问题；确定性 Dependabot 与 agent 应分层使用 |
| BrowserStack Self-Healing | 测试维护 | 用历史成功上下文替换失效 locator，继续执行；可应用 locator 或开 PR | locator 范围 SH3 | 必须已有成功执行；元素真正不存在、系统/WebDriver 故障仍应失败。其“可能掩盖真实问题”是风险推断，不是官方原话 |
| Argo CD Auto-Sync / Self-Heal | GitOps | live state 偏离 Git 时自动回归声明态 | 确定性 SH4 | prune、allowEmpty 等默认保护；失败的同 SHA sync 不会无限自动重试；启用 auto-sync 时不能直接 rollback |
| Flagger / Argo Rollouts | 渐进发布 | 按指标成功/失败阈值推进、暂停或回滚 | 确定性 SH4 | 强点是指标和动作可声明、可审计；agent 可辅助解释和生成配置，不应替代阈值控制器 |
| Akuity AI for GitOps | 部署/运维 | 读 Argo/Kargo 状态并给建议或执行受策略约束的工具；权限继承用户 RBAC | 默认 SH2–SH3；非生产白名单可局部 SH4 | 工具可 auto-allowed、approval-required 或 disabled；官方能力发布，无独立故障注入评测 |
| HolmesGPT remediation MCP | 生产运维 | 诊断工具可自动；restart/scale/drain/patch 等写操作始终要求人工审批 | 默认 SH1；批准动作 SH3 | 官方明确把风险决定固定在工具层，不让模型自行判定；要求 scoped ClusterRole、无 secrets、NetworkPolicy |
| AWS DevOps Agent | 生产诊断 | 多假设 RCA 和 mitigation plan；本体不执行 remediation | SH1–SH2 | 官方 2026-05 工程文明确“生成计划但不执行”；2026-07 与 Kiro CLI/CodeBuild 组合后可到 PR，但仍人审合并 |
| Spotify Honk | 代码代理验证 | 独立 verifier 在开 PR 前运行；失败不出 PR；格式/构建/测试后再用 LLM judge 检测越界/关测试 | SH3 | 数千次内部运行中 judge 否决约 25%、agent 修正其中约半数；厂商自报且无正式公开评测，但架构边界清楚 |
| Meta AI Regression Solver | 发布后性能回归 | 检测并归因到 PR/文件/行，生成 fix-forward PR 给原作者审阅 | SH2–SH3 | 官方自报把约 10 小时诊断降至约 30 分钟；整体节能“数百 MW”不能全部归因给该 agent |

## 5. 分领域深挖

### 5.1 CI 与构建修复：先把失败分流，再谈生成补丁

CI 红灯至少包含代码缺陷、测试缺陷、流水线配置、环境/依赖、runner/网络、外部服务和 flaky。GitHub CI Doctor 的官方 workflow 已把这些分类列为分析任务，CircleCI 也把“可靠性故障重跑”和“代码修复开 PR”区分成不同动作。正确架构应先分类，再路由到确定性重试、资源恢复或代码 agent；不能所有红灯都喂给 coding agent。

Nx 展示了当前最完整的低风险 PR 闭环：失败后执行 `nx fix-ci`，生成候选，在 Nx Cloud 后台验证；只有任务匹配自动修复模式、置信度高且建议已验证，才自动推送到 PR。格式、sync、conformance 等确定性任务是天然白名单；e2e 等高歧义任务应排除。保护分支不生成修复，进一步把爆炸半径压在 PR 内。

Harness 的 2026 Worker Agent 架构补上了执行控制：agent 是管道步骤，运行在客户控制基础设施的沙箱中；短期 token 权限取“agent 允许权限”和“调用者权限”的交集；OPA、审批门、审计、成本和 max-turn 限制共同约束循环。其 CI Autofix 可在 PR 分支修复、重跑构建、限次迭代，但公开资料没有证明它能安全自动合并或生产发布。

CircleCI 提出的 sidecar/microbuild 是值得复用的“两环验证”：内环在 Firecracker microVM 中快照环境，仅执行从 CI 提炼出的高信号检查并增量同步 patch；外环仍运行完整 CI，作为批准门。官方明确承认它不是完全 hermetic 的 CI 复现。这类架构的价值是降低 agent 迭代成本，而不是用近似验证替代完整 CI。

CI-Repair-Bench 为能力边界提供了外部校准：567 个真实实例、103 个仓库、12 类错误，并以原始完整 CI 重跑验证；最佳受测模型修复率仅 18.9%。这意味着企业落地首先要做故障分层、白名单和基线评测，而不是设置一个“通用自愈率”目标。

### 5.2 代码、安全与依赖：Analyzer → Agent → Analyzer

安全修复最适合由专用分析器夹住 agent。Snyk 的公开流程是：静态分析先最小化相关数据流上下文，模型生成多个候选，静态引擎再次排名、过滤无效或仍有漏洞的候选。2026 新架构又加入失败反馈重试。这个闭环能增强“漏洞是否被消除”的判断，却不能替代编译、全量测试、性能和业务语义检查；Snyk 文档也明确要求用户始终审阅。

Dependabot 体现了“确定性优先、agent 处理长尾”：有最近安全版本且可直接升级时，传统 Dependabot PR 更透明；存在破坏性变化、必须降级、没有直接补丁或需要复杂代码迁移时，才把 alert 分配给 coding agent。输出仍是 draft PR，GitHub 明确要求审阅和测试。

Kumushi 论文给出更一般的理论解释：没有根因信号时，agent 容易做“消除症状”的表面修改；引入动态故障定位、证据加权排序和两层质量评估后，才能更接近根因修复。论文也强调 oracle-passing 不等于真实修复，因此应把自动判定和专家语义审查分开记录。

### 5.3 测试自愈：维护测试资产，不是修复产品

BrowserStack 的 self-healing 聚焦 Selenium locator：从同一 elementIdentifier 的最近成功执行中提取上下文，生成替代 locator，继续测试，并记录 healed locator；用户可通过 MCP 应用或创建 GitHub PR。它要求至少一次历史成功执行，且系统/WebDriver 故障或元素确实不存在时仍会失败。

测试自愈的风险是**把界面语义变化误判为定位器变化**。这是从机制得出的风险推断，而非 BrowserStack 官方的直接警告。控制方式包括：

- 只允许修改 locator，不允许自动删除测试、skip、放宽业务断言或修改基线阈值；
- healed run 必须发出显式事件，不能与原始通过混为一类；
- 继续执行后仍需用语义断言、可访问性、截图或业务状态验证目标元素是否正确；
- 高价值路径要求人工批准 locator 写回；低风险 locator 变更也要有过期/回滚和复发监控；
- 将“因 healing 继续执行”计入 false-heal 指标，抽样回放未经 healing 的测试。

### 5.4 部署与 GitOps：确定性控制器应处在 agent 之外

Argo CD 的 `selfHeal` 是成熟的确定性闭环：Git 是 desired state，控制器发现 live state 漂移后自动同步。它不是 LLM 能力，却是 agentic self-healing 的控制面基准：目标态明确、diff 可审计、动作幂等，并有 prune/allowEmpty 等保护。agent 可以辅助解释差异或生成 Git 变更，但不应绕过 Git 直接手改集群。

Flagger 和 Argo Rollouts 把同一思想用于渐进发布：流量逐步切换，指标分析成功则继续，达到失败阈值则中止并回到稳定版本。应由确定性控制器读取 SLO/KPI 并做 promote/abort；agent 的职责是总结证据、提出阈值改进、生成 runbook 或 fix-forward PR。

Akuity 的 agent 对 Argo CD/Kargo 提供了工具化访问，并用三态策略约束工具：自动允许、需审批、禁用；同时继承调用用户的 RBAC。可接受的 SH4 场景应进一步限定为非生产、特定 app/namespace、特定 runbook 参数、可逆动作和观察窗。生产 delete、scale、修改镜像/内存等动作不应仅靠自然语言授权。

### 5.5 发布后生产恢复：快恢复与慢修复双环

AWS DevOps Agent 展示了较强的调查能力：以拓扑图为基础，同时展开多个根因假设，主动寻找反证，最后输出 mitigation 与 prevention。关键边界是官方明确说明该 agent **不执行 remediation**，写权限只用于票据/支持案例。2026-07 的官方实践用 EventBridge、Lambda、SQS、CodeBuild 和 Kiro CLI 把 mitigation plan 转成代码 PR；唯一人工触点是 PR 审批，然后才进入发布流水线。它是组件编排后的 SH3，不是 DevOps Agent 本体自动修生产。

HolmesGPT 的 Kubernetes remediation MCP 提供更直接的安全范式：读和诊断可自动；`restart`、`scale`、`drain`、`patch` 等最终通过 mutating kubectl 工具的动作始终要求人工批准。风险级别固定在工具层，不能让模型自己决定“这次写操作足够安全”；同时使用受限 ClusterRole、禁止 secrets 和 NetworkPolicy。

生产自愈可拆为：

- **快环（秒到分钟）**：已知症状 → 预批准 runbook → 回滚、降级、切流、重新调度或有限扩容 → SLO 观察 → 自动停止/补偿。只有可逆、幂等、低爆炸半径动作进入 SH4。
- **慢环（分钟到天）**：保留证据 → 多假设 RCA → 代码/配置 fix-forward → 独立测试 → PR → 渐进发布。Meta 的 AI Regression Solver 就是检测发布后性能回归、定位原始 PR/文件/行，再把修复 PR 交给原作者审阅的慢环案例。

## 6. 七种可复用实践模式

### P1. Diagnostic-to-PR：把自动化终点设在可审阅变更

适合未知代码缺陷、复杂配置、依赖迁移和生产根因修复。证据包、RCA、patch、验证记录和剩余风险放入同一个 PR；生成身份无合并权限。GitLab、Dependabot、AWS+Kiro、Meta 都符合这一主流边界。

### P2. Analyzer-Agent-Analyzer：用专用判定器夹住生成模型

适合 SAST、SCA、类型检查、策略和 IaC。第一次分析提供精确问题上下文，agent 生成最小候选，第二次由同类分析器验证问题消失，再运行更广的回归集。Snyk 是典型案例。

### P3. Classified Retry：只对已识别的瞬态故障重试

重试本身可恢复吞吐，却不等于修复。应记录失败指纹、runner、网络/服务状态、重试次数和最终结果；使用指数退避、总预算和并发限制。代码/确定性失败不得进入无限重试，flaky 通过后仍创建治理项。

### P4. Inner Verifier + Outer CI：快反馈不替代最终门禁

内环在隔离环境运行受影响的格式、编译、单测和微构建，向 agent 返回精炼反馈；外环由原 CI 基于干净环境和完整提交重新执行。Spotify Honk、CircleCI sidecar 和 Harness 的迭代都支持这一模式。

### P5. Runbook-Bound Action：模型只能填写参数，不能发明生产动作

把 `rollback_release(version)`、`restart_workload(namespace,name)`、`scale_within_cap(...)` 等动作封装为版本化工具；工具层做参数校验、RBAC、审批、幂等、审计和补偿。HolmesGPT 与 Harness 的控制设计可作为参考。

### P6. SLO Oracle Progressive Delivery：指标控制发布，agent 解释异常

用 Flagger/Argo Rollouts 等控制器完成 promote/abort；agent 为异常生成摘要、关联变更和后续修复 PR。禁止 agent 动态放宽失败阈值来让当前发布通过。

### P7. Incident Fast/Slow Loops：恢复与根治分离

快环只做已批准的可逆恢复，慢环负责根因和永久修复。二者共享同一个事件 ID、证据时间线和变更链，但拥有不同身份、权限、SLO 和成功标准。

## 7. 最低安全与治理控制

1. **身份分离**：诊断、生成、验证、合并、发布和生产操作至少分成不同权限；agent token 短期化、按任务签发。
2. **分支与环境隔离**：默认只写临时分支或 PR；保护分支、制品仓和生产集群不可由生成身份直接写入。
3. **oracle 不可变**：agent 不得修改触发失败的测试、门禁阈值、策略文件和验证脚本；确需修改时自动降级为人工评审。
4. **输入零信任**：日志、issue、PR 评论、仓库文档和依赖输出都可能包含 prompt injection；工具调用以结构化参数和允许列表仲裁。
5. **绑定精确对象**：证据、候选、验证和发布必须绑定同一 commit SHA、依赖锁文件、构建镜像、制品 digest、流水线版本和环境配置。
6. **预算和熔断**：限制 wall-clock、token、工具调用、重试次数、并发和累计变更行数；相同失败指纹连续误修时熔断。
7. **幂等与补偿**：生产动作必须可重复执行或能检测已执行；每个写工具都声明回滚/补偿步骤和不可逆风险。
8. **审计与可解释性**：保留原始证据、工具调用、策略判定、patch、验证、人工覆盖和回滚；自然语言摘要不能替代机器日志。
9. **记忆治理**：只吸收经过合并、发布观察窗和无复发验证的修复；记录适用版本与过期时间，错误经验可删除。
10. **kill switch**：按组织、仓库、失败类型、环境和工具分别关闭；不依赖模型配合。

## 8. 常见失败模式与反模式

| 反模式 | 后果 | 改进 |
|---|---|---|
| agent 生成补丁同时修改测试或门禁 | “自证正确”，把错误变绿 | oracle 权限隔离；门禁变更强制人工 |
| 只读取日志末尾或压缩摘要 | 漏掉首个因果错误，把后续噪声当根因 | 保存完整日志并提取因果链；显式标注截断 |
| 所有红灯统一重试 | 隐藏确定性失败、形成重试风暴 | 先分类；只对瞬态指纹使用限次重试 |
| 把“PR 已创建”统计为“自愈成功” | 成功率虚高 | 分开统计 proposed、verified、merged、deployed、30/90 天无复发 |
| 把“CI 绿”当业务正确 | 语义、性能或安全回归逃逸 | 原检查+回归集+领域 oracle+发布后观察 |
| 让测试 agent 自动 skip/放宽断言 | 质量门禁被侵蚀 | locator 等低风险变更白名单；断言变化人工审查 |
| 生成与合并/部署共用身份 | 单点误判直达生产 | 生成、批准、部署身份和策略分离 |
| 沙箱和真实 CI 环境不一致 | 内环通过、外环失败 | 内环用于反馈；完整 CI 作为最终 oracle；记录环境差异 |
| 无限 agent 迭代 | 成本失控、同一错误循环扩大 | max-turn、差异阈值、重复指纹熔断 |
| 从刚通过的结果立即学习 | 把偶然通过或隐性回归固化 | 合并/发布后观察窗、无复发和人工反馈后再入库 |
| GitOps agent 直接改集群不回写 Git | 声明态与现场继续分叉 | 变更走 Git；紧急动作必须产生追踪 commit/事件 |
| agent 动态放宽 SLO/金丝雀阈值 | 发布控制器失去独立性 | 阈值变更与当前发布解耦并单独审批 |
| 默认 `GITHUB_TOKEN` 创建 PR 后假设 CI 会触发 | 验证链实际未运行 | GitHub 官方说明默认 token 创建的事件通常不触发新 workflow；使用受控 App/PAT 或明确的验证触发机制 |
| 回滚点未经持续验证 | 真正事故时无法回滚 | 对回滚制品、数据库兼容和配置恢复做周期性演练 |

## 9. 评估与观测指标

### 9.1 漏斗指标

按失败类别分别统计：

`Detected → Correctly Classified → Candidate Produced → Independently Verified → Human Accepted → Merged → Deployed → No Recurrence`

推荐指标：分类 top-1/top-3 准确率、repair@1、限次 repair@n、独立验证通过率、人工接受率、合并率、部署率、30/90 天逃逸缺陷率、复发率、回滚率、误自愈率、人工接管率、TTG（time-to-green）、MTTR、每次成功修复成本、平均重试/工具调用次数、最大爆炸半径。

### 9.2 指标解释纪律

- “开出 PR 的比例”不能替代修复成功率；“合并率”也受审阅偏好和样本筛选影响。
- flaky 重跑通过应计为“流水线恢复”，不计为“根因修复”。
- 安全 analyzer 复验通过仅计为“目标告警消除”，还需功能和回归结果。
- 测试 locator healed 必须单列，不能混入原生 test pass rate。
- 供应商自报指标与内部独立回放/故障注入结果分开展示。
- 建立静默对照组或 A/B：先仅建议、不写入，得到误报、误修和人工接受基线后再开放 SH3/SH4。

## 10. 一手信息源清单

| 日期 | 来源 | 强度 | 直接支持的事实 | 局限/备注 |
|---|---|---:|---|---|
| 2026-01-13 | [GitHub: Meet the workflows—Quality & hygiene](https://github.github.com/gh-aw/blog/2026-01-13-meet-the-workflows-quality-hygiene/) | B | CI Doctor 调查失败并创建诊断 issue；早期 13 个建议中 9 个 PR 合并 | 效果数字为 GitHub 内部早期自报，样本很小 |
| 持续更新，访问 2026-07-15 | [GitHub CI Doctor workflow 源文件](https://github.com/githubnext/agentics/blob/main/workflows/ci-doctor.md) | A | `workflow_run` 触发、`read-all`、safe outputs、失败分类、去重；输出 issue/评论而非直接改码 | 示例/官方 workflow，不代表所有自定义 agentic workflow |
| 持续更新，访问 2026-07-15 | [GitHub Agentic Workflows: Triggering CI](https://github.github.com/gh-aw/reference/triggering-ci/) | A | 默认 `GITHUB_TOKEN` 产生的 PR/事件通常不会触发新的 workflow，需专门触发策略 | 是闭环实现细节，不是效果评测 |
| GA 18.8；19.1/19.2 更新 | [GitLab: Fix CI/CD Pipeline flow](https://docs.gitlab.com/user/duo_agent_platform/flows/foundational_flows/fix_pipeline/) | A | 读失败日志和代码，生成 suggestion/MR；只处理末尾 150 KiB；沙箱验证有限 | 当前 UI only；文档不证明会完整重跑原 CI |
| 2026-03-19 | [GitLab 18.10: Agentic SAST vulnerability resolution](https://about.gitlab.com/blog/gitlab-18-10-brings-ai-native-triage-and-remediation/) | B | 读漏洞代码、生成修复、自动测试、开 MR；要求人审 | Beta/厂商发布，无公开独立成功率 |
| 2026-06-30 | [Harness: Autonomous Worker Agents](https://www.harness.io/blog/introducing-autonomous-worker-agents) | B | 沙箱、scoped token、OPA、审批、审计；CI Autofix 修 PR 分支、重跑并限次迭代 | 发布材料；效果与失败率未公开 |
| 2026-07-02 | [Harness Code Quality Agents](https://developer.harness.io/3k-docs/platform/getting-started/agents/code-quality/) | A | Review、Coverage、Autofix；Autofix 从日志诊断并由 coding agent 开 PR | 基础文档未明确 basic Autofix 的独立完整重跑链 |
| 2025-10-14 | [Nx: What’s new in Self-Healing CI](https://nx.dev/blog/whats-new-in-nx-self-healing-ci) | B | 默认审批，支持对白名单任务自动应用；验证成功后推送 PR；Git 可 revert | 第一方产品材料，无独立大样本评测 |
| 持续更新，访问 2026-07-15 | [Nx Self-Healing CI documentation](https://nx.dev/docs/features/ci-features/self-healing-ci) | A | 保护分支、任务白/黑名单、flaky 重试、高置信+已验证才自动应用 | “高置信”校准方法未公开 |
| 2025-09-23 | [CircleCI: Introducing Chunk](https://circleci.com/blog/introducing-chunk/) | C | 能分析 flaky/red build/config 并开 PR；私测 90% 可开 PR、客户称约 60% 修复 | 厂商/客户自报，缺样本、基线和成功定义；当前文档仍标 Beta |
| 2026-01-28 | [CircleCI: Fix bugs faster with Chunk](https://circleci.com/blog/fix-bugs-faster-with-circlecis-chunk-ai-agent/) | B | 使用构建历史、测试、模式和代码；用户决定是否开 PR并在合并前审阅 | 官方使用说明，不是闭环评测 |
| 2026-06-02 | [CircleCI Changelog](https://circleci.com/changelog/) | B | 瞬态/基础设施失败可重跑；prompt 可预授权自动重跑；可靠性修复可开 PR | 行为受 prompt 和 Beta 产品变化影响 |
| 2026-06-05 | [CircleCI: Agentic validation needs different infrastructure](https://circleci.com/blog/agentic-validation-needs-different-infrastructure/) | B | microVM sidecar、快照、microbuild、patch 同步；完整 CI 仍作批准门 | 3x token/10–20x core cost 是内部测量；明确非完全 hermetic |
| 2025-12-01 | [Buildkite: AI-powered CI workflows—three examples](https://buildkite.com/resources/blog/building-ai-powered-ci-workflows-three-practical-examples/) | A-/B | 标签触发 PR fixer，MCP 读日志，容器修复、开后续 PR、等待 CI、迭代 | 可复用实例，不是平台默认能力或成功率研究 |
| 持续更新，访问 2026-07-15 | [Snyk: Fix code vulnerabilities automatically](https://docs.snyk.io/scan-with-snyk/snyk-code/manage-code-vulnerabilities/fix-code-vulnerabilities-automatically) | A | 多候选由静态引擎排名/过滤；要求始终审阅；建议可能破坏应用；不支持跨文件 | 文档可能描述旧版边界，需与新架构发布日期区分 |
| 2026-04-27 | [Snyk: Agent Fix agentic architecture](https://snyk.io/blog/snyk-agent-fix-agentic-architecture/) | B | 35k 漏洞/专家修复动态示例；失败候选反馈重试；安全、逻辑和 golden tests 评测 | 性能结论均为厂商自评；新架构计划 2026-05-26 到达 |
| 2026-04-07 | [GitHub: Dependabot alerts assignable to AI agents](https://github.blog/changelog/2026-04-07-dependabot-alerts-are-now-assignable-to-ai-agents-for-remediation/) | B | coding agent 可针对 alert 开 draft PR 并尝试修测试；明确要求人审和验证 | 发布说明，无修复成功率；多 agent 建议仍需比较 |
| 持续更新，访问 2026-07-15 | [BrowserStack Selenium Self-Healing](https://www.browserstack.com/docs/automate/selenium/self-healing) | A | 基于历史成功上下文修 locator 并继续；记录、MCP 应用/开 PR；说明限制 | 只覆盖 locator；“可能掩盖语义变化”是本文风险推断 |
| 持续更新，访问 2026-07-15 | [Argo CD Automated Sync Policy](https://argo-cd.readthedocs.io/en/release-2.10/user-guide/auto_sync/) | A | `selfHeal` 回归声明态；prune/allowEmpty 保护；失败同 SHA 不无限重试 | 链接为 2.10 文档；需按部署版本核对细节 |
| 持续更新，访问 2026-07-15 | [Argo Rollouts Analysis](https://argo-rollouts.readthedocs.io/en/stable/features/analysis/) | A | 指标成功/失败控制 rollout 继续或 abort，失败时 canary 归零并 Degraded | 确定性发布控制，不是 AI agent |
| 持续更新，访问 2026-07-15 | [Flagger deployment strategies](https://docs.flagger.app/main/usage/deployment-strategies) | A | 控制环逐步切流、评估 KPI、promote/abort，失败回到 primary | 确定性控制器；是否满足业务 SLO 取决于指标设计 |
| 2026-02-24 | [Akuity: AI Agents for GitOps Operations](https://akuity.io/blog/beyond-dashboards-ai-agents-for-gitops-operations) | B | 读取 Argo/Kargo 实时状态；继承用户 RBAC；工具可自动、审批或禁用 | 产品发布/厂商自述，无公开独立故障注入数据 |
| 持续更新，访问 2026-07-15 | [HolmesGPT Kubernetes Remediation MCP](https://holmesgpt.dev/latest/data-sources/builtin-toolsets/kubernetes-remediation-mcp/) | A | 写 kubectl 工具始终人工批准；读诊断自动；受限 ClusterRole、无 secrets、NetworkPolicy | 部署者仍可改变周边控制；默认推荐配置不是全自治 |
| 2026-05-27 | [AWS: Multi-agent reasoning for root cause](https://aws.amazon.com/blogs/devops/how-aws-devops-agent-uses-multi-agent-reasoning-to-find-root-causes/) | B | 拓扑、多假设、反证、mitigation/prevention；明确计划不执行 remediation | AWS 第一方技术说明，无独立 RCA 基准 |
| 2026-07-14 | [AWS: DevOps Agent + Kiro CLI automated incident remediation](https://aws.amazon.com/blogs/devops/automated-incident-remediation-with-aws-devops-agent-and-kiro-cli/) | B | EventBridge/Lambda/SQS/CodeBuild/Kiro 把计划转 PR；人工 PR 审批后发布 | 参考架构；75%/80%/94% 为预览客户自报，非组合方案独立评测 |
| 2025-12-09 | [Spotify: Feedback loops for background coding agents, part 3](https://engineering.atspotify.com/2025/12/feedback-loops-background-coding-agents-part-3) | B | 独立 verifier、失败不开 PR、确定性检查+LLM judge；judge 否决约 25% | Spotify 内部数千次自报；无正式公开 eval，但失败分类和边界具体 |
| 2026-04-16 | [Meta: AI Regression Solver](https://engineering.fb.com/2026/04/16/developer-tools/capacity-efficiency-at-meta-how-unified-ai-agents-optimize-performance-at-hyperscale/) | B | 发布后回归检测与归因，agent 生成 fix-forward PR；约 10 小时降至 30 分钟 | 厂商自报；整体数百 MW 收益不能全归因于该 agent |
| 2026-04-29 / rev. 2026-05-04 | [CI-Repair-Bench](https://arxiv.org/abs/2604.27148) | A- | 567 真实 CI 故障、103 仓、完整 CI 验证；最佳模型 18.9%；环境/依赖/配置更难 | arXiv 预印本；需后续同行评审和复现 |
| 2026-02-23 | [PhantomRun](https://arxiv.org/abs/2602.20284) | A- | 四个嵌入式 OSS、4000+ build failures；硬件依赖占多数；生成并验证后最高修复 45% | 专门领域、小范围项目；不可外推到通用 CI |
| 2026-05-05 | [Kumushi: Root-Cause-Driven Vulnerability Repair](https://arxiv.org/abs/2605.04251) | A- | 动态故障定位和证据加权；无根因信号易做表面修复；oracle pass 不等于真实正确 | 投稿中预印本；专家评估仍有主观性 |

## 11. 仍待验证的关键不确定性

1. **厂商成功率口径不可比。** CircleCI 的“可开 PR/修复”、GitHub 的“建议后 PR 合并”、Harness/Nx 的“验证通过”和研究基准的“原始完整 CI 通过”不是同一个指标。
2. **长期逃逸数据缺失。** 公开材料很少披露自动修复在 30/90 天后的回归、复发、安全事件或回滚率。
3. **“高置信度”的校准方法不透明。** Nx 等产品把高置信作为自动应用条件，但阈值、误校准率和不同任务类别的表现未公开。
4. **沙箱与生产等价性不足。** GitLab 明确依赖安装不一定可验证，CircleCI 明确 sidecar 非完全 hermetic；企业必须测量内外环分歧率。
5. **生产 agent SH4 缺乏公开规模化证据。** 当前最可信的生产闭环仍是声明态控制器、SLO 回滚和预定义 runbook，而不是通用 agent 自由写生产。
6. **prompt injection 与供应链攻击实测不足。** 产品文档强调最小权限和审批，但公开的红队、绕过率、恶意日志/PR 评测很少。
7. **数据库、状态迁移和跨仓库修复是明显空白。** 这些场景可逆性差、验证昂贵、责任边界复杂，不应从单仓 PR 修复能力直接外推。

## 12. 研究支持的最小实施契约

任何被称为“CI/CD 自愈”的方案，在进入生产试点前至少应回答并证明：

- 故障属于哪一类，是否有可测的分类置信度和反证？
- agent 可以读什么、写什么，身份是否与合并/发布/生产身份分离？
- 候选修复在哪个隔离环境执行，能否重现原始失败？
- 哪个独立 oracle 判定成功，agent 是否能修改它？
- 自动动作的仓库、任务、环境、资源和参数白名单是什么？
- 最大尝试次数、时间/成本预算、并发和重复事件策略是什么？
- 写入后观察哪些技术与业务指标，观察多久？
- 回滚/补偿是否真实演练过，何时自动熔断和升级人工？
- 结果如何绑定 commit、artifact、配置和环境，如何完整审计？
- 成功率是否按故障类别和漏斗阶段统计，是否包含误修、逃逸和复发？

若缺少其中任一项，能力应降级表述为“辅助诊断”“候选修复”或“人机协同修复”，而不是端到端自愈。
