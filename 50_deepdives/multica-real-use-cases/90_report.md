---
title: Multica 与业界真实使用场景深度调研报告
aliases:
  - Multica Real Use Cases Report
tags:
  - research/agentic-cicd
  - research/report
  - company/multica
  - topic/agent-orchestration
status: complete
as_of: 2026-08-29
confidence: medium
---

# Multica 与业界真实使用场景深度调研报告

- **观察日：** 2026-08-29
- **Multica 基线：** v0.4.36（2026-08-28）与观察日 main 文档
- **研究范围：** Multica 当前机制与公开真实使用；其他 Agent 平台的 Issue→PR / CI / Review / 多 Agent 案例；企业适配与试点
- **证据口径：** 官方自用 > 具名客户案例 > 社区用户自述 > 产品文档/教程 > 愿景/请求

> [!summary] 一句话结论
> Multica 最值得采用的角色是“多 Agent 协调、上下文与审计层”：把 Claude Code、Codex 等执行单元放进统一 Issue/Project/Task 体系，保留运行历史并把候选产物交回 Review。Multica 已有公开可复核演示与多种社区实测，其他 Agent 平台的行业案例也证明这类模式对有界、重复、可验证任务有效；但 Multica 自身尚无公开具名生产客户或独立质量数据，默认 Task 又继承 daemon 用户全部权限，Squad 也不是严格状态机。因此应从低风险 Issue→draft PR、只读 Review 和周期巡检开始，外部 CI、安全、合并与发布门必须保持独立。

## 一、执行摘要

### 核心判断

1. **Multica 不是另一个 Coding Agent。** 它不自带模型，而是驱动现有 Agent CLI；核心产物是统一的任务分派、运行状态、讨论、上下文和历史。
2. **Multica 已有多类真实运行，但证据仍早期。** 公开材料覆盖端到端 Webhook Inbox、Dev→Reviewer→Tester、八阶段 Squad、高频 Autopilot、MCP 编排、Slack Bridge、多云 Runtime 和完成证据门；没有公开具名客户、SLA、独立质量、稳定性或 ROI 证据。
3. **其他平台的行业案例都有明确边界。** GitHub 把框架化实现交 Agent、研究和 darkship 留给人；Asana 并行迁移但逐项审查；Datadog 用历史事故回放验证 Review；Barclays 只公开到失败日志解释；AMD 的大规模分诊仍是候选修复而非发布授权。这些不是 Multica 客户案例。
4. **Multica 的 Issue/Task 模型与这些模式相容。** 它很适合承载“意图→候选产物”的异步过程；Task `completed` 不代表 Issue、质量或业务完成。
5. **当前两条硬边界不能忽略。** 一是默认执行无文件系统沙箱，真正边界是 daemon 用户/容器/VM；二是 Squad 是 leader 路由，不是步骤、条件、审批、重试均可强制的工作流引擎。
6. **采用策略应是分级而非全量标准化。** 清晰、低风险、可自动验证的任务先进入 Multica；模糊探索、高风险核心变更、高频确定性事件和生产发布保留在更合适的工具与强门禁中。

### 场景优先级

| 优先级 | 场景 | 建议 |
|---|---|---|
| P0 | 文档、测试、feature flag 清理、重复重构、简单 bug | 立即试点，输出 draft PR |
| P0 | 只读 PR Review、周期代码健康检查 | 立即试点，先草稿或只读评论 |
| P1 | CI 失败日志分诊与候选修复 | 条件试点，必须由原失败任务重跑验证 |
| P1 | Planner→Coding→Review→Test | 人工确认和 handoff 稳定后再启用 Squad |
| P2 | 大迁移、跨仓库并行、夜间批处理 | 建 Project、worktree、批量上限、golden cases 后试点 |
| 暂缓 | 自动 merge/deploy、生产变更、高频确定性对账、模糊产品探索 | 使用外部 CI/CD/规则/交互工具；不要默认交给 Multica Agent |

## 二、Multica 到底是什么

### 2.1 产品模型

Multica 的对象分工很清楚：

