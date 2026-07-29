---
title: 代表性 CLI 与能力覆盖表来源
as_of: 2026-07-28
artifact:
  - cli-software-delivery-agent-interface-insight-final.pptx
---

# 行级来源映射

| 作业阶段 | 公司 / CLI | CLI 能力来源 | MCP 形态来源与边界 |
|---|---|---|---|
| 编码与协同 | GitHub / `gh` | [GitHub CLI manual](https://cli.github.com/manual/) | [GitHub MCP Server](https://github.com/github/github-mcp-server)：独立于 `gh`；官方远程托管，也支持本地运行。 |
| 检查与门禁 | Snyk / `snyk` | [Snyk CLI](https://docs.snyk.io/developer-tools/snyk-cli) | [Snyk agentic integrations](https://docs.snyk.io/integrations/snyk-studio-agentic-integrations/getting-started-with-snyk-studio)：本地启动命令为 `snyk mcp -t stdio`，也可用 `npx -y snyk@latest mcp -t stdio`；最后明确产品状态为 EA。 |
| 构建与验证 | CircleCI / `circleci` | [CircleCI CLI](https://circleci.com/docs/guides/toolkit/local-cli/) | [CircleCI CLI MCP](https://circleci.com/docs/guides/toolkit/connecting-to-the-circleci-cli-mcp/)：本地启动命令为 `circleci mcp start`；CLI v1 当前为 Preview，另有独立 Hosted Server。 |
| 制品与供应链 | JFrog / `jf` | [JFrog CLI command reference](https://docs.jfrog.com/integrations/docs/jfrog-cli-command-reference) | [JFrog MCP installation](https://docs.jfrog.com/installation/docs/mcp)：独立于 `jf`；支持 SaaS 远程与自托管，自托管安装当前标注 Beta。 |
| 部署与发布 | HashiCorp / `terraform` | [Terraform CLI overview](https://developer.hashicorp.com/terraform/cli/commands) | [Terraform MCP Server](https://developer.hashicorp.com/terraform/mcp-server)：独立于 `terraform` CLI 的本地 Server；官方文档显示 v1.0.x。 |
| 观测与恢复 | Sentry / `sentry-cli` | [Sentry CLI repository](https://github.com/getsentry/sentry-cli) | [Sentry MCP repository](https://github.com/getsentry/sentry-mcp)：独立于 `sentry-cli`；远程托管服务可用，本地 stdio 形态仍在开发。 |

## 选择口径

- 每个软件交付阶段保留一家代表性公司，优先选择能体现不同 CLI 能力与 MCP 产品形态的案例。
- 表格中的“官方独立 Server”“CLI 内置 / 启动 Server”和“Hosted Server”是不同架构关系，不应合并为统一的“支持 MCP”。
- CLI 可执行与 MCP 可发现都不自动授予写入、部署或发布权限；认证、授权、审批和外部门禁仍由宿主系统控制。
