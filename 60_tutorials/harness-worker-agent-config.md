---
title: Harness Worker Agent YAML 配置速查
aliases:
  - Harness Agent 配置教程
tags:
  - research/agentic-cicd
  - tutorial
  - company/harness
status: complete
as_of: 2026-07-17
---

# Harness Worker Agent YAML 配置速查

> [!info] 配置状态
> 下例采用 Harness 2026-07-15 官方 Worker Agent definition YAML 结构。需要把两个 `default` Connector ID 替换为当前 Account/Org/Project 中真实存在的 Connector。

## 配置放在哪里

进入 **AI → Worker Agents → 选择或新建 Agent → YAML**。该 YAML 定义 Worker Agent 的运行容器、Prompt、模型/MCP 输入以及 Visual Editor 布局。Catalog 中的 Name、Description 等发现信息在创建/编辑 Agent 时配置，不要自行猜测并添加到 YAML 根节点。

## 最小示例

```yaml
version: 1

agent:
  step:
    group:
      steps:
        - name: Agent
          if: <+Always>
          id: agent
          run:
            container:
              image: pkg.harness.io/vrvdt5ius7uwygso8s0bia/harness-agents/harness-ai-agent:latest
            env:
              PLUGIN_MAX_TURNS: 30
              PLUGIN_TASK: |
                Review the current pipeline execution.
                Identify the most likely failure cause and cite the evidence used.
                Return a diagnosis and recommended next steps.
                Do not deploy, approve, retry, or modify any resource.
              PLUGIN_HARNESS_CONNECTOR: ${{inputs.llmConnector.id}}
              PLUGIN_MCP_FORMAT: harness
              PLUGIN_MCP_SERVERS: <+connectorInputs.resolveList(<+inputs.mcpConnectors>)>

  inputs:
    llmConnector:
      type: connector
      required: true
      default: connector_Anthropic_REPLACE_ME
      ui:
        connectorCategories:
          - AI

    mcpConnectors:
      type: array
      default:
        - connector_Mcp_REPLACE_ME
      ui:
        component: array
        input:
          inputType: connector
          inputConfig:
            connectorTypes:
              - Mcp

  layout:
    - title: Agent Configuration
      items:
        - llmConnector
        - mcpConnectors
```

## 关键字段怎么理解

| 字段路径 | 必填 | 示例 | 含义与注意事项 |
|---|---|---|---|
| `version` | 是 | `1` | Worker Agent Definition 的 Schema 版本；当前固定为 `1`。 |
| `agent.step.group.steps` | 是 | 数组 | Agent 容器内执行的步骤集合。官方主步骤使用 `id: agent` 和 `if: <+Always>`。 |
| `steps[].name` | 是 | `Agent` | Visual Editor 和执行详情中展示的步骤名。 |
| `steps[].if` | 是（官方结构） | `<+Always>` | Harness 条件表达式，表示该 Agent Step 按定义进入执行；真正的上游门禁仍应放在 Pipeline 中。 |
| `steps[].id` | 是（主步骤） | `agent` | 步骤的稳定标识，其他引用和运行时会依赖它，不建议随意修改。 |
| `run.container.image` | 是 | Harness Agent 镜像 | Worker Runtime 镜像。官方例子使用 `latest`；生产应先验证兼容性，再考虑内部镜像、版本或 Digest 固定策略。 |
| `env.PLUGIN_MAX_TURNS` | 否，但建议显式写 | `30` | 单次执行允许的最大推理轮数。轮数越高，成本、时延和工具副作用机会越多；不要照搬官方完整结构中的 `150` 作为默认生产值。 |
| `env.PLUGIN_TASK` | 是 | 多行文本 | Agent 的系统任务说明。应包含目标、输入证据、禁止动作和输出格式；支持 Harness 表达式注入动态上下文。 |
| `env.PLUGIN_HARNESS_CONNECTOR` | 使用模型时需要 | `${{inputs.llmConnector.id}}` | 把类型化的 `llmConnector` 输入解析为实际模型 Connector ID。这里的 `${{inputs...}}` 是 Worker Agent 输入引用，不是 GitHub Actions 表达式。 |
| `env.PLUGIN_MCP_FORMAT` | 使用 Harness MCP 时需要 | `harness` | MCP Connector 的协议格式；Harness MCP Connector 使用 `harness`。 |
| `env.PLUGIN_MCP_SERVERS` | 使用 MCP 时需要 | `connectorInputs.resolveList(...)` | 运行时解析一组 MCP Connector 引用。保留完整 Harness 表达式，不要直接把 API Key 写进 YAML。 |
| `inputs.llmConnector.type` | 是 | `connector` | 声明该输入必须选择 Harness Connector，而不是普通字符串。 |
| `inputs.llmConnector.required` | 建议是 | `true` | 没有模型 Connector 就拒绝保存或运行，避免到运行时才产生模糊错误。 |
| `inputs.llmConnector.default` | 需替换 | `connector_...` | 默认 LLM Connector ID。模型默认值在 Connector 中配置；若要覆盖模型，还需按所用 Connector 的模型字段配置。 |
| `inputs.llmConnector.ui.connectorCategories` | 否 | `[AI]` | 让 Visual Editor 只展示 AI 类 Connector，减少误选。它是 UI 过滤，不是权限边界。 |
| `inputs.mcpConnectors.type` | 使用 MCP 时需要 | `array` | 允许选择一个或多个 MCP Connector。 |
| `inputs.mcpConnectors.default` | 否 | `[connector_Mcp_...]` | 默认 MCP Connector 列表；若任务不需要工具，可删除 MCP 相关输入和对应环境变量。 |
| `inputs.mcpConnectors.ui...connectorTypes` | 否 | `[Mcp]` | Visual Editor 只允许选择 MCP 类型 Connector，同样不能代替服务端授权和工具 Allowlist。 |
| `layout` | 否，但建议保留 | `Agent Configuration` | 决定 Visual Editor 如何分组和排列输入；不改变 Agent 运行权限。 |