| 对象 | 负责什么 | 不负责什么 |
|---|---|---|
| Workspace | 成员、Agent、Issue、设置的协作边界 | 不是 OS 或云租户安全边界 |
| Issue | 目标、讨论、assignee、状态与最终工作记录 | 不是一次 Agent 进程 |
| Agent | 名称、Instructions、Skills、模型、Access、Runtime 的长期配置 | 不是常驻自主进程 |
| Task | 一次触发到结束的运行记录、transcript、失败原因和成本 | `completed` 不证明 Issue 目标完成 |
| Runtime | 哪台机器、哪个 Agent CLI 执行 | 不自动提供安全沙箱 |
| Project | 多 Issue 的目标、描述、仓库/本地目录资源与进度 | lead 不会自动 assign/运行 |
| Squad | 一个 leader 对多成员的动态路由层 | 不会自动广播，也不是严格 DAG |
| Skill | 可复用的方法、模板、脚本与资料 | 第三方内容不由 Multica 审查或签名 |
| Autopilot | schedule/webhook/manual 触发与 run history | 不保证每个任务都应该用 LLM |

一次标准运行是：

`Issue/mention/chat/autopilot 触发 → Task 入队 → Runtime claim → 本地 CLI 读写代码/执行命令 → transcript 与结果回写 Issue`

