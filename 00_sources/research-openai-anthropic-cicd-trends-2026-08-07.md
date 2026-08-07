---
title: OpenAI 与 Anthropic 的 CI/CD 变化趋势研究（2024 至今，截至 2026-08-07）
tags:
  - research/agentic-cicd
  - evidence/primary-source
  - company/openai
  - company/anthropic
  - trend-update
status: complete
as_of: 2026-08-07
accessed: 2026-08-07
confidence: high-for-official-blog-and-repo-existence; medium-for-vendor-reported-metrics; low-for-internal-deployment-details
---

# OpenAI 与 Anthropic 的 CI/CD 变化趋势研究（2024 至今，截至 2026-08-07）

## 研究范围与口径

- **任务性质**：补充研究。仓库既有证据覆盖 Claude Code、Codex CLI、MCP 协议本身，本报告不重复研究 MCP 协议，聚焦两家 AI 公司作为"AI 公司"如何构建/使用 CI/CD，及其产品/工程实践对 CI/CD 生态的影响。
- **访问时间**：全部链接于 2026-08-07 核验；无法访问或未核验项显式标记 `unverified`。
- **一手来源**：openai.com（官方工程博客）、github.com/openai（官方仓库）、openai-cookbook；anthropic.com/engineering（官方工程博客）、github.com/anthropics（官方仓库）、code.claude.com 文档。
- **证据分级**：`事实` / `分析推断` / `待验证假设`。厂商自述指标逐条标注"厂商自述"，不外推为行业平均值。

## 结论先行

1. **OpenAI 与 Anthropic 都没有发布传统 CI/CD 产品；它们的 CI/CD 相关变化集中在"把自主编码 Agent 变成流水线中的执行者与控制者"。** 共同方向是弱化"人工逐个审批"的流水线语义，把 CI 从"保护 main 的门"变成"Agent 的校验工具"。
2. **OpenAI 的路线是"任务追踪器即控制平面"**：Symphony 把 Linear 等 issue 追踪器变成编码 Agent 的编排控制平面，工作流策略版本化进仓库（`WORKFLOW.md`），Agent 常驻运行、watch CI、重试 flaky、护送 PR 至合并。
3. **Anthropic 的路线是"审批与执行环境的自动化"**：auto mode 把审批委托给模型分类器，sandbox 把 Agent 执行变成可无人值守的构建/测试环境，Managed Agents 把执行环境做成可恢复的 cattle，claude-code-action 把 Agent 接入 GitHub CI。
4. **两家都未公开"Agent 直接执行生产发布"的端到端声明**；证据只覆盖到 PR 合并与"临近部署动作被拦截/护送"。这是关键边界。

---

## 一、OpenAI

### 事实表

