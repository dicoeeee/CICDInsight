---
title: Multica 与业界真实使用场景一手证据底稿
tags:
  - research/agentic-cicd
  - evidence/research-note
  - company/multica
  - topic/agent-orchestration
status: complete
as_of: 2026-08-29
confidence: mixed
---

# Multica 与业界真实使用场景一手证据底稿

> [!abstract] 研究口径
> 本底稿只回答“公开证据能证明什么”。访问日期均为 **2026-08-29**。未标发布日期的文档标为“未标注”。产品文档只能证明能力存在；官方教程只能证明官方演示；社区 Issue 只能证明提交者自述；厂商客户案例只能证明具名客户与厂商共同公开的口径，不能外推为行业平均值。

## 证据等级

| 等级 | 定义 | 可支持的结论 |
|---|---|---|
| E1 | 官方自用、公开运行记录或可核验的工程闭环 | 该组织确实按所述方式使用，但效果数字仍需按来源口径标注 |
| E2 | 具名客户案例，由供应商发布 | 可证明客户公开背书的使用方式；数字均为客户/厂商自述 |
| E3 | 社区用户公开自述、Issue 或 Discussion | 可证明存在该使用尝试或问题；不能证明普遍性、成功率或当前版本仍相同 |
| E4 | 官方文档、教程、示例、测试合同 | 只证明当前能力、设计或演示，不证明外部生产使用 |
| E5 | Feature Request、愿景或尚未完成的提案 | 只证明需求或方向，不进入“真实使用”结论 |

## 一、Multica 当前产品事实