## Prompt 至少写清四件事

```yaml
PLUGIN_TASK: |
  Goal: diagnose the current pipeline failure.
  Evidence: use only pipeline logs, execution metadata, and approved MCP tools.
  Forbidden: do not deploy, approve, retry, change secrets, or edit resources.
  Output: return cause, evidence, confidence, and recommended human action.
```

Prompt 的“禁止”不是强安全边界。真正的限制还要由 Harness RBAC、Agent 声明权限、Connector Tool Allowlist、Pipeline Approval/Barrier 和运行环境隔离共同实现。

## 常见错误

- **把 Connector 名称当 ID**：`default` 需要实际 Connector Identifier，且必须在 Agent 所在 Scope 可见。
- **把 `${{inputs...}}` 当 GitHub 表达式改写**：这是 Harness Worker Agent 输入引用，按官方结构保留。
- **把 API Key 直接写进 `PLUGIN_MCP_SERVERS`**：应由 Harness Connector 和 Secret 管理解析。
- **只在 `ui` 里过滤 Connector 就认为已授权**：UI Filter 只改善选择体验；运行时权限必须在服务端配置并验证。
- **盲目使用 `PLUGIN_MAX_TURNS: 150`**：先从 20—30 轮开始，根据任务完成率、成本和工具调用审计调整。
- **长期依赖 `latest` 且不做回归**：镜像升级可能改变工具或运行行为，生产环境应建立版本验证与回滚策略。
- **让无明确主体的 Trigger 获得生产写权限**：Webhook、Schedule、Artifact 等触发路径要单独验证身份注入和批准模型，不能假设与人工运行等价。

## 如何验证

1. 在 YAML Editor 保存，确认 Schema 和 Connector 类型校验通过；
2. 切回 Visual Editor，确认 `llmConnector` 和 `mcpConnectors` 正确渲染；
3. 把 Agent 加入非生产 Pipeline，第一次仅允许读取和生成诊断；
4. 使用人工触发运行，检查模型 Connector、MCP Tool、轮数、Token、日志和 Audit Trail；
5. 分别测试 Manual/API 与 Webhook/Schedule 等触发方式的实际权限；
6. 只有在服务端拒绝测试、审批和回退均通过后，才增加写权限。

## 下钻阅读

- [Harness Agents YAML 官方参考](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents-references/)
- [Harness Worker Agents 官方文档](https://developer.harness.io/docs/platform/harness-ai/harness-agents/)
- [[50_deepdives/harness-company/90_report|Harness 公司深度报告]]
- [[50_deepdives/harness-company/40_labs/README|Harness 实验与权限验证方案]]