官方机制见 [How Multica works](https://multica.ai/docs/how-multica-works)、[Agents](https://multica.ai/docs/agents) 与 [Tasks](https://multica.ai/docs/tasks)。这说明 Multica 的核心不是推理本身，而是让多个推理/执行单元出现在同一工作系统中。

### 2.2 与相邻层的边界

| 相邻层 | Multica 与它的关系 |
|---|---|
| Claude Code / Codex / Cursor / Copilot CLI | Multica 选择、配置和启动它们，不替代它们 |
| Git / GitHub / GitLab | Multica 提供仓库资源、worktree 和 PR 状态关联；最终分支规则与 merge 权仍在 Git host |
| CI / Test / Scan | Multica 可触发 Agent 分析结果，但测试、扫描与 policy 才是独立 Oracle |
| Workflow Engine | Squad 负责动态路由；严格步骤、条件、补偿与审批仍需外部工作流或后续能力 |
| Runtime Sandbox | Multica 默认不提供；需要专用用户、容器或 VM |
| Project Management | Multica 有 Project/Issue/状态，但当前研究重点是 Agent execution/control，而非完整企业 PPM |

### 2.3 产品成熟度

观察日前最新公开版本是 [**v0.4.36**](https://github.com/multica-ai/multica/releases/tag/v0.4.36)。仓库更新和 Release 很频繁，Cloud、Desktop、CLI、自托管、Slack/Lark 等路径均有当前文档；这足以支持“可下载、可运行、快速迭代的早期产品”。

但公开材料未提供 GA 声明、SLA、具名客户、企业安全认证或质量/稳定性指标。[Multica License](https://github.com/multica-ai/multica/blob/main/LICENSE) 也不是无附加条件的 Apache-2.0：对外托管或商业嵌入需要额外许可核对。因而不能把“有 Release、可自托管”推断成“企业生产成熟”。

## 三、Multica 本身的公开业界与社区使用案例

> [!important] 证据边界
> 本节只收录**真实运行过 Multica 本身**的案例，不包含 Copilot、Codex、GitLab Duo 或 Factory 的案例。当前材料主要来自开源作者和社区用户：最强一例有公开 PR、Git、测试和验收产物，其余多为带环境、Task、日志或数量的用户自述。它们能证明“Multica 被实际运行”，仍不能证明具名企业生产采用或普遍成功率。

### 3.1 案例总览

| 场景 | 公开运行证据 | 实际结果 | 证据边界 |
|---|---|---|---|
| 端到端软件交付 | oh-my-multica Webhook Inbox | 5 PR、5 节点 DAG、独立 Review/Acceptance；作者报告 86 tests、97.18% coverage、11/11 flows | 可复核演示，不是客户生产 |
| Dev→Review→Test | self-hosted #1998 | 三 Agent + sub-issue/comment handoff 可运行 | 正确性仍在 prompts |
| 八阶段 Squad | #5856 | TDD 到 PR 的串行 route 在使用 | 过密触发曾造成重复实现和重复 PR |
| 完成证据门 | #4804 Done-Gate | 240 transitions、69 历史 Issues、42 fake-evidence variants | 本地验证，未进入 core |
| MCP 多项目编排 | #1351 / multica-mcp | 作者称 daily 10+ Agents、周末 470+ Issues | 使用量自述，业务质量未审计 |
| 高频 Autopilot | #4276 | 523 run dirs/约 22GB，近 24h 新增 263 | 证明运行量和运维负担，不证明业务收益 |
| 外部系统轮询 | #2373 / #6202 | GitHub→Huly、PR 评论→revision Issue、PR reconciliation | 空轮询和确定性任务会浪费 token/受 quota 影响 |
| Slack 团队入口 | #4073 | Bridge→Autopilot/Hermes，验证发送和 thread/history read | 自定义共享 Bridge，不是 native 成品 |
| 多云 Runtime/handoff | #6150 | 两个腾讯云 Hermes Runtime，fresh task 成功并实际 handoff | session resume 可重复失败 |
| 分阶段写作 | #4929 | Leader→polish child→cre-writer，人工 rerun 完成 | 无公开最终文档，自动启动未成功 |
| 并发隔离对照 | #7326 | 三 Agent 并发身份污染；串行 rerun 正确完成 | 控制试验，无真实业务仓库 |

### 3.2 最强公开案例：oh-my-multica 端到端交付

[oh-my-multica](https://github.com/xiaohei-info/oh-my-multica) 在 Multica 的 Issue/Task/Runtime 之上增加确定性 delivery loop。其公开的 [Webhook Inbox demo](https://github.com/xiaohei-info/oh-my-multica-demo-webhook-inbox) 从一个带生产约束的需求开始：Planner/Orchestrator 生成五节点 DAG，三个较低成本 Runtime 负责主要实现，独立 Reviewer 复跑节点检查，Acceptor 从 HTTP API 边界验收集成后的 `main`。

公开 Git 历史能核对到 PR #2—#6 对应的五个合并提交。仓库还保存 Goal、DAG、acceptance document、CI、`acceptance.sh` 和 `verify_delivery.sh`；作者报告 86 tests、97.18% coverage、Python 3.10–3.13 CI、非 root container 与最终 11/11 flows。第一次最终验收实际只通过 2/11，因为验收文档仍指向 stub entrypoint；修正证据源后才从头重跑并返回 exit 0。

**能证明：** Multica 确实被用作多 Agent work item 与 Runtime 基础，完成了一次公开、可检查的设计→实现→Review→合并→验收闭环。

**不能证明：** 这是工具作者控制的演示仓库，没有生产用户/流量、独立客户或跨组织复现；本轮核对了仓库与 Git 产物，没有在本机重新执行全部测试。

### 3.3 多 Agent 软件交付与 Squad 路由

- [Issue #1998](https://github.com/multica-ai/multica/issues/1998)：用户搭起 self-hosted Developer→Reviewer→Tester，以 sub-issue 前向、父 Issue 评论反向反馈，并称可运行；正确性仍依赖各 Agent prompt。
- [Issue #5856](https://github.com/multica-ai/multica/issues/5856)：用户实际运行 TDD→Contrarian→Implementer→Verifier→Review→PR 等八阶段，由 leader 每轮读取 board 后串行派发。过密 trigger 曾让两个 Agent 对同一文件重复实现并关闭一个重复 PR，因此当前 workaround 仍是严格串行 route。
- [Issue #7326](https://github.com/multica-ai/multica/issues/7326)：Official Desktop 上同时分配 Product/Delivery、Tech Lead、QA 三个 Issue，公开 task IDs 显示并发任务共享 workdir 并污染身份 brief；串行 rerun 使用不同目录、身份正确且完成成功。

**判断：** Multica 的多 Agent 与 Squad 已被真实运行；同一批证据也说明“能触发多 Agent”不等于并发隔离、顺序和交付收敛得到保证。

### 3.4 Autopilot、事件集成与运行运营

- [Issue #4276](https://github.com/multica-ai/multica/issues/4276)：Official App/macOS 上高频 Autopilot 连续运行多日，单 workspace 有 523 个 run directories、约 22GB，其中 508 个带 `completed_at`；近 24 小时新增 263 个。它证明周期任务有真实运行量，也暴露 GC 与磁盘运营成本。
- [Issue #2373](https://github.com/multica-ai/multica/issues/2373)：`GitHub Issue Scan` Autopilot 定期把带 label 的 GitHub Issue 镜像到 Huly；`PR Comment Watch` 读取评论/review 后创建 revision sub-issues。两个 workaround 会在无事件时仍唤醒 Orchestrator。
- [Issue #6202](https://github.com/multica-ai/multica/issues/6202)：PR reconciliation 在 24 小时收到 132 个事件，可见 20 个 Agent run 中 8 个因 Claude quota 失败，而底层只是确定性对账。

**判断：** Autopilot 已被用于周期任务和外部系统连接；高频确定性步骤应改为 script/rule-first，只有 `ambiguous` 状态升级给 Agent。

### 3.5 Chat/MCP 与团队消息入口

- [Issue #1351](https://github.com/multica-ai/multica/issues/1351) 与 [multica-mcp](https://github.com/Korkyzer/multica-mcp)：外部 MCP bridge 提供 27 个工具，让 Claude/Codex 从 Chat 创建/查询 Issue、分派 Agent、查看 run/usage、管理 Autopilot。作者称日常编排 10+ Agents/多项目，一个周末创建 470+ Issues；实现和 smoke test 公开，但数量与业务质量未独立审计。
- [Issue #4073](https://github.com/multica-ai/multica/issues/4073)：团队运行 Slack Bridge→Hermes Autopilot，保留 permalink、channel、user 与 thread timestamp，并验证真实发送、history/thread read。该共享 Bridge 是 Skill/webhook/env 拼装；观察日 native Slack 仍是一 App 对一 Agent。

**判断：** Multica 已被实际放进“人所在的会话入口”；外部身份和普通频道消息不能直接获得高权限执行。

### 3.6 Runtime、多机器与自定义 Provider

- [Issue #6150](https://github.com/multica-ai/multica/issues/6150)：两个腾讯云 Linux 服务器分别运行 Multica remote Runtime，Hermes 连接火山引擎 Ark。fresh task 成功，同 Agent continuation 与 ProjectManager→Lead handoff 都被实际执行，但 resume 后会丢失 named provider。
- [Issue #1494](https://github.com/multica-ai/multica/issues/1494)：用户在 Mac/NAS 上测试多 daemon/Runtime，维护者确认同一 workspace 支持多个 daemon。
- [Issue #3262](https://github.com/multica-ai/multica/issues/3262)：同一 workspace 中 Codex smoke task 卡住后切换 Hermes rerun，38 秒/5 tools 完成并回写 `hermes-ok`。

**判断：** provider-neutral 和多 Runtime 不是纯能力宣称，已有真实部署/切换；session、daemon 版本与任务领取环境成为新的可靠性变量。

### 3.7 完成态、独立验证与“假绿”

- [Issue #4804](https://github.com/multica-ai/multica/issues/4804)：自托管用户构建 Done-Gate，本地验证包含 240 个 API 状态转换、69 个历史 done Issue、42 个伪证据变体、35 个 mutation checks 与 300 条 rollout metadata，明确把 `done` 当 evidence contract 而非 Agent 自报。
- [Issue #3933](https://github.com/multica-ai/multica/issues/3933)：Agent A 生成文件后切换 Agent B 检查，B 可能进入不同临时目录，说明“独立 Reviewer 已运行”也不自动证明检查了正确产物。
- [Issue #5233](https://github.com/multica-ai/multica/issues/5233)：两个 self-host OpenCode builder run 均被记录为 `completed,error:null`，但没有 commit、push、PR 或 completion comment，workdir 后被 GC。

**判断：** Task `completed`、Agent 自报和 Reviewer comment 都不是交付 Oracle；至少要读取同一 SHA/worktree、独立 CI/Scan、PR/commit 与验收产物。

### 3.8 非编码的分阶段工作

[Issue #4929](https://github.com/multica-ai/multica/issues/4929) 记录 self-host/local daemon 上的 Leader→stage 6 `polish` 子 Issue→`cre-writer` + `polish` Skill；自动启动没有发生，但操作者执行 `issue rerun` 后 task 被记录为成功完成。

**判断：** Multica 已被用于写作/润色类分阶段任务，但公开证据只到 Task 成功，没有最终文档和质量评审；不能从 Vision 进一步外推客服、营销或物理履约已成熟。

### 3.9 Multica 采用证据的最终边界

当前可以确认：Multica 已有公开可复核演示、持续运行的社区 workflow、量化本地验证和失败型实测，覆盖多 Agent 软件交付、Autopilot、外部系统、Chat/MCP、多 Runtime、写作和证据门。

当前仍不能确认：Multica 官方具名企业客户、独立第三方生产评估、SLA、安全认证、部署规模、长期任务成功率、质量改进或 ROI。官方 Tutorial、Vision、Feature Request、无运行证据的模板仍不进入真实采用结论。

时间边界：#805、#3933、#5233、#6150、#7326 等历史 Issue 已关闭、修复或不再计划处理。本文引用它们，是为了证明相关工作流曾被真实运行并暴露过边界，不是断言 v0.4.36 仍存在相同缺陷；当前版本状态需另行回归验证。

## 四、其他 Agent 平台的行业真实案例及对 Multica 的类比启示

> [!warning] 不属于 Multica 案例
> 本节的 GitHub Copilot、OpenAI Codex、GitLab Duo 与 Factory 案例都是真实行业使用，但**不是使用 Multica 的案例**。它们只用于回答：哪些任务形态、验证方法和治理边界已经在相邻平台被真实采用，Multica 试点可借鉴什么。

### 4.1 Issue→PR：GitHub Secret Protection

GitHub Secret Protection 团队先由人研究 token provider 的低侵入验证 endpoint，再由工作流创建包含背景、文档、样例和备注的详细 Issue，assign 给 Copilot coding agent。Agent 产出 PR，走自动测试、人审、darkship 观察，最后才正式 release。

官方称数周内新增近 90 种 token validator。更重要的是其失败边界：Agent 不能可靠选择 endpoint、跟随文档链接、创建/测试 live token 或解释真实 API 漂移。这些留给工程师。

**对 Multica 的启示：** Issue 必须是执行合同；把 research/decision 与 implementation 分开；Task 产出只进 Review；对外行为先 darkship/observe。

### 4.2 核心仓日常维护：GitHub dogfooding

GitHub 对核心仓一个月的 Agent PR 做分析，覆盖 UI/文档、feature flag 清理、跨仓重构、性能、生产 bug、flaky test、新 API、安全门禁、数据库迁移和代码库审计。

GitHub 同时明确 Agent PR 的合并率低于人类是预期：价值是让人不从零开始，有 first pass 可 review、iterate 或 close。

**对 Multica 的启示：** 首批任务不需要追求“全自动成功”，应衡量“是否快速得到可评审候选”。

### 4.3 大规模迁移：Asana + Codex

Asana 用五句话 prompt 启动最多四个并行 Agent，每个在独立代码副本工作，工程师每天检查两次并逐项 review，移除旧 Enzyme 测试系统。供应商案例称两周完成原计划五年的工作，模型与基础设施约 1.2 万美元。

**对 Multica 的启示：** Project 应拆成多个可独立 review 的 Issue；worktree 隔离是并行前提；简单一致的目标可能优于复杂角色编排；数字是单一案例，不可当计划基线。

### 4.4 系统级 Code Review：Datadog + Codex

Datadog 在一个大型高频仓库让 Codex 自动审每个 PR，并建立历史事故 replay harness：重建曾引发事故的 PR，让 Agent 重新 review，再由原事故负责人判断反馈是否会改变结果。公开案例称 10 余例、约 22% 被工程师判断会产生差异。

**对 Multica 的启示：** Reviewer Agent 最好的验证不是 comment 数，而是 golden PR、历史事故、误报/漏报、accepted feedback 和合并后信号。22% 不能写成未来事故下降率。

### 4.5 CI 失败解释：Barclays + GitLab Duo

Barclays 工程人员使用 GitLab Duo Troubleshooting 分析失败 job 日志；客户案例描述一次跨时区 pipeline 故障得到根因解释后解决。

**对 Multica 的启示：** CI triage 是高价值入口；但公开证据只证明解释，不证明 Agent 自动改代码、重跑、合并或上线。Multica 中应把原失败 Task、日志、候选 patch 和重跑结果分别记录。

### 4.6 夜间大规模分诊：AMD + GitHub Copilot

AMD 的客户案例称用 Copilot coding agent 处理每晚逾百万 pre-silicon simulation：分析日志、参照历史问题、提出修复，使人工晨间分诊降为零；另有历史漏洞 Autofix。

**对 Multica 的启示：** 大批量任务先以确定性规则聚合、去重和分类；Agent 处理歧义与候选修复；独立 verification 决定是否接受。

### 4.7 跨项目专业 Agent：Cube + GitLab Duo

Cube 在 2026 年运行 ticket refinement、上下文收集、设计复用和升级风险分类 Agent，共享 code、Issues、MR 和 pipeline history，并通过 MCP 向外部工具暴露同一上下文层。

**对 Multica 的启示：** Workspace-global Agent、Project resources 和 shared Skills 的方向与真实需求一致。但 Cube 是 GitLab Channel Partner，且案例没有公开每个 Agent 的权限、Review、merge 和生产变更细节，指标需要降权。

### 4.8 统一多模型与后台长任务：You.com + Factory

You.com 的供应商案例描述多模型选择、共享 Skills/MCP、长期后台 Mission、跨仓 Review 与复杂 bug，Factory 被纳入核心工程平台。

**对 Multica 的启示：** “不锁定一个 CLI、统一运行和规范”的市场需求真实存在；但平台采用不能替代外部权限与质量证据。

## 五、Multica 与业界模式的对应关系

| 业界成熟模式 | Multica 已有机制 | 当前缺口 |
|---|---|---|
| 结构化任务入口 | Issue、Project context、resources、attachments | Issue quality gate 与 Planner 确认需自行治理 |
| 多 Agent 并行 | 多 Agent/Runtime、worktree、Squad | 强依赖图、批量 Gate、跨任务事务/补偿不足 |
| 候选产物可审阅 | Issue timeline、transcript、PR card、in_review | 不可绕过的 merge/deploy approval 不在平台内 |
| 专业方法复用 | Instructions、workspace/repo Skills | 第三方 Skill 无签名/沙箱；版本治理需加强 |
| 周期与事件触发 | Autopilot schedule/webhook/manual | 高频确定性任务缺非 LLM execution primitive |
| 多渠道入口 | Slack/Lark/DingTalk/WeCom/Telegram | 外部身份、频道策略与一 Bot 多 Agent 仍需按集成核对 |
| 反馈与评估 | Task transcript、Issue comments、状态、成本 | golden cases、事故 replay、accepted/rejected 与质量指标需另建 |
| 安全执行 | Agent Access、task-scoped Multica token、worktree | 默认 OS/凭据/网络无 sandbox；真正边界必须外置 |

## 六、场景适配结论

### 6.1 强适配：现在就可试

#### 有界 Issue→draft PR

适合补测试、文档、简单 bug、feature flag 清理、重复重构和框架化 onboarding。要求：目标清晰、验收可写、现有 CI 完整、Agent 只能写候选分支。

#### 只读 Review 与周期巡检

Reviewer 只给发现和证据，不改代码；Autopilot 定期创建 Issue，结果由人决定。首轮甚至可只生成草稿，不直接发布 PR 评论。

#### 统一任务与运行历史

如果团队已经并用 Claude Code、Codex、Cursor 等，Multica 最直接价值是统一 assignee、status、transcript、成本和交接，不需要先自动化全流程。

### 6.2 条件适配：有 Gate 后再试

#### Planner→Dev→Review→Test

Planner 先提出一个 Milestone，人明确确认后才拆任务；初期由人触发专业 Agent。待 handoff、输出合同、失败恢复和 CI Gate 有数据后，再让 Squad leader 自动路由。

#### CI 分诊与候选修复

输入必须来自真实失败 run；输出 patch 后必须重跑原失败任务；“修绿”只说明进入评估，不能证明语义正确或可发布。

#### 大规模迁移与跨仓工作

使用 Project 拆 Issue、每 Task 独立 worktree、并发上限、批量 Review、回滚和 progress ledger；不要依赖单个超长 Task。

### 6.3 默认不适配

- 直接 merge、deploy、rollback 或操作生产；
- daemon 以个人用户运行并持有 SSH/云/生产凭据；
- 高频确定性 webhook、状态同步和 policy checks；
- 模糊产品发现、实时视觉判断和强交互探索；
- 对外发送消息、客户承诺、物理发货等后果不可逆流程；
- 需要法律、合规、安全或业务真相但没有独立 Oracle 的任务。

## 七、关键风险与控制建议

### 7.1 运行权限风险

[官方 Security Model](https://multica.ai/docs/security-model) 明确：默认 Task 可读取 daemon 用户能读的文件、使用其凭据并自由联网。Task-scoped `MULTICA_TOKEN` 只限制 Multica API 身份，不能限制 `gh`、SSH、AWS、kubectl 或本地文件。

**控制：** 专用 Unix 用户、容器或 VM；只挂载试点仓库；独立 deploy key；不提供生产 kubeconfig/云管理员权限；出网 allowlist；短期凭据。

### 7.2 流程正确性风险

Squad 依赖 leader 根据自然语言决定路由。系统 protocol 和去重能减少混乱，但不能证明每一步都执行、顺序正确、验证通过。

**控制：** 将阶段状态、进入/退出条件和 evidence contract 放在外部工作流或 Issue schema；每个 handoff 有结构化产物；失败时回到明确上一步；不把 comment 等同 Gate。

### 7.3 Skill 供应链风险

第三方 Skill 可带脚本和指令，Multica 不审核、签名或 sandbox。

**控制：** 固定来源与 commit/digest；代码审查；允许工具清单；禁用未知网络与 secret；Skill 更新走变更审计。

### 7.4 状态与真实结果不一致

Task `completed` 只表示进程正常结束；Issue `in_review/done` 也可能由 Agent 更新。

**控制：** 独立检查 PR SHA、CI status、scan artifact、review approval、merge SHA 和部署状态；状态更新必须带可回链证据。

### 7.5 成本与可用性风险

多 Provider 不等于任务在 quota、rate limit 或 runtime offline 时自动成功。PR 对账案例已显示确定性任务被模型 quota 拖垮。

**控制：** 按任务类型选 model；确定性步骤不用 LLM；对 queue、timeout、quota、retry 和 cost per accepted task 建指标。

## 八、推荐试点

### 8.1 首个 30 天试点

**范围：** 一个非生产仓库、一个 Coding Agent、一个只读 Reviewer、20–30 个低风险 Issue。

**运行：**

1. daemon 跑在专用用户/容器；
2. Git 只能建分支/PR，不能写 main 或 deploy；
3. Issue 模板要求 Goal、Scope、Acceptance、Evidence、Out-of-scope；
4. Coding Agent 只产 draft PR；
5. 独立 CI/Scan；
6. Reviewer 只读；
7. 人 review/merge；
8. 每周复盘 accepted、iterated、closed、返工、误报、失败、越权和成本。

**通过标准：** 无越权/secret；候选产物持续值得 review；人类上下文搬运与从零实现时间下降；质量不劣于基线；所有状态可回链到证据。

### 8.2 第二阶段才引入 Planner/Squad

Planner 首先只回答：目标、当前状态、Gap、优先级、一个 Milestone、验收、风险和未知。用户明确确认后再拆任务。初期 Planner 只提出协作请求，不直接触发其他 Agent。

当 10–20 个手工 handoff 都能稳定完成，才把同一模式固化为 Squad instructions；即使如此，CI、Review、merge 和 release Gate 仍在外部。

### 8.3 CI 场景的特殊门

若试点 CI 修复：

- admission 只接受真实失败 run；
- Agent patch 不是 Oracle；
- 必须重跑原失败 task，并补充相关回归测试；
- 冲突证据不足时返回“无法判断”；
- 自动 apply 只从格式、lint、同步等低风险任务开始；
- 发布权永不随“修绿”自动授予。

## 九、最终判断

### 是否值得关注

值得。多个 Agent 同时工作后，人的瓶颈会从“写代码”转向“分派、上下文、运行可见性、交接、Review 和决策”。Multica 正面解决这个新问题，而且 provider-neutral、本地 Runtime、Issue/Task 分层、Project context、Squad 和 Autopilot 的组合具有清晰产品逻辑。

### 是否可以直接作为全 SDLC 标准

不能由当前公开证据支持。Multica 自身的生产采用证据弱于 GitHub、GitLab、OpenAI、Factory 的具名案例；默认执行权限很大；严格工作流和平台级发布 Gate 也不足。

### 当前最合理的定位

> **让 Multica 管“意图如何进入、由谁执行、过程如何记录、候选产物如何交回”；让 Git、CI、安全、Policy、Review 与 CD 管“结果是否可信、是否允许合并、是否允许发布”。**

这一定位既能利用 Multica 的强项，也与其他 Agent 平台真实案例中反复出现的人类判断、独立验证和受控发布边界一致。

## 证据入口

- [[00_sources/research-multica-industry-real-use-cases-2026-08-29|一手证据底稿]]
- [[50_deepdives/multica-real-use-cases/20_evidence-map|Claim—Evidence—Gap 矩阵]]
- [[50_deepdives/multica-real-use-cases/30_case-map|案例地图]]
- [[50_deepdives/multica-real-use-cases/60_playbook|企业试点 Playbook]]
- [[50_deepdives/multica-real-use-cases/70_fact-audit|逐主张事实审计]]