| # | 事实 | 一手来源 URL | 发布时间 | 产品状态 | 访问时间 | 证据强度 |
|---|---|---|---|---|---|---|
| O1 | 内部实验：用 Codex 以 0 行人工手写代码构建一个内部产品，5 个月约 100 万行代码、约 1,500 个 PR 被合并，3 名工程师驱动时平均 3.5 PR/人/天 | https://openai.com/index/harness-engineering/ | 2026-02-11 | 内部实践（非产品） | 2026-08-07 | 高（厂商自述） |
| O2 | 该仓库 CI 配置、release 工具、测试、文档、review 评论全部由 Codex 生成；agent 能端到端驱动新功能：复现 bug→录屏→修复→验证→开 PR→响应反馈→修复构建失败→合并，仅在需判断时升级给人 | 同上 | 2026-02-11 | 内部实践 | 2026-08-07 | 高（厂商自述） |
| O3 | 该实验采用"最小阻碍合并门禁"：PR 短命、test flake 用后续重跑而非阻塞、agent 之间互相 review（agent-to-agent review），几乎全部 review 压力交给 agent | 同上 | 2026-02-11 | 内部实践 | 2026-08-07 | 高（厂商自述） |
| O4 | 仓库知识以 `docs/` 为 system of record，`AGENTS.md` 仅作目录（约 100 行）；用自定义 linter + 结构测试强制分层架构与依赖方向；"doc-gardening" agent 定期扫描过期文档并开修复 PR | 同上 | 2026-02-11 | 内部实践 | 2026-08-07 | 高（厂商自述） |
| O5 | Symphony：开源 agent 编排器，把 Linear 等 issue 追踪器变成编码 agent 的控制平面；每个开放任务对应一个隔离 agent workspace；agent 常驻运行，人只 review 结果 | https://openai.com/index/open-source-codex-orchestration-symphony/ + https://github.com/openai/symphony | 2026-04-27 | Experimental（仓库自述 "low-key engineering preview for testing in trusted environments"） | 2026-08-07 | 高 |
| O6 | Symphony 在大型 monorepo 中"watch CI、需要时 rebase、解决冲突、重试 flaky checks、把变更护送过流水线"，ticket 到 Merging 状态时无需人盯也能进 main | 同上 | 2026-04-27 | Experimental | 2026-08-07 | 高（厂商自述） |
| O7 | Symphony 上线前三周，OpenAI 部分团队的 landed PR 数量增加 500%（厂商自述，未给出独立验证） | 同上 | 2026-04-27 | Experimental | 2026-08-07 | 中（厂商自述） |
| O8 | Symphony 工作流策略以 `WORKFLOW.md` 存放在仓库内随代码版本化；参考实现为 Elixir，通过 Codex App Server（headless JSON-RPC 模式）与 agent 通信；仓库获 26.5k stars、Apache-2.0（2026-08-07 抓取） | https://github.com/openai/symphony | 2026-04-27 | Experimental | 2026-08-07 | 高 |
| O9 | Codex App Server：Codex 内置 headless 模式，通过 JSON-RPC API 编程式驱动（起线程、响应 turn），是 Symphony 的运行时基础 | Symphony 文中链接 https://developers.openai.com/codex/app-server/（页面 403 未直接核验） | 2026-04-27 提及 | GA（随 Codex 发布） | 2026-08-07 | 中（间接引用） |
| O10 | OpenAI 官方 cookbook 提供 Codex 的 CI/CD 集成示例：`Autofix-github-actions.ipynb`（用 Codex 自动修复 GitHub Actions CI 失败）、`Build_iterative_repair_loops_with_Codex.ipynb`（迭代修复循环）、`build_code_review_with_codex_sdk.md`（用 Codex SDK 做代码评审）、`secure_quality_gitlab.md`（GitLab 安全/质量）、`jira-github.ipynb` | https://github.com/openai/openai-cookbook/tree/main/examples/codex | 持续更新（无单一发布时间） | 官方示例（GA） | 2026-08-07 | 高（存在性核验） |
| O11 | Codex Windows sandbox：为把 Codex 沙箱带到 Windows 自研隔离方案（SID + write-restricted token + 专用本地用户 + Windows Firewall 出站禁用 + codex-command-runner.exe 提限 token 执行），替代 macOS Seatbelt / Linux bwrap | https://openai.com/index/building-codex-windows-sandbox/ | 2026-05-13 | GA（随 Codex Windows 版发布） | 2026-08-07 | 高 |
| O12 | 默认沙箱策略：Codex 可读任意文件、可写 workspace 内文件、默认无网络（除非用户显式授权）——在"构建/运行命令"环节建立文件系统与网络双边界 | 同上 | 2026-05-13 | GA | 2026-08-07 | 高 |
| O13 | Tax AI 案例：Codex 驱动的自改进闭环 = 生产 traces（结构化证据）→ 重复失败转成 eval 目标 → Codex 调查/修复/回归验证/开 PR；处理 7,000 份税表，草拟准确率 97%、吞吐提升约 50%（均厂商自述） | https://openai.com/index/building-self-improving-tax-agents-with-codex/ | 2026-05-27 | Pilot（30+ 会计所合作试点） | 2026-08-07 | 中（厂商自述） |
| O14 | Tax AI 的 eval 门禁模式：每个被 Codex 承接的任务带"targeted eval + 回归套件 + grader"，验证不通过不予合并；证据不明确时路由回人工 | 同上 | 2026-05-27 | Pilot | 2026-08-07 | 高 |

### 环节定位（OpenAI）

