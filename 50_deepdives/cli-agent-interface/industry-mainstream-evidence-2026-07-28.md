---
title: CLI Agentic CI/CD 主流机制核验（2026-07-28）
tags:
  - research/agentic-cicd
  - research/evidence
  - tool/cli
  - status/verified
status: complete
as_of: 2026-07-28
accessed_at: 2026-07-28
topic_id: cli-agent-interface
confidence: high
---

# CLI Agentic CI/CD 主流机制核验（2026-07-28）

> [!abstract] 可用于汇报的校准后判断
> **CLI 不是被单一协议取代的旧入口，而是正被多家 Agent Harness 用作非交互、可组合的执行面；其自治上限不由 CLI 或模型单独决定，而由任务权限、隔离运行时、批准与独立验证共同限定。**
>
> 这里的“主流”只指 **OpenAI、Anthropic、GitHub、Google 四个相互独立的 Agent Harness 都已公开产品化终端/非交互运行与控制机制，且 Kubernetes、Terraform 等工程能力继续维护机器可读 CLI 契约**。它**不**表示已有公开的市场份额、企业采用率或 CI/CD 成功率统计；不能写成“所有企业的默认架构”。

## 一、研究范围与判定口径

- **观察对象：** OpenAI Codex CLI、Anthropic Claude Code、GitHub Copilot CLI 与 Agentic Workflows、Google Gemini CLI、Kubernetes `kubectl`、HashiCorp Terraform、MCP 规范、HKUDS CLI-Anything 论文/源码。
- **来源限制：** 官方文档、官方源码仓库/发布说明、MCP 规范和原始论文；未使用媒体报道、市场报告或二手解读。
- **访问日期：** 2026-07-28。除发布公告与论文外，多数活文档未显示页面发布日期/更新时间，表中如实标记为“未显示”，不臆补日期。
- **判定规则：** 单个产品事实只证明该产品；“收敛”只在两个以上独立组织有同构机制时使用；“行业主流”不外推为市场采用率。

## 二、产品与组织事实矩阵

