---
title: CLI Claim—Evidence—Gap Matrix
tags:
  - research/agentic-cicd
  - research/evidence-map
  - tool/cli
status: complete
as_of: 2026-07-27
---

# CLI Claim—Evidence—Gap Matrix

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 |
|---|---|---|---|---|
| CLI-C01 | CLI 可成为 Agent 的稳定机器接口 | [GitHub CLI JSON/`jq`/template](https://cli.github.com/manual/gh_help_formatting)、[kubectl 输出格式](https://kubernetes.io/docs/reference/kubectl/)、[AWS CLI 输出格式](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-output-format.html) | 这些机制不保证业务语义稳定，也不统一输入 Schema | high |
| CLI-C02 | 本地单 Harness 场景中，成熟 CLI 通常比 MCP 更简单 | OS 进程、容器、版本锁定与 CI Runner 的既有能力；[Thoughtworks “MCP by default”](https://www.thoughtworks.com/en-us/radar/techniques/mcp-by-default) 的 Caution 判断 | 这是架构判断；跨客户端、远程多租户时未必成立 | high |
| CLI-C03 | CLI 适合可重放的 Plan/Apply 工作流 | [Terraform 自动化指南](https://developer.hashicorp.com/terraform/tutorials/automation/automate-terraform) | Provider 与外部系统仍可能漂移；`apply` 有真实副作用 | high |
| CLI-C04 | 结构化输出不等于安全 | [Terraform Output](https://developer.hashicorp.com/terraform/tutorials/configuration-language/outputs) 明确 `-json` 会显示 sensitive 值 | 安全结果取决于具体实现与凭据边界 | high |
| CLI-C05 | CLI 正同时成为 Agent Harness 的用户入口 | [GitHub Copilot CLI 2026-06 GA](https://github.blog/changelog/2026-06-23-copilot-cli-new-terminal-interface-is-generally-available/)、本地 S78—S80 | 产品采用数据有限；终端入口不代表执行面都是 CLI | high |
| CLI-C06 | CLI 与 MCP 趋势是组合，而非替代 | Copilot CLI 同时支持 MCP、Skills、Plugins；CLI-Anything 可生成 CLI/Skill，并可被 MCP 包装 | 不同厂商的默认集成路径仍在变化 | high |
| CLI-C07 | CLI 的主要企业风险来自隐式状态、共享身份与不稳定契约 | Terraform 状态/锁、CLI 输出文档及 CI/CD 通用运行模型 | 缺少跨企业公开事故统计 | medium-high |
| CLI-C08 | Agent-ready CLI 应被当作版本化产品而非脚本 | GitHub、Terraform、AWS、Kubernetes 的稳定格式与版本文档 | 对内部 CLI 的维护成本尚需企业自测 | high |
| CLI-C09 | 模型可见的 Toolset 与获准执行的命令是两条不同控制线 | [GitHub Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli/allowing-tools) 分开定义 available/excluded tools 与 allow/deny permissions，且 deny 优先 | 这是单产品的明确实现，其他 Harness 的配置粒度不同 | high |
| CLI-C10 | CLI 只负责把行动变成可重放契约，自治上限由身份、Sandbox 与外部 Oracle 共同决定 | [Codex 非交互模式](https://developers.openai.com/codex/noninteractive) 分开提供 JSONL/Schema、Sandbox 与 CI 凭据边界；[Terraform 自动化](https://developer.hashicorp.com/terraform/tutorials/automation/automate-terraform) 将 Plan、人工 Review 与 Apply 分离 | 这是跨实现归纳出的架构判断；公开材料缺少长期生产效果对照 | high for mechanism, medium for outcome |

## 证据缺口

- 缺少对同一 CI/CD 任务分别使用直接 CLI、CLI+Skill、MCP Server 的公开对照基准；
- 缺少 CLI Agent 在生产发布操作上的长期误操作和接管数据；
- 终端 Agent 产品活跃度可证明方向，不能证明企业单位成功成本。
- 缺少对“模型可见 Toolset”与“任务可执行命令集”配置错误的跨产品事故与效果数据。