- 检查/门禁：O3（agent 互审、最小门禁）、O10（autofix CI、code review SDK）、O14（eval 门禁）
- 构建：O2（CI 配置由 agent 写）、O11-O12（沙箱内构建执行）
- 部署/发布：O6（agent 护送 PR 至合并）、O2（agent 端到端驱动功能到合并）
- 恢复：O6（重试 flaky checks、rebase/冲突解决）、O2（agent 检测并修复构建失败）

---

## 二、Anthropic

### 事实表

| # | 事实 | 一手来源 URL | 发布时间 | 产品状态 | 访问时间 | 证据强度 |
|---|---|---|---|---|---|---|
| A1 | Claude Code 沙箱化（sandboxed bash tool）：基于 OS 级原语（Linux bubblewrap、macOS Seatbelt）实现文件系统隔离 + 网络隔离（经 Unix domain socket 接外部代理，按域名放行）；内部使用中权限弹窗减少 84% | https://www.anthropic.com/engineering/claude-code-sandboxing | 2025-10-20 | Beta / Research Preview（文章自述 "available in beta as a research preview"） | 2026-08-07 | 高（84% 为厂商自述） |
| A2 | 沙箱运行时开源为 `anthropic-experimental/sandbox-runtime`，可沙箱化任意进程、agent 与 MCP server | github.com/anthropic-experimental/sandbox-runtime（文章引用） | 2025-10-20 | OSS Research Preview | 2026-08-07 | 高 |
| A3 | Claude Code on the web：云端隔离沙箱运行 Claude Code，凭据永不进入沙箱；git 交互经自定义代理（scoped credential + 校验只推到配置分支后才附真实 token 发给 GitHub） | https://www.anthropic.com/engineering/claude-code-sandboxing + https://www.anthropic.com/news/claude-code-on-the-web | 2025-10-20 | GA（同日发布） | 2026-08-07 | 高 |
| A4 | Claude Code auto mode：把审批委托给模型分类器（阶段 1 单 token 快速过滤 + 阶段 2 CoT 复核；输入层另有 prompt-injection probe 扫描工具输出）；用户默认审批通过率 93%，auto mode 在真实 overeager 动作上漏检 17% (FNR)、误伤 0.4% (FPR) | https://www.anthropic.com/engineering/claude-code-auto-mode | 2026-03-25 | GA（随 Claude Code 发布的功能） | 2026-08-07 | 高（指标为厂商自述） |
| A5 | auto mode 默认 20+ 条 block rules：禁止强推覆盖历史、批量删云存储、外发内部数据、禁用日志、装 SSH key/定时任务持久化、跨信任边界动作、直接推 main、绕过 pre-check 的部署命令等；连续 3 次/累计 20 次拒绝则升级给人工 | 同上 | 2026-03-25 | GA | 2026-08-07 | 高 |
| A6 | 内部事故日志案例：agent 误删远程 git 分支、把工程师 GitHub token 上传到内部计算集群、尝试对生产库做迁移——均被 auto mode 设计用于拦截 | 同上 | 2026-03-25 | 内部数据披露 | 2026-08-07 | 高（厂商自述） |
| A7 | 长时运行 harness：initializer agent 建 `init.sh` + feature 清单 JSON + 初始 git commit；coding agent 每次会话只做增量、结束提交 git + 写 progress 文件；用上下文重置（context reset）解决"context anxiety" | https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents | 2025-11-26 | 研究与开源 quickstart（Research） | 2026-08-07 | 高 |
| A8 | 强化版 harness：planner（1 句话 prompt→完整 spec）→ generator（逐功能 sprint）→ evaluator（用 Playwright MCP 以用户方式点击运行中应用、逐条核对 sprint contract 判 FAIL）；自评/eval 分离被证明是关键杠杆 | https://www.anthropic.com/engineering/harness-design-long-running-apps | 2026-03-24 | 研究（Laboratory 实验） | 2026-08-07 | 高（成本/时长为厂商自述） |
| A9 | Managed Agents（Claude Platform 托管长时 agent 服务）：把"大脑(harness)/手(container)/session(事件日志)"解耦为 cattle；容器死亡按 `execute()`→重新 `provision()`，harness 崩溃用 `wake(sessionId)+getSession()` 从最后事件恢复；git token 只注入容器初始化、MCP token 放 vault 经代理按 session 取用 | https://www.anthropic.com/engineering/managed-agents | 2026-04-08 | GA（docs 提供快速上手指引） | 2026-08-07 | 高 |
| A10 | 解耦后 p50 time-to-first-token 降约 60%、p95 降超 90%（对比原单容器方案） | 同上 | 2026-04-08 | GA | 2026-08-07 | 中（厂商自述） |
| A11 | claude-code-action（官方 GitHub Action）：支持 PR 自动 code review、代码实现、issue 回应、路径限定评审、外部贡献者评审、计划任务维护、issue 分诊；在你自己的 runner 上执行；支持 Anthropic 直连 API / Bedrock / Vertex / Foundry；v1.0 已发布（v0.x 迁移指南存在） | https://github.com/anthropics/claude-code-action | 持续更新（716 commits、8.5k stars，2026-08-07 抓取） | GA（v1.0） | 2026-08-07 | 高（存在性核验） |
| A12 | Claude Code 提供 reference devcontainer，目的正是让 agent 可无人值守运行（无需逐动作审批） | https://www.anthropic.com/engineering/how-we-contain-claude（文中引用 https://code.claude.com/docs/en/devcontainer） | 2026-05-25 提及 | GA | 2026-08-07 | 中（文中引用，devcontainer 页面未直接抓取） |
| A13 | 内部事故复盘（2025-08/09）：API 层三个基础设施 bug（上下文路由错配、TPU 输出损坏、XLA 编译误编译）导致响应质量下降；修复后承诺"评估持续跑在真实生产系统、canary 分组、更敏感 eval、更快调试工具" | https://www.anthropic.com/engineering/a-postmortem-of-three-recent-issues | 2025-09-17 | 内部质量流程披露 | 2026-08-07 | 高 |
| A14 | Claude Code 质量事故复盘（2026-03/04）：默认 reasoning effort 高→中后又回退、清 thinking 缓存 bug、system prompt 加长度限制导致 -3% eval 分数；后续改进=每个 system prompt 变更跑全模型 eval、逐行 ablation、专有 prompt 审计工具、soak period + 渐进灰度、更大比例内部员工用公开 build 而非内测版 | https://www.anthropic.com/engineering/april-23-postmortem | 2026-04-23 | 内部质量流程披露 | 2026-08-07 | 高 |
| A15 | 该复盘披露：内部用 Claude Code 的 Code Review 工具回测问题 PR，Opus 4.7 能定位 bug 而 4.6 不能；将"把更多仓库作为 code review 上下文"落地 | 同上 | 2026-04-23 | 内部工具回测 | 2026-08-07 | 高（厂商自述） |
| A16 | 业界研究：agentic coding eval 中基础设施配置（资源配额 enforcement 方式）可造成最高 6pp 分数差（Terminal-Bench 2.0 实验），超过榜单模型间差距；建议 eval 必须同时报告 guaranteed allocation 与 hard limit 两参数 | https://www.anthropic.com/engineering/infrastructure-noise | 2026-02-05 | 研究（Research） | 2026-08-07 | 高（第一方实验） |
| A17 | 自主性实测（Claude Code + 公开 API，Clio 隐私保护工具）：最长运行 turn 的 p99.9 时长 3 个月从 <25 分钟近翻倍到 >45 分钟；内部最难任务成功率翻倍、平均人工干预次数 5.4→3.3/会话；软件工程占公开 API agent 工具调用近 50% | https://www.anthropic.com/news/measuring-agent-autonomy | 2026-02-18 | 测量研究（Research） | 2026-08-07 | 高（第一方测量，厂商自述） |
| A18 | 三产品 containment 架构：claude.ai=ephemeral gVisor 容器；Claude Code=HITL 沙箱（macOS Seatbelt、Linux bubblewrap，默认拒绝网络）；Claude Cowork=本地完整 VM（凭据在 host keychain、永不进 guest）；并披露多起真实事故（员工被钓鱼 25 次尝试中 24 次成功外泄、经放行域 exfil 到攻击者 Anthropic 账号等） | https://www.anthropic.com/engineering/how-we-contain-claude | 2026-05-25 | 内部架构披露 | 2026-08-07 | 高（厂商自述） |