| 组织 / 资料 | 可直接核验的事实 | 发布时间 / 页面时间 | 产品状态（截至访问日） | 可支持的边界 |
|---|---|---|---|---|
| OpenAI / [Codex 非交互模式](https://learn.chatgpt.com/docs/non-interactive-mode) | `codex exec` 明示用于脚本和 CI，可管道化输出；支持 JSON Schema 的最终结构化输出；运行可预设 sandbox 与 approval；CI 凭据须避免暴露给仓库控制代码。 | 未显示；访问 2026-07-28 | 文档未标注 GA/Preview；官方源码称其为本地运行的 coding agent。 | 证明一条已产品化 Harness 将 Agent 置入 CI 和结构化下游流程；不证明其能替代 CI 的发布门禁。 |
| Anthropic / [Claude Code CLI reference](https://code.claude.com/docs/en/cli-usage)、[Claude 4 公告](https://www.anthropic.com/news/claude-4) | `claude -p` 是非交互查询；支持 pipe、`--json-schema`、`--max-turns`、`--allowed-tools` 与 `--tools`（可见工具集合）；可经 `--mcp-config` 加载 MCP。公告称 Claude Code 于 2025-05-22 GA。 | 2025-05-22（GA 公告）；CLI 页未显示 | **GA**（Claude Code；单项 flag 的 Preview/Beta 另按页面标注） | 证明终端 Harness 同时产品化非交互、结构化结果、工具集与许可控制，并且可组合 MCP。 |
| GitHub / [Copilot CLI 工具许可](https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli/allowing-tools)、[GA 发布说明](https://github.blog/changelog/2026-06-23-copilot-cli-new-terminal-interface-is-generally-available/) | `--available-tools` / `--excluded-tools` 决定模型知道什么；`--allow-tool` / `--deny-tool` 决定能否执行特定工具或子命令，且 deny 优先。新终端界面于 2026-06-23 GA，提供 MCP、Skills、Plugins 的配置入口。 | 2026-06-23（GA 公告）；许可页未显示 | **GA**（Copilot CLI 新终端界面） | 最强的“工具可见性与执行授权分离”一手例证；还证明 CLI Harness 与 MCP/Skill 可以并存。 |
| GitHub / [Agentic Workflows 源码仓库](https://github.com/github/gh-aw) | 以自然语言 Markdown 编写，运行在 GitHub Actions；支持 Copilot、Claude、Codex、Gemini。默认只读，写入只经 sanitized `safe-outputs`；另有 sandbox、网络隔离、工具 allowlist、编译期验证和关键操作人工批准。 | 仓库活文档未显示；访问 2026-07-28 | 开源项目；仓库未对整体标注 GA/Preview | 证明 CI 承载中，Agent 运行时与写入准入、批准、人机控制是分层的；不能据此推断 GitHub Actions 所有工作流均采用该模型。 |
| Google / [Gemini CLI headless](https://raw.githubusercontent.com/google-gemini/gemini-cli/main/docs/cli/headless.md)、[sandbox](https://raw.githubusercontent.com/google-gemini/gemini-cli/main/docs/cli/sandbox.md) | Headless 模式在非 TTY 或 `-p` 下运行，返回 JSON 或 JSONL（含 tool request/result），并给出标准退出码；sandbox 隔离潜在危险 shell/文件操作并可限制文件范围。 | main 分支活文档未显示；访问 2026-07-28 | 资料未标注 GA/Preview，**状态未核实** | 以独立生态佐证“非交互 + 机器输出 + 沙箱”不是单一厂商特例；不把 main 分支文档当作稳定 API 承诺。 |
| Kubernetes / [kubectl JSONPath](https://kubernetes.io/docs/reference/kubectl/jsonpath/)、[Authorization](https://kubernetes.io/docs/reference/access-authn-authz/authorization/) | `kubectl` 支持 `-o json`/JSONPath；API server 在认证后对请求属性与策略作允许/拒绝判断，默认拒绝；`kubectl auth can-i` 可询问当前身份对动作的授权。 | 活文档未显示；访问 2026-07-28 | Kubernetes 文档未对 `kubectl` 另标产品阶段 | 证明成熟能力 CLI 已具机器可读输出，而真实执行许可在 API/身份控制面，不由 CLI 输出格式决定。 |
| HashiCorp / [Terraform plan](https://developer.hashicorp.com/terraform/cli/commands/plan)、[show](https://developer.hashicorp.com/terraform/cli/commands/show)、[apply](https://developer.hashicorp.com/terraform/cli/commands/apply) | `plan` 只提出变更，不执行；可保存 Plan 后在自动化中执行；`show -json` 给出机器可读 Plan/State；直接 `apply` 默认提示批准，而 `-auto-approve` 显式跳过该批准。 | 活文档未显示；当前页为 v1.15.x；访问 2026-07-28 | 文档未标注 Preview/Beta | 最强的“可检查 Plan → 明确批准/Apply” CLI 契约例证；Plan 也可能因外部漂移失效，故它不是业务成功证明。 |
| Model Context Protocol / [2025-06-18 规范](https://modelcontextprotocol.io/specification/2025-06-18) | MCP 用 JSON-RPC 连接 Host/Client/Server，标准化 Context、Resources、Prompts、Tools 与能力协商；规范明确要求用户对数据访问和 Tool invocation 有明确同意，并说明协议本身不能强制实施授权。 | 2025-06-18 | 已发布版本规范；不以产品 GA 口径标注 | 证明 MCP 是跨应用的连接/发现能力层，且仍需 Host/控制面实现授权；不等于替代底层 CLI/API。 |
| HKUDS / [CLI-Anything 论文](https://arxiv.org/abs/2606.03854)、[HARNESS.md](https://raw.githubusercontent.com/HKUDS/CLI-Anything/main/cli-anything-plugin/HARNESS.md)、[仓库](https://github.com/HKUDS/CLI-Anything) | 原始论文提出以程序化控制和确定性执行改造 Agent computer use；方法规范要求人机双模输出（`--json`）、subprocess 测试、真实后端 E2E、`SKILL.md` 与可安装 harness；其 Codex 集成在仓库中标作 Experimental/Community。 | 论文 v1：2026-06-02；仓库活文档访问 2026-07-28 | **研究/开源参考实现**；部分集成 Experimental/Community | 证明“Agent-native interface factory”路线正在出现；不证明企业 CI/CD 生产规模、可靠性或授权成熟度。 |

## 三、五个待汇报判断的逐项审计

### C1：Agent Harness 已产品化非交互、结构化输出与权限/隔离控制

**来源事实。** Codex `exec` 面向 CI、管道输出、Schema、sandbox/approval；Claude Code 有 `-p`、Schema、turn budget、allowed tools；Copilot CLI 有模型可见工具集与 allow/deny；Gemini CLI 有 headless JSON/JSONL、退出码和 sandbox。四者来自 OpenAI、Anthropic、GitHub、Google 四个独立组织。

**跨来源推断（高置信）。** “终端 Agent 只能以交互聊天形式使用”已不是主流产品形态；非交互执行、可供下游消费的机器结果和受控副作用，已构成跨生态的共同设计方向。

**不能推出。** 这些机制不证明所有产品功能均 GA，也不证明它们已成为全部企业 CI/CD 的默认 Harness。

### C2：能力 CLI 正形成可重放的机器契约

**来源事实。** Kubernetes 的 `kubectl -o json` 和 Terraform 的 `show -json` 均提供机器可读输出；Terraform 将 `plan` 与保存的 `apply` 分成两个显式阶段；CLI-Anything 的方法规范把 `--json`、进程级 CLI 测试、真实后端 E2E 与安装分发作为生成 harness 的要求。

**跨来源推断（中高置信）。** 对 Agent 而言，稳定 CLI 价值不在“终端”而在显式参数、可保存的输入/Plan、机器输出、退出状态和真实后端调用所组成的进程契约。这让任务可以在 Runner 中复验和审计。

**证据缺口。** 现有来源没有同一 CI/CD 任务的“直接 CLI vs MCP vs GUI Agent”成功率、成本或生产事故对照；因此不能写“CLI 已成为所有 Agent 的主执行接口”。

### C3：Tool 可见性与执行授权必须分离

**来源事实。** Copilot CLI 将 `--available-tools` / `--excluded-tools` 定义为模型可感知的工具集合，将 `--allow-tool` / `--deny-tool` 定义为实际权限，后者还能限制子命令且 deny 优先。Kubernetes 则说明 API server 对认证后的每个请求作策略裁决，默认拒绝。

**跨来源推断（高置信）。** Tool discovery、模型可见性、命令/资源级许可是不同控制对象。将它们合并会产生两个风险：模型可见却反复被拒，或为减少拒绝而过度授权。

**不能推出。** Copilot 的具体 flags 不是行业标准；企业需要在自己的 Harness、CI identity 与后端策略中实现等价分层。

### C4：外部 Oracle / Plan-Approval 决定自治上限

**来源事实。** Terraform 的 Plan 不执行变更，自动化可使用保存的 Plan；直接 Apply 默认需要批准。GitHub Agentic Workflows 默认只读、以 `safe-outputs` 限制写入，并为关键操作设置人工批准、编译期验证和 sandbox。MCP 规范也强调 Tool 调用须经用户同意，且协议本身不能强制授权。

**跨来源推断（高置信，措辞限缩）。** 更准确的结论是：**Agent 的“能否尝试命令”与系统“是否接受写入/变更结果”必须由不同的控制点决定。** 在 CI/CD 中，Plan/审批/策略/测试/扫描/签名/SLO/人工 Review 等独立判据才可充当接受门；CLI 的 exit code 不是充分的业务 Oracle。

**证据缺口。** 本次一手来源只直接证明 Plan/Approval、safe output 与授权，不足以证明每一种 Test、Scan、Signature 或 SLO 都已在上述 Harness 中统一集成。PPT 不应画成单一“自动 Oracle 产品”。

### C5：CLI 与 MCP 是分层互补，不是替换关系

**来源事实。** MCP 规范定义 Host/Client/Server 的 Context、Resource、Prompt、Tool 与能力协商；Claude Code CLI 可通过 `--mcp-config` 加载 MCP；Copilot CLI 的 GA 终端产品同时配置 MCP servers、Skills、Plugins；Terraform 和 Kubernetes 仍将业务能力以独立 CLI 与后端 API/授权提供。

**跨来源推断（高置信）。** MCP 解决跨 Host 的能力暴露、发现和交互语义；CLI 解决本地/Runner 内的进程执行、脚本组合和既有命令生态。常见组合是 Harness 经 MCP 发现/接入能力，再由 CLI/API/SDK 落实后端动作，但并非每个 MCP Server 都会调用 CLI。

**边界。** 不应称“MCP 必然包裹 CLI”，也不能称“所有 CLI 都需要 MCP”。这是一种架构分层判断，不是协议规定。

## 四、可直接进入 PPT 的最强证据组合

| 页面要表达的机制 | 画面中应点名的组织 / 证据 | 推荐的受限表述 |
|---|---|---|
| **Agent Harness 已成为可进入 CI 的产品形态** | OpenAI Codex `exec`（CI + Schema + safety strategy）；Anthropic Claude Code `-p`/Schema/permissions；Google Gemini CLI headless JSONL/sandbox | “四家独立 Harness 都把非交互、机器输出和副作用控制做成显式产品机制。” |
| **“看见工具”不等于“获准执行”** | GitHub Copilot CLI 的 available/excluded 与 allow/deny 两层；Kubernetes API 的默认拒绝和 `auth can-i` | “Tool visibility 决定模型能规划什么；任务身份与后端策略决定真实动作能否发生。” |
| **自治不是 CLI 成功码，而是接受门的组合** | Terraform Plan → inspect/approve → Apply；GitHub Agentic Workflows read-only → safe-output → human approval | “CLI 将判断压缩为可检查行动；批准、策略与独立验证决定结果能否被接受。” |
| **CLI 与 MCP 形成调用栈** | MCP Host/Client/Server 规范；Claude Code `--mcp-config`；Copilot CLI 同时装载 MCP/Skill/Plugin；`kubectl`/Terraform 的机器契约 | “MCP 提供跨 Agent 连接与发现；CLI/API 保留确定性执行与既有权限边界。” |
| **接口工厂是新兴补位，而非主结论** | CLI-Anything 论文 + HARNESS 的 `--json`、真实后端 E2E、SKILL；Codex 集成 Experimental/Community | “CLI-Anything 展示长尾软件可被生成 Agent-ready CLI；生产授权与规模可靠性仍待验证。” |

## 五、建议替换原页面标题与注脚

**标题（建议）：**

> 多家 Agent Harness 正把 CLI 产品化为受控执行面；自治上限由权限、隔离与独立接受门决定

**标题证据脚注（可放 Speaker Notes）：**

> 机制佐证：OpenAI Codex `exec`、Anthropic Claude Code、GitHub Copilot CLI/Agentic Workflows、Google Gemini CLI；执行契约佐证：Kubernetes `kubectl`、HashiCorp Terraform；协议边界：MCP 2025-06-18 Spec。访问于 2026-07-28。该页描述跨产品机制收敛，不代表市场份额或企业普遍采用率。

## 六、来源索引

1. OpenAI, [Non-interactive mode](https://learn.chatgpt.com/docs/non-interactive-mode), 页面未显示日期，访问 2026-07-28。
2. OpenAI, [openai/codex](https://github.com/openai/codex), 活仓库，访问 2026-07-28。
3. Anthropic, [CLI reference](https://code.claude.com/docs/en/cli-usage), 页面未显示日期，访问 2026-07-28；[Introducing Claude 4](https://www.anthropic.com/news/claude-4), 2025-05-22。
4. GitHub Docs, [Allowing and denying tool use](https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli/allowing-tools), 页面未显示日期，访问 2026-07-28；GitHub Changelog, [Copilot CLI GA](https://github.blog/changelog/2026-06-23-copilot-cli-new-terminal-interface-is-generally-available/), 2026-06-23。
5. GitHub, [github/gh-aw](https://github.com/github/gh-aw), 活仓库，访问 2026-07-28。
6. Google, [Gemini CLI headless](https://raw.githubusercontent.com/google-gemini/gemini-cli/main/docs/cli/headless.md) 与 [sandbox](https://raw.githubusercontent.com/google-gemini/gemini-cli/main/docs/cli/sandbox.md)，main 分支活文档，访问 2026-07-28。
7. Kubernetes, [JSONPath Support](https://kubernetes.io/docs/reference/kubectl/jsonpath/) 与 [Authorization](https://kubernetes.io/docs/reference/access-authn-authz/authorization/)，活文档，访问 2026-07-28。
8. HashiCorp, [`terraform plan`](https://developer.hashicorp.com/terraform/cli/commands/plan), [`show`](https://developer.hashicorp.com/terraform/cli/commands/show), [`apply`](https://developer.hashicorp.com/terraform/cli/commands/apply)，活文档，访问 2026-07-28。
9. Model Context Protocol, [Specification 2025-06-18](https://modelcontextprotocol.io/specification/2025-06-18), 2025-06-18。
10. Yang et al., [CLI-Anything: Towards Agent-Native Computer Use](https://arxiv.org/abs/2606.03854), arXiv v1 2026-06-02；[CLI-Anything repository](https://github.com/HKUDS/CLI-Anything) 与 [HARNESS.md](https://raw.githubusercontent.com/HKUDS/CLI-Anything/main/cli-anything-plugin/HARNESS.md)，活仓库，访问 2026-07-28。