| ID | 来源 | 日期 / 状态 | 可支持的精确主张 | 等级 |
|---|---|---|---|---|
| M-S01 | [Multica README](https://github.com/multica-ai/multica/blob/main/README.md) | main，访问 2026-08-29 | 官方将 Multica 定位为可自托管的 Agent 工作空间；Issue、Agent、Runtime、Task、Squad、Skill、Autopilot 共同构成协作层；当前 README 列出 23 种 Agent CLI | E4 |
| M-S02 | [v0.4.36 Release](https://github.com/multica-ai/multica/releases/tag/v0.4.36) | 2026-08-28 | 观察日前最新公开版本为 v0.4.36；仍处于 0.x 快速迭代阶段 | E4 |
| M-S03 | [How Multica works](https://multica.ai/docs/how-multica-works) | 未标注 | 服务端记录工作并调度，连接电脑上的 daemon 启动本地 Agent CLI；代码、CLI 凭据和命令执行在连接电脑一侧 | E4 |
| M-S04 | [Agents](https://multica.ai/docs/agents) | 未标注 | Agent 是长期身份与配置，不是常驻进程；一次具体执行由 Task 记录；`@mention` 是执行触发，不是普通通知 | E4 |
| M-S05 | [Tasks](https://multica.ai/docs/tasks) | 未标注 | 一个 Issue 可产生多个 Task；运行有队列、执行、失败、重试与 transcript；Task `completed` 只表示一次运行正常结束，不证明 Issue 目标达成 | E4 |
| M-S06 | [Squads](https://multica.ai/docs/squads) | 未标注 | Squad 先唤醒 leader，由 leader 用 `@mention` 路由成员；不会自动运行所有成员，也不会自动提高并发；`done` 留给人或既有集成 | E4 |
| M-S07 | [Projects](https://multica.ai/docs/projects)、[Project resources](https://multica.ai/docs/project-resources) | 未标注 | Project 将描述与资源注入后续任务；Git 仓可按 Task 建 worktree 并行；worktree 保护工作副本但不是安全沙箱 | E4 |
| M-S08 | [Autopilots](https://multica.ai/docs/autopilots) | 未标注 | Autopilot 支持 schedule、webhook、manual trigger，以及 create-issue / run-only；webhook 支持幂等键、签名和事件过滤 | E4 |
| M-S09 | [Security model](https://multica.ai/docs/security-model) | 未标注 | 默认 Task 拥有 daemon OS 用户的全部文件、凭据和网络权限；Multica 不保证 filesystem sandbox；官方建议专用 Unix 用户、容器或 VM | E4 |
| M-S10 | [Skills](https://multica.ai/docs/skills) | 未标注 | Skills 可在 Agent 间复用；第三方 Skill 不经 Multica 审查、签名或 sandbox，内容原样交给 Agent | E4 |
| M-S11 | [Multica License](https://github.com/multica-ai/multica/blob/main/LICENSE) | 未标注 | 许可证由 Apache-2.0 正文加附加条件构成；未经商业许可不得向第三方提供托管服务或嵌入商业产品，内部单组织使用不需要商业许可 | E4 |
| M-S12 | [Vision](https://github.com/multica-ai/multica/blob/main/VISION.md) | 未标注 | 文档明确说明它描述未来方向而非当前功能；研究、客服、内容等非软件场景因此不能仅凭 Vision 写成当前真实落地 | E5 |
| M-S13 | [Slack Bot](https://multica.ai/docs/slack-bot-integration) | 未标注 | 当前原生集成使用自有 Slack App，且一 App 映射一个 Multica Agent；DM、显式 mention 与 `/issue` 可进入 Multica | E4 |

## 二、Multica 公开使用、评估与需求证据

> 下表同时保留真实运行、内部评估和使用愿望，以便审计检索范围；只有带运行、日志、Task、Git/PR 或验收产物的条目进入报告第三节的“真实使用案例”。

| ID | 场景 | 公开证据 | 能证明什么 | 不能证明什么 | 等级 |
|---|---|---|---|---|---|
| M-C01 | 自托管 Dev → Reviewer → Tester | [Issue #1998](https://github.com/multica-ai/multica/issues/1998)，2026-05-02 | 提交者明确称已用一个 Agent 一个角色、sub-issue 前向交接、父 Issue 评论反向反馈搭起三 Agent 管道，并称“it works” | 正确性仍依赖 Agent prompt；当时缺状态触发、确定性 handoff 与声明式工作流；无质量/效率数字 | E3 |
| M-C02 | GitHub PR 对账 Autopilot | [Issue #6202](https://github.com/multica-ai/multica/issues/6202)，2026-07-30 | 一个自托管工作流 24 小时收到 132 个 PR 事件；可见历史有 20 次 Agent run，其中 8 次因 Claude quota 失败 | 该任务本质是确定性对账，反而证明高频确定性任务不宜强行走 LLM；不是成功的自治交付案例 | E3 |
| M-C03 | Slack → Multica → Agent 路由原型 | [Issue #4073](https://github.com/multica-ai/multica/issues/4073)，2026-06-12 | 提交者已运行 Slack Bridge → Hermes Autopilot，保留 thread/context 元数据，并验证真实 Slack 发送和历史读取 | 当时是 Skill + webhook/Autopilot + env 的拼装方案；当前原生 Slack 文档仍是一 App 对一 Agent，不等同共享桥接已完整产品化 | E3 |
| M-C04 | 评论触发的迭代修改 | [Issue #805](https://github.com/multica-ai/multica/issues/805)，2026-04-12，已关闭 | 用户在 v0.1.27/self-hosted/Claude local 上执行“首轮完成 → 评论追加要求 → 再运行”的实际流程，并提交可复现日志 | Bug 已关闭，不能当作当前缺陷；可作为多轮任务需要读取新上下文与独立验收的历史反例 | E3 |
| M-C05 | 多机器 / 多 Runtime 自托管测试 | [Issue #1494](https://github.com/multica-ai/multica/issues/1494)，2026-04-22 | 用户公开描述在 Mac 与 NAS 上运行两个 self-host 实例并测试多 daemon / Runtime；维护者确认同一 workspace 多 daemon 是支持的设计 | 不是企业规模或生产稳定性证据 | E3 |
| M-C06 | 组织内部评估 Multica 作为 SDLC 层 | [Issue #6227](https://github.com/multica-ai/multica/issues/6227)，2026-07-31 | 提交者称团队正在内部评估和推广 Multica，并提出按任务清晰度、交互密度、风险与验收能力分级采用 | 没有披露采用范围、交付结果或维护者正式答复；只能作为需求与决策问题 | E3/E5 |
| M-C07 | B2B 海外营销计划与跨人机交接 | [Issue #2517](https://github.com/multica-ai/multica/issues/2517)，2026-05-13 | 用户描述了 Claude 规划、Codex 插件开发、达人沟通与发样的当前人工流程，并询问如何拆 Agent | 是需求咨询，不是 Multica 成功案例；物理发样与外部沟通授权也未形成产品闭环 | E5 |
| M-C08 | 公开可复核的端到端多 Agent 软件交付 | [oh-my-multica](https://github.com/xiaohei-info/oh-my-multica)、[Webhook Inbox demo](https://github.com/xiaohei-info/oh-my-multica-demo-webhook-inbox)，访问 2026-08-29 | 外部项目使用 Multica work items 与 Coding Agent Runtimes，让 Planner/Orchestrator 规划 5 节点 DAG、Workers 并行实现、Reviewer/Acceptor 独立复核；公开 Git 历史可核对 PR #2—#6 的五个合并提交，仓库报告 86 tests、97.18% coverage、四个 Python 版本 CI 与最终 11/11 业务流 | 是作者控制的可复核演示交付，不是独立客户或生产流量案例；测试数字本轮未在本机重新执行 | E3，public artifacts |
| M-C09 | `done` 状态的证据门 | [Issue #4804](https://github.com/multica-ai/multica/issues/4804)，2026-07-01 | 用户明确称自托管 Multica 并构建 Done-Gate 本地原型；公开 240 个 API 状态转换、69 个历史 done Issue 回放、42 个伪证据变体、35 个 mutation checks 与 300 条 rollout metadata | 本地验证，不是生产采用或维护者认可；原型尚未进入 Multica core | E3，local validation |
| M-C10 | Manager Agent + Skills 路由配置 | [Discussion #2099](https://github.com/multica-ai/multica/discussions/2099)、[workflow template](https://github.com/wksudud/multica-agent-workflow-template)，2026-05-05 | 作者称持续试验 Multica 多 Agent 配置，并公开“实际适合自己”的 7-Agent case、Manager Issue routing、CLI dispatch 与由 health check 到完整 dispatch chain 的测试步骤 | 模板和作者自述，无独立运行日志、质量或成本数据；不能当通用最佳 Agent 数量 | E3，community self-report |
| M-C11 | GitHub/Huly 与 PR 评论的轮询集成 | [Issue #2373](https://github.com/multica-ai/multica/issues/2373)，2026-05-10 | 提交者明确列出两个已运行的 Autopilot：`GitHub Issue Scan` 定期读取 label 后镜像到 Huly，`PR Comment Watch` 读取评论/review 后创建 revision sub-issues | 是为说明 webhook 缺口而披露的 workaround；轮询会在无事件时也唤醒 Orchestrator、消耗 token | E3，operational workaround |
| M-C12 | 双云端 Runtime + 自定义模型 + 跨 Agent handoff | [Issue #6150](https://github.com/multica-ai/multica/issues/6150)，2026-07-29，已关闭 | 用户在两个腾讯云 Linux 服务器运行 Multica remote Runtime，Hermes 连接火山引擎 Ark；fresh Task 可成功，并复现 same-Agent continuation 与 ProjectManager→Lead handoff | 第二轮 session resume 会丢失 named provider，属于失败型实测；证明部署和交接被实际运行，不证明其稳定 | E3，reproduced failure |
| M-C13 | Claude/Codex 通过 MCP 操作 Multica | [Issue #1351](https://github.com/multica-ai/multica/issues/1351)、[Korkyzer/multica-mcp](https://github.com/Korkyzer/multica-mcp)，2026-04-19 | 社区桥接提供 27 个 Issue、comment、run、Agent、Runtime、usage、Autopilot 等 MCP tools；作者称日常编排 10+ Agents/多项目，一个周末创建 470+ Issues；README 说明 Claude/Codex 配置已在本地应用并提供 smoke test | 使用量是作者自述，470 个 Issue 未逐条审计；不能证明业务交付质量或企业生产部署 | E3，working integration + usage claim |
| M-C14 | 生成 Agent→验证 Agent 的工作目录连续性 | [Issue #3933](https://github.com/multica-ai/multica/issues/3933)，2026-06-09，closed as not planned | 用户实际执行“Agent A 生成文件→切换 Agent B 检查”的专业分工，并给出复现步骤 | 检查 Agent 可能进入不同临时目录；这是失败型用例，说明共享 Issue/会话不自动证明验证对象一致 | E3，reproduced failure |
| M-C15 | 高频 Autopilot 的本机运营 | [Issue #4276](https://github.com/multica-ai/multica/issues/4276)，2026-06-17 | Official App v0.3.24/macOS 上持续运行高频 Autopilot 多日；单 workspace 累积 523 个 run directories、约 22 GB，其中 508 个有 `completed_at`，近 24 小时新增 263 个 | 未公开 Autopilot 的业务任务或收益；主要证明高频真实运行与磁盘/GC 运维负担 | E3，operating evidence |
| M-C16 | 八阶段 Squad 软件交付循环 | [Issue #5856](https://github.com/multica-ai/multica/issues/5856)，2026-07-23 | 用户运行 TDD→Contrarian→Implementer→Verifier→Review→PR 等八阶段，由 leader 读 board 后顺序路由；该 workaround 仍在使用 | 过密 trigger 曾让两个 Agent 重复实现同一文件并关闭一个重复 PR；不能写成稳定的自动流水线 | E3，operational workflow + failure |
| M-C17 | 分阶段写作/润色子 Issue | [Issue #4929](https://github.com/multica-ai/multica/issues/4929)，2026-07-04 | self-host/local daemon 上，Leader 创建 stage 6 `polish` 子 Issue GXG-48，绑定 `cre-writer` 与 `polish` Skill；人工 `issue rerun` 后 task 被记录为成功完成 | 没有公开最终文档或质量评审；自动启动未发生，证明人工恢复而非自动 handoff 稳定 | E3，staged task |
| M-C18 | 多 Agent 并发与串行恢复对照 | [Issue #7326](https://github.com/multica-ai/multica/issues/7326)，2026-08-20 | Official Desktop v0.4.30 同时分配 Product/Delivery、Tech Lead、QA 三个 Issue；公开 task IDs/同一路径与 identity brief 污染；串行 rerun 后目录、身份正确且均成功完成 | 是无业务仓库的控制试验；证明当时并发隔离故障与串行 workaround，不证明真实交付 | E3，controlled reproduction |
| M-C19 | Builder Task 的“平台完成、交付未发生” | [Issue #5233](https://github.com/multica-ai/multica/issues/5233)，2026-07-10 | self-host v0.3.42 + OpenCode 的两个真实 builder run 各有 75/97 messages，平台均记录 `completed,error:null` | 无 commit/push/PR/completion comment，workdir 后被 GC；这是反例，不能当成功案例，说明 Task completed 不是交付 Oracle | E3，false-positive completion |
| M-C20 | 同一 Workspace 切换 Agent Runtime | [Issue #3262](https://github.com/multica-ai/multica/issues/3262)，2026-05-29 | Hosted Desktop 上 Codex 的最小任务卡住后，操作者将同一 Agent 切换为 Hermes 重跑；公开记录显示 38 秒、5 次 tool calls 后完成并回写 `hermes-ok`，没有文件变更 | 只是窄 smoke test；能证明 Runtime 切换被实际操作，不能证明真实代码任务、长期稳定性或跨 Agent handoff | E3，smoke test |

### Multica 证据结论

1. 已有公开证据证明 Multica 被用于**端到端多 Agent 演示交付、自托管角色交接、完成证据门、外部系统轮询、PR 对账、消息入口、多云 Runtime、Runtime 切换和 MCP 桥接**。
2. 其中最强的是带公开 Git/PR/测试/验收产物的 Webhook Inbox，但它仍是作者控制的演示；其余多为社区自述、原型或失败型实测。
3. 未检索到 Multica 官方发布的具名客户案例、SLA、企业安全认证、生产规模、质量改进或 ROI 数据。
4. 因此可以说“Multica 已有多种真实运行和可复核实验”，不能说“Multica 已在企业生产中广泛验证”。

## 三、其他 Agent 平台的行业真实案例（非 Multica）

> 本节所有案例都不是 Multica 客户案例，只用于检验哪些工作单元、验证门和治理模式已经在相邻平台真实发生。

| ID | 组织 / 产品 | 真实工作流 | 公开结果 | 关键控制边界 | 等级 |
|---|---|---|---|---|---|
| I-C01 | GitHub Secret Protection / Copilot coding agent | 人先研究 token 验证 endpoint，将背景、文档、样例写入结构化 Issue；Agent 产出 PR；自动测试、人审、darkship 观察后再正式启用 | 2025 年数周内新增近 90 种 token validator；官方自用 | Agent 对链接、真实 API 漂移、live token 测试不可靠；研究、review 与 darkship 留给人 | E1，[来源](https://github.blog/ai-and-ml/github-copilot/how-we-accelerated-secret-protection-engineering-with-copilot/)，2025-08-28，更新 2025-08-29 |
| I-C02 | GitHub 核心仓库 / Copilot coding agent | 人分配 Issue；Agent 开 PR；用于 UI/文档、feature flag 清理、重构、性能、生产 bug、flaky test、新 API、安全门禁、数据库迁移与代码审计 | 官方分析了核心仓一个月的 Agent PR | 合并率低于人类属预期；价值是提供可批判的 first pass，不是盲目合并 | E1，[来源](https://github.blog/ai-and-ml/github-copilot/how-copilot-helps-build-the-github-platform/)，2025-11-12 |
| I-C03 | Asana / OpenAI Codex | 从五句话 prompt 启动最多四个并行 Agent，每个独立代码副本；工程师每日两次检查并逐项 review，完成 Enzyme 测试系统迁移 | 厂商案例称两周完成原计划五年的工作，模型与基础设施约 1.2 万美元 | 单一迁移项目；数字来自客户/厂商自述，不能外推；人仍逐项审查 | E2，[来源](https://openai.com/index/asana/)，2026-08-18 |
| I-C04 | Datadog / OpenAI Codex | 在大型高频仓库自动审每个 PR；以历史事故 PR 回放，再由事故负责人判断 Agent 反馈是否有用 | 10 余例、约 22% 的回放事故被工程师判断“反馈会产生差异” | 是反事实回放判断，不是生产事故下降率；Agent review 只补充人审 | E2，[来源](https://openai.com/index/datadog/)，2026-01-09 |
| I-C05 | Cube / GitLab Duo Agent Platform | 生产运行 refinement、上下文收集、设计复用、跨项目升级风险分类 Agent；共享 Issues、MR、pipeline history 与 MCP 上下文 | 厂商案例称项目启动快 50%、每日部署 5 倍、ROI 400% | Cube 同时是 GitLab Channel Partner，应降权；未披露每个 Agent 的权限、合并与人审细节 | E2，[来源](https://about.gitlab.com/customers/cube/)，发布日期未标注 |
| I-C06 | Barclays / GitLab Duo | 工程人员使用 Troubleshooting 分析失败 job 日志；一次跨时区 pipeline 故障获得根因解释后解决 | 案例称问题在数秒内解决；Duo 从 6,000 席增长至 23,000 活跃成员 | 只证明失败诊断/解释，不证明 Agent 自动改代码、重跑 CI 或上线 | E2，[来源](https://about.gitlab.com/customers/barclays-plc/)，发布日期未标注 |
| I-C07 | AMD / GitHub Copilot | 夜间 pre-silicon simulation 日志分诊，结合历史问题提出候选修复；另有历史漏洞 Autofix | 厂商案例称每晚超过 100 万 simulation 自动分诊、早晨人工分诊降为零，约 70% vulnerability classes 自动解决 | 数字为客户/厂商自述；“提出修复”与“独立验证、合并、部署”不可混写 | E2，[来源](https://github.com/customer-stories/amd)，发布日期未标注 |
| I-C08 | You.com / Factory | 统一多模型/多工具，长期后台 Mission、复杂 bug、跨仓 review、共享 Skills 与 MCP 标准 | Factory 称已进入 You.com 核心工程平台并 24/7 后台运行 | 供应商案例，无独立审计；具体授权、门禁与失败率未披露 | E2，[来源](https://factory.ai/case-studies/you-com)，2026-06-16 |
| I-C09 | OpenAI 内部 Agent-first 产品 / Codex | 以短入口文档、版本化知识库、execution plan、worktree、机械 lint/CI 和多 Agent review 建设内部 beta | 官方称约 100 万行、约 1,500 个 PR、三人起步；产品有内部日活和外部 alpha | 官方自用实验；依赖大量仓库工程、可观测性和人类 steering，不能复制成“无人治理开发” | E1，[来源](https://openai.com/index/harness-engineering/)，2026 年，页面未标月日 |

## 四、证据冲突与处理

### “Review gate”是否为平台强制门禁

- README 写“nothing ships without a human saying so”，教程也把人工 sign-off 作为流程；
- 但 Security Model 明确 Task 默认继承 daemon 用户权限，并可使用其 `gh`、SSH、云 CLI 与网络；Multica 不提供 filesystem sandbox；
- **处理：** 报告将“review gate”写成产品流程意图，不写成不可绕过的安全控制。真正的 merge/deploy 权限必须在 Git host、CI/CD、凭据、环境审批和 policy 层强制。

### Squad 是否等于确定性工作流引擎

- 当前文档已有系统管理的 leader protocol、去重与唤醒规则；
- 社区 [Issue #1943](https://github.com/multica-ai/multica/issues/1943) 与 [Issue #5972](https://github.com/multica-ai/multica/issues/5972) 同时指出严格的步骤、条件、重试和状态机仍不足；维护者在 #1943 明确说先交付开放式 Squad，固定 workflow 仍在 roadmap；
- **处理：** Squad 被定义为“基于 leader 的动态路由层”，不等于声明式 DAG/BPMN/强状态机。

### 官方称“open-source”与许可证附加条件

- README 使用 open-source 表述；
- LICENSE 在 Apache-2.0 正文上增加托管服务、商业嵌入和品牌条件；
- **处理：** 报告不进行法律定性，只准确写“源代码公开、可自托管，采用带附加条件的 Multica License；对外托管/商业嵌入需单独核对许可”。

## 五、负面搜索与缺口

截至 2026-08-29，在 Multica 官网、官方仓库、官方文档、Release、公开 Issues/Discussions 范围内：

- 未找到具名客户案例、独立第三方生产评估、SLA、SOC 2 / ISO 27001 等认证证据；
- 未找到 Multica 自身对交付成功率、合并率、缺陷率、返工率或事故率的公开统计；
- 未找到严格声明式工作流引擎已完成的证据；
- 未找到平台层不可绕过的 merge/deploy 人工审批机制；
- 未找到 Task 默认安全 sandbox 的证据，官方文档反而明确默认按 daemon 用户权限运行。

这些只是本轮公开材料中的证据缺口，不证明私有客户、未公开认证或后续实现不存在。