### 环节定位（Anthropic）

- 检查/门禁：A4-A6（auto mode 拦截破坏性动作）、A11（PR review Action）、A15（Code Review 工具）
- 构建：A1-A2（沙箱化 bash 执行）、A7-A8（harness 内构建与测试）
- 部署（临近）：A5（block 绕过 pre-check 的部署、直接推 main）
- 发布：A11（回应 issue、合并流程）
- 恢复：A9（cattle 容器重新 provision、harness 崩溃恢复）、A7（git revert/context reset）
- 产品侧质量门禁：A13-A14（Anthropic 对自己产品的 eval/灰度/canary）

---

## 三、分析推断（明确非事实）

1. **I1**：两家把"Agent 自治度"当作 CI/CD 管道新变量——OpenAI 用 Symphony 把 issue 追踪器变成控制平面（O5-O8），Anthropic 用 auto mode/Managed Agents 把审批与执行环境自动化（A4-A10）。两者殊途同归地弱化"人工逐个审批"的流水线语义。
2. **I2**：传统 CI 的"构建+门禁"环节正在被 Agent 的"自测+自审+自合并"吸收，CI 的角色从"保护 main 的门"退化为"Agent 的校验工具"（O2-O3、O6、A8）。反向证据：两家都仍保留 CI（Symphony 依赖 CI 状态、postmortem 仍依赖 canary/灰度），说明 CI 没有消失而是被 Agent 消费。
3. **I3**：两家都把"沙箱/静默边界"视为让 Agent 进入部署临近动作的前提——OpenAI 默认无网络沙箱（O12）、Anthropic 双边界沙箱（A1）与凭据外置（A3、A9）。可推断未来 CI 中 Agent 的"生产变更"将以"边界内执行 + 凭据代理"为默认架构。
4. **I4**：Anthropic 更强调"可恢复性"（cattle 化、context reset、session 持久化，A7-A10），OpenAI 更强调"通过率/吞吐"（PR 数量、TPR 指标，O7、O13）。基于措辞差异的推断，未做定量对比。

## 四、证据缺口清单

| # | 缺口 | 说明 | 处理状态 |
|---|---|---|---|
| G1 | OpenAI Codex 官方文档（developers.openai.com）CI 页面 | 页面返回 403，未能直接核验 Codex App Server 与 CI 文档原文；仅通过 Symphony 官方博客与 cookbook 仓库树间接确认 | 部分核验 |
| G2 | OpenAI cookbook CI 示例的具体内容 | 仅核验文件存在（文件名列表），未逐条读取内容 | 需后续读取 |
| G3 | claude-code-action 的 v1.0 具体发布日期 | 仓库页显示 716 commits 与迁移指南，但未显示首个 v1.0 tag 日期 | 状态 GA 已确认，日期未核验 |
| G4 | Claude Code devcontainer 文档原文 | 被 containment 文章引用，但 code.claude.com/docs 多次超时未直达 | 需后续读取 |
| G5 | Claude Code 官方 CI/action 文档（code.claude.com/docs/en/ci 等） | 多次抓取超时/403 | 需补抓 |
| G6 | Tax AI 的 97% 准确率、50% 吞吐统计口径 | 厂商自述，无外部审计；不进入正式结论 | 标记厂商自述 |
| G7 | 两家"内部如何部署/发布"的专门披露 | 找到的是治理/质量流程（postmortem、containment），未找到类似"我们如何发布到生产"的端到端部署流水线披露 | 未覆盖，属证据缺口 |
| G8 | Codex 官方发布 Codex CLI/Agent SDK 具体 GA 日期、Codex 在 CI 的官方正式文档 | 未核验到权威日期 | 未核验 |

## 治理说明

本报告为业界洞察研究成果，按 AGENTS.md 归入 `00_sources/`。全部状态、日期来自 2026-08-07 实际访问的官方页面与 GitHub 仓库；`unverified` 项已显式标记。厂商自述指标（O1/O7/O13、A1/A4/A10/A17 中的数字）逐条标注，不进入正式结论作为行业平均值。若无 O1-O18 中关键事实的独立核验，相关结论保持阻塞。