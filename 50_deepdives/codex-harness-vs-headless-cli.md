---
title: Codex Harness 开放：从编程助手到 Agent Runtime，以及它和 Headless Codex CLI 到底有什么区别
aliases:
  - Codex Harness vs Headless CLI
tags:
  - research/agentic-cicd
  - research/deep-dive
  - tool/codex
  - topic/agent-runtime
status: complete
as_of: 2026-08-24
verified: 2026-08-24
confidence: high
source_policy: official-openai-primary-only
---

# Codex Harness 开放：从编程助手到 Agent Runtime，以及它和 Headless Codex CLI 到底有什么区别

2026 年 8 月 19 日，OpenAI 发布了 **[《Codex as a platform: build on the open agent harness》](https://developers.openai.com/blog/codex-as-a-platform)**，正式把 Codex 背后的 Agent Harness 推到平台层面。

这件事容易被简单理解成：

> OpenAI 又开源了一个 Codex 组件。

但如果只看到“开源”，其实会低估这次变化。

OpenAI 真正释放出的信号是：

> **Codex 不再只是一个 AI 编程产品，而正在被抽象为一套可以嵌入 IDE、CI/CD、运维平台、安全平台和企业业务系统的 Agent Runtime。**

与此同时，这也带来一个很自然的问题：

> 既然 `codex exec` 已经可以通过 Headless 模式在 CI、脚本和后台任务中运行完整 Agent，那么 Codex Harness 到底是什么？为什么还需要 Harness、SDK 和 App Server？

答案的关键在于：

> **Codex Harness 和 Headless Codex CLI 并不是两个同级的竞争方案。**

更准确的关系是：

> **Harness 是运行 Agent 的“发动机”，而 `codex exec` 是这个发动机提供的一种批处理式调用入口。**

理解这一点，是理解这次 Codex 平台化最重要的一步。

---

# 一、先澄清：“Codex Harness 开源”到底发生了什么

先纠正一个容易被新闻标题误导的地方。

2026 年 8 月 19 日并不是 Codex 突然从闭源变成开源。

官方资料支持的精确边界是：OpenAI 当前把 Codex CLI、SDK 与 App Server 分别列为开源组件，并给出三者的源码位置；8 月 19 日的平台文章进一步明确说明，Codex App、CLI 和 IDE 体验由同一个开源 Codex Harness 驱动。因此这次变化的重点是把这些开放组件明确组织成平台与集成契约，而不是只宣布某一个新组件开源。[OpenAI：Codex 开源组件](https://learn.chatgpt.com/docs/open-source)；[OpenAI：Codex as a platform](https://developers.openai.com/blog/codex-as-a-platform)

因此 8 月 19 日这次变化更准确的描述应该是：

> **OpenAI 正式把此前分散存在于 CLI、SDK、App Server 和 Codex Core 中的能力，统一定义成一套 “Open Agent Harness”，并明确鼓励第三方直接基于它构建自己的 Agent 产品。**

OpenAI 官方现在明确表示，Codex CLI、App Server 和官方 Codex SDK 都属于开放组件，而模型访问和 OpenAI 托管服务仍是另外一层。[OpenAI：Codex as a platform](https://developers.openai.com/blog/codex-as-a-platform)

所以这次最重要的变化并不是：

```text
Closed Source
      ↓
Open Source
```

而更接近：

```text
Codex 内部实现
      ↓
公开组件
      ↓
统一 Agent Harness
      ↓
正式 Platform Contract
      ↓
第三方 Agent 产品
```

也就是说，以前开发者看到的是几个 Codex 工具；现在 OpenAI 开始告诉开发者：

> **这些工具背后其实存在一个可以被复用的 Agent Runtime。**

这是一种明显的平台化。

---

# 二、什么是 Codex Harness？

理解 Harness，首先要摆脱一个简单的 Agent 模型：

```text
Prompt
  ↓
LLM
  ↓
Response
```

一个真正能够完成复杂工程任务的 Agent，远远不只是调用一次模型。

例如让 Agent：

> “分析 CI 为什么失败，找到代码问题，修改代码，运行测试，如果失败继续分析，必要时请求人工确认，最后生成 MR。”

背后至少涉及：

```text
理解任务
  ↓
收集上下文
  ↓
调用模型推理
  ↓
决定下一步动作
  ↓
调用 Shell / Git / Tool
  ↓
获取执行结果
  ↓
重新进入模型
  ↓
维护上下文
  ↓
判断是否完成
  ↓
必要时请求 Approval
  ↓
继续执行
```

这整个循环才是真正的 Agent。

OpenAI 对 Harness 的定义也非常直接：

> 一个有能力的 Agent 不只是 Prompt 加模型响应，它还需要维护上下文、检查信息、调用工具、暴露执行进度、处理失败、请求人工批准并返回最终结果；包围模型的这套执行系统就是 Harness。[OpenAI：The reusable part is the agent loop](https://developers.openai.com/blog/codex-as-a-platform)

因此可以把 Codex Harness 理解成：

> **模型之上的 Agent Runtime。**

---

# 三、Codex Harness 里究竟有什么？

如果把它拆开，大致可以分成几个关键能力。

## 1. Agent Loop

最核心的当然是 Agent Loop。

```text
               ┌───────────────┐
               │     Model     │
               └───────┬───────┘
                       │
                 Reason / Decide
                       │
                       ↓
               ┌───────────────┐
               │  Tool Action  │
               └───────┬───────┘
                       │
                    Result
                       │
                       ↓
               ┌───────────────┐
               │     Model     │
               └───────────────┘
```

Harness 决定：

- 给模型什么上下文；
- 如何调用模型；
- 模型可以使用哪些工具；
- Tool Result 如何重新进入模型；
- 什么时候继续推理；
- 什么时候结束；
- 什么时候需要人工介入。

所以 Agent Loop 本质上是整个 Agent 的执行引擎。

---

## 2. Context 与 Compaction

长时间 Agent 最大的问题之一就是上下文增长。

例如一个 CI 自愈 Agent 已经：

- 看过 5000 行 Build Log；
- 阅读了十几个代码文件；
- 运行了 8 次测试；
- 尝试过 3 种修复方案。

如果每次都把所有历史重新发送给模型，成本和性能都会迅速恶化。

因此 Harness 会负责：

```text
Context Selection
Context Retention
Context Compaction
Reasoning Retention
```

OpenAI 在官方文章中特别用 ARC-AGI-3 说明了 Harness 的重要性：通过 retained reasoning 和 context compaction，同一个 GPT-5.6 Sol 的表现从 **13.3% 提升到 38.3%**，同时输出 Token 降低约六倍。这是 OpenAI 披露的特定 Benchmark 结果，不应外推为所有 Agent 任务的通用增益。[OpenAI：Codex as a platform](https://developers.openai.com/blog/codex-as-a-platform)

这个数据非常值得关注。

它说明：

> **同一个模型，仅仅改变 Harness，就可能产生完全不同的 Agent 能力。**

所以未来比较 Agent 系统，不能只比较：

```text
GPT vs Claude vs Gemini
```

还要比较：

```text
Model
 ×
Context Engineering
 ×
Harness
 ×
Tools
 ×
Execution Environment
 ×
Verification
```

Harness 本身正在成为 AI 基础设施的一层。

---

# 四、Thread / Turn / Item：Harness 的状态模型

Agent 和普通 API 最大的区别之一，是它具有长期状态。

Codex 把运行状态抽象成类似：

```text
Thread
  │
  ├── Turn
  │     ├── Item
  │     ├── Item
  │     └── Item
  │
  └── Turn
        ├── Item
        └── Item
```

可以简单理解为：

### Thread

一整个长期任务或 Agent 会话。

例如：

```text
修复 CI #24831
```

### Turn

Agent 围绕一个目标执行的一轮工作。

例如：

```text
分析第一次 Build Failure
```

### Item

这一轮里的具体行为。

例如：

```text
读取日志
调用 Shell
读取文件
执行测试
生成消息
请求 Approval
```

OpenAI 的 App Server 正是围绕 Thread 生命周期和事件流来暴露 Harness 能力：客户端可以创建或恢复 Thread、开始 Turn、接收事件以及处理 Approval。[OpenAI：Codex App Server](https://learn.chatgpt.com/docs/app-server)

---

# 五、为什么 Thread 模型对 Agent 很重要？

因为一个真正复杂的 Agent 不可能总是：

```text
Input
 ↓
Output
```

例如：

```text
Thread: CI #24831
│
├─ Turn 1：诊断
│
│   ├─ Read build.log
│   ├─ Read recent commits
│   ├─ Inspect dependency graph
│   └─ Hypothesis:
│        dependency mismatch
│
├─ Turn 2：第一次修复
│
│   ├─ Modify BUILD
│   ├─ Run test
│   └─ Test failed
│
├─ Turn 3：第二次修复
│
│   ├─ Modify dependency
│   ├─ Run test
│   └─ Test passed
│
└─ Turn 4：准备提交
    ├─ Generate diff
    ├─ Request approval
    └─ Create MR
```

有了这种模型以后，就可以自然实现：

```text
persist
resume
interrupt
stream
audit
fork
```

Agent 不再只是“模型聊天记录”，而成为一个真正存在生命周期的 Runtime Object。

---

# 六、Tool Execution：Agent 不只是“说”，而是真的“做”

Coding Agent 最重要的区别，就是它能够执行操作。

例如：

```text
read file
write file
shell
git
test
build
MCP
API
```

Codex Harness 负责把这些工具统一接入 Agent Loop。

于是：

```text
Model
  ↓
决定执行动作
  ↓
Harness
  ↓
Tool
  ↓
真实环境
  ↓
Tool Result
  ↓
Harness
  ↓
Model
```

这意味着模型本身不需要理解：

> “如何管理一个 Shell Process？”

模型只需要表达：

> “我要运行 pytest。”

Harness 负责真正执行。

---

# 七、Sandbox 与 Approval：Agent 能执行，但不能无限执行

Agent 最大的风险通常不是：

> 回答错了。

而是：

> **执行错了。**

Coding Agent 可能拥有：

```text
rm
git push
kubectl
terraform
deploy
database
production API
```

如果只依赖 Prompt：

```text
请不要执行危险命令。
```

显然不够。

因此 Codex Harness 把：

```text
Sandbox
Approval
Policy
```

变成 Runtime 能力。

更合理的执行模型是：

```text
Model
  ↓
提出 Action
  ↓
Harness Policy
  ↓
是否允许？
  │
  ├── Yes → Sandbox 执行
  │
  └── Need Approval
          ↓
        Human
          ↓
        Approve
          ↓
        Execute
```

也就是说：

> **模型负责提出行为，Runtime 决定行为有没有资格发生。**

这对于企业 Agent 尤其关键。

---

# 八、Skills、MCP 与外部系统

Harness 还负责把 Agent 和真实世界连接起来。

例如：

```text
Git
Jenkins
GitHub
Artifact Repository
Observability
Kubernetes
ServiceNow
Slack
Internal Platform
```

都可以通过 Tool、Skill 或 MCP 等形式提供给 Agent。

于是架构变成：

```text
                   Agent
                     │
                 Harness
                     │
       ┌─────────────┼─────────────┐
       ↓             ↓             ↓
     Shell          MCP          Skill
       ↓             ↓             ↓
      Git          CI API       Workflow
      Test        Artifact      Knowledge
      Build       Deploy        Rule
```

这里有一个非常重要的架构边界：

> **Harness 不应该拥有所有企业业务逻辑。**

OpenAI 在官方示例 Relay 中明确把职责分为两层：

```text
Host Application
负责：
UI
业务上下文
Business Rule
企业数据
Tools
Approval UX

Codex Harness
负责：
Agent Loop
Conversation State
Tool Interaction
Streaming
Sandboxed Execution
```

官方甚至直接概括为：

> Your application owns product context, business rules, and tools; Codex app-server provides the agent loop and sandboxed execution. [OpenAI：Codex as a platform，Figure 1](https://developers.openai.com/blog/codex-as-a-platform)

这是 Codex Harness 最值得企业架构借鉴的一点。

---

# 九、Codex Harness 开放之后，开发者有三种主要接入方式

OpenAI 目前明确提供了三种主要集成层次：

```text
                 Codex Harness
                       │
          ┌────────────┼────────────┐
          ↓            ↓            ↓
     codex exec       SDK       App Server
```

它们不是三套 Agent。

而是：

> **同一个 Agent Runtime 的三种控制粒度。**

---

## 第一种：codex exec

用于：

```text
Script
CI Job
Cron
一次性后台任务
```

例如：

```bash
codex exec "分析当前仓库测试为什么失败，并给出修复方案"
```

它的核心模型是：

```text
Start
 ↓
Run Agent
 ↓
Output
 ↓
Exit
```

官方把它定位为非交互式、可脚本化的 CLI 模式，适合 Pipeline、CI、Pre-merge Check 和 Scheduled Job；它也可以输出 JSONL 事件或符合 JSON Schema 的最终结果。[OpenAI：Non-interactive mode](https://learn.chatgpt.com/docs/non-interactive-mode)

---

## 第二种：Codex SDK

SDK 适合：

> 你的程序需要控制 Codex，但又不想自己实现完整 App Server Protocol。

例如：

```text
Your Backend
     ↓
Codex SDK
     ↓
Codex Runtime
```

程序可以：

```text
start
resume
stream
```

Agent Task。

官方当前将 SDK 定位为本地 Codex Agent 的程序化控制接口，可在应用或 CI/CD 流程中启动、继续和恢复 Thread。[OpenAI：Codex SDK](https://learn.chatgpt.com/docs/codex-sdk)

---

# 十、第三种：Codex App Server

如果 Agent 本身就是产品的一部分，就进入 App Server。

例如：

```text
SRE Portal
CI Agent Console
IDE
Security Platform
Ops Dashboard
```

架构大致是：

```text
             Your Application
                    │
              Agent Protocol
                    │
                    ↓
            ┌──────────────┐
            │ App Server   │
            └──────┬───────┘
                   │
              Codex Harness
                   │
         ┌─────────┼─────────┐
         ↓         ↓         ↓
     Thread A   Thread B   Thread C
```

App Server 以进程形式暴露可恢复的 Thread、Turn 和 Item 生命周期，同一进程可同时加载多个 Thread；协议采用双向 JSON-RPC，客户端可以发请求，服务端也可以主动发送事件与 Approval Request，并在客户端返回决策后继续或拒绝相关工作。[OpenAI：Codex App Server](https://learn.chatgpt.com/docs/app-server)

它支持的交互已经不再是简单：

```text
request → response
```

而更像：

```text
client
  ↓
thread/start

server
  ↓
thread.started

server
  ↓
item.started

server
  ↓
item.updated

server
  ↓
approval/request

client
  ↓
approve

server
  ↓
item.completed

server
  ↓
turn.completed
```

这才是真正的 Agent Runtime Protocol。

---

# 十一、这也是 Codex Harness 这次开放最重要的地方

过去 Codex 更多给人的感觉是：

```text
一个非常强的 Coding Agent
```

现在 OpenAI 想表达的是：

```text
Codex App
Codex CLI
Codex IDE
```

其实只是：

```text
三个 Codex Client
```

它们背后共同存在：

```text
             Codex Harness

         Agent Loop
         Context
         Tools
         Sandbox
         Approval
         Thread
         Persistence
         Events
```

因此真正值得复用的不一定是：

> Codex 的聊天界面。

而是：

> **Codex 背后的 Agent Runtime。**

这也是 OpenAI 8 月 19 日文章标题中特意使用：

> **Codex as a platform**

而不是：

> Codex CLI 2.0

的原因。

---

# 十二、那么 Codex Harness 和 Headless Codex CLI 到底有什么区别？

理解完 Harness 之后，这个问题就容易回答了。

首先：

> **Headless Codex CLI 本身就在使用 Harness。**

所以不能理解成：

```text
Harness
VS
Headless CLI
```

更准确的是：

```text
                  Codex Harness
                       │
             ┌─────────┼─────────┐
             ↓         ↓         ↓
           TUI      codex exec  App Server
                       ↑
                   Headless CLI
```

而且当前官方接口已经让不同 Surface 共享同一套生命周期语义：`codex exec --json` 会输出 `thread.started`、`turn.*` 与 `item.*` 事件；App Server 也以 Thread、Turn、Item 和事件流为核心原语；CLI TUI 还可以通过 Remote 模式直接连接 App Server。这里能够确认的是**接口与生命周期语义趋同**，不再把未由当前官方文档直接证明的内部实现细节写成既定事实。[OpenAI：Non-interactive mode](https://learn.chatgpt.com/docs/non-interactive-mode)；[OpenAI：Codex App Server](https://learn.chatgpt.com/docs/app-server)

所以真正应该比较的是：

> **Headless CLI 的 Harness 使用方式，与 App Server 的 Harness 使用方式有什么区别？**

---

# 十三、Headless CLI 的本质：Agent as a Job

假设 CI 失败：

```text
Pipeline Failed
      ↓
codex exec
      ↓
Analyze Logs
      ↓
Read Code
      ↓
Run Tests
      ↓
Generate Fix
      ↓
Exit
```

从 Jenkins 或 CI Engine 看：

Codex 仍然只是一个 Process。

平台只需要理解：

```text
stdin
stdout
stderr
exit code
```

整个 Agent 被包装成：

> **一个 CI Job。**

因此可以称之为：

# Agent as a Job

这种模式最大的优点就是简单。

---

# 十四、Headless 并不代表 Stateless

这里还有一个非常重要的区别。

很多人会认为：

```text
codex exec
=
一次性 Prompt
```

实际上并不是。

Codex 本身已经拥有 Thread，因此：

```text
Run 1

Pipeline Failed
     ↓
codex exec
     ↓
Thread A
     ↓
Analyze
```

之后可以：

```text
Run 2

New CI Result
     ↓
resume Thread A
     ↓
Continue
```

于是完全可以形成：

```text
Agent Orchestrator
      │
      ├── exec
      │     ↓
      │   Turn 1
      │
      ├── resume
      │     ↓
      │   Turn 2
      │
      └── resume
            ↓
          Turn 3
```

所以：

> **Headless 不等于 Stateless。**

这意味着很多 Agent Workflow，其实只需要：

```text
codex exec
+
Thread ID
+
Orchestrator
```

就能完成。

---

# 十五、真正的区别是：谁控制生命周期

这是两种模式最大的架构差异。

Headless CLI：

```text
Your System
    ↓
Start Process
    ↓
Codex
    ↓
Agent Run
    ↓
Process Exit
```

生命周期主要由 CLI Process 控制。

而 App Server：

```text
Your Agent Platform
       │
       ├── create Thread
       ├── resume Thread
       ├── interrupt Thread
       ├── append Turn
       ├── receive Event
       ├── approve Action
       └── archive Thread
               ↓
           App Server
               ↓
            Harness
```

此时：

> **Agent 生命周期已经成为你应用的一部分。**

这就是：

# Agent as a Runtime

---

# 十六、什么时候 Headless CLI 已经足够？

如果一个 Agent Task 满足：

```text
明确输入
+
Agent 能自主执行
+
最后返回明确结果
```

Headless CLI 往往是最好的选择。

例如 CI/CD 中：

- CI Failure Analysis；
- PR Review；
- Test Failure Analysis；
- Lint 自动修复；
- Build Performance Analysis；
- Artifact Inspection；
- Dependency Migration；
- Patch Generation；
- RCA Generation。

这些任务都是：

```text
Input
 ↓
Agent
 ↓
Output
```

没有必要为了它建设一个长期运行的 Agent Service。

因此在 CI/CD Agent 的早期阶段：

> **CLI First 是非常合理的工程策略。**

---

# 十七、什么时候 CLI 会开始变得别扭？

假设 Self-Healing Agent 开始这样工作：

```text
CI Failed
   ↓
Analyze
   ↓
Fix
   ↓
Trigger Build
   ↓
等待 20 分钟
   ↓
Build Failed
   ↓
Continue
```

其实：

```text
exec
+
resume
```

仍然可以很好地解决。

但是继续演进：

```text
Agent
 ↓
发现需要修改生产配置
 ↓
Request Approval
 ↓
等待 2 小时
 ↓
用户在 IM 点击 Approve
 ↓
继续 Agent
 ↓
触发 Pipeline
 ↓
等待 30 分钟
 ↓
收到 Pipeline Event
 ↓
继续
 ↓
创建 MR
 ↓
等待 Review
 ↓
收到 Review Comment
 ↓
继续修复
```

这时候 CLI 外面就会逐渐出现：

```text
Session Manager
State Machine
Event Bus
Approval Manager
Process Manager
Thread Store
Audit
Agent UI
```

最终你会发现：

> **你实际上正在 CLI 外部重新实现一个 Agent Control Plane。**

这时候才真正应该考虑 App Server / Harness Integration。

---

# 十八、Headless CLI 和 App Server 的区别可以总结成一张表

| 能力 | `codex exec` | App Server / Harness |
|---|---|---|
| Agent Loop | ✅ | ✅ |
| Context / Compaction | ✅ | ✅ |
| Tools | ✅ | ✅ |
| Shell / File | ✅ | ✅ |
| Sandbox | ✅ | ✅ |
| Skills / MCP | ✅ | ✅ |
| Thread | ✅ | ✅ |
| Resume | ✅ | ✅ |
| 一次性 CI Job | **非常适合** | 能做，但偏重 |
| 长生命周期 Agent | 可通过外部编排实现 | **天然适合** |
| 多 Thread 管理 | 外部系统负责 | ✅ |
| 双向事件 | 主要依赖 CLI 输出 | ✅ |
| Approval UX | 较笨重 | ✅ |
| Agent Interrupt | 外部 Process 控制 | ✅ Runtime API |
| 实时 UI | 需要解析日志/JSON | ✅ |
| 大量 Session | 不理想 | ✅ |
| Agent Platform | 外部自己建设 | **天然面向此场景** |

所以区别并不是：

> 哪一个 Agent 更聪明。

而是：

> **你希望以一个 Process 的方式管理 Agent，还是以 Runtime Object 的方式管理 Agent。**

---

# 十九、对 CI/CD Agent 来说，这个区别尤其重要

今天很多所谓 CI Agent 的架构其实还是：

```text
Pipeline
   ↓
LLM
```

稍微进一步：

```text
Pipeline
   ↓
Agent
   ↓
Shell / API
```

但 Codex Harness 代表的下一阶段可能是：

```text
                    CI/CD Control Plane

              Pipeline / Build / MR
              Artifact / Deploy
              Observability
                     │
                     ↓
                Agent Runtime
                     │
            ┌────────┼────────┐
            ↓        ↓        ↓
        Thread A Thread B Thread C
            │        │        │
            ↓        ↓        ↓
          Build     Git     Deploy
```

这时候 Agent 不再属于某一个 Pipeline。

它可以跨越：

```text
Commit
Build
Test
Artifact
Deploy
Observability
MR
IM
```

持续完成一个工程目标。

---

# 二十、Thread / Turn / Item 可以直接映射 CI 自愈

例如：

```text
Thread
=
一次 CI Incident 生命周期
```

内部：

```text
Thread #CI-24831
│
├─ Turn 1：分析问题
│
│   ├─ Read build logs
│   ├─ Query commits
│   ├─ Analyze stacktrace
│   └─ Hypothesis
│
├─ Turn 2：第一次修复
│
│   ├─ Modify BUILD
│   ├─ Run test
│   └─ Failed
│
├─ Turn 3：第二次修复
│
│   ├─ Modify dependency
│   ├─ Run test
│   └─ Passed
│
└─ Turn 4：提交
    ├─ Generate diff
    ├─ Approval
    └─ Create MR
```

于是很多企业真正关心的问题天然就有了数据结构：

```text
Agent 为什么这样修改？

尝试过什么？

失败了几次？

调用过哪些工具？

哪个步骤人工批准？

花了多少钱？

耗时多久？

谁最终承担责任？
```

这比只保存一个：

```text
messages[]
```

强得多。

---

# 二十一、对 CI/CD Agent 更合理的演进路线

因此在 CI/CD 智能化建设中，并不建议一开始就建设巨大的：

```text
Multi-Agent Platform
Agent Runtime
Agent Scheduler
Agent Gateway
Agent Memory
Agent UI
```

更合理的是分三阶段。

---

## Stage 1：Agent as a Job

```text
Pipeline
   ↓
codex exec
   ↓
Agent Task
```

目标：

> 先验证 AI 是否真的能解决业务问题。

例如：

```text
CI Diagnosis
Artifact Inspection
Test Analysis
PR Review
```

---

## Stage 2：Agent Workflow

```text
                  Orchestrator
                       │
          ┌────────────┼────────────┐
          ↓            ↓            ↓
        exec         resume       resume
          │            │            │
        Turn 1       Turn 2       Turn 3
```

开始实现：

- 多轮处理；
- Thread 持久化；
- 等待 Pipeline；
- 根据新结果继续；
- 自动修复。

形成真正：

> **Self-Healing Loop。**

---

## Stage 3：Agent Runtime Platform

当组织出现大量长期 Agent Workflow：

```text
              CI/CD Agent Control Plane
                         │
                         ↓
                   Agent Harness
                         │
          ┌──────────────┼──────────────┐
          ↓              ↓              ↓
      CI Agent       Release Agent   SRE Agent
          ↓              ↓              ↓
      Thread A        Thread B        Thread C
```

这个阶段才真正需要统一建设：

```text
Session
Policy
Approval
Observability
Audit
Agent UI
Runtime
Multi-Agent
```

---

# 二十二、这次 Codex Harness 开放最值得关注的并不是 CLI

回过头看 OpenAI 这次开放 Harness，真正重要的并不是：

> 又多了几个 API。

而是 OpenAI 正在重新定义：

> **Agent 产品应该如何构建。**

过去开发 Agent，经常从：

```text
调用模型 API
```

开始。

然后自己实现：

```text
Tool Loop
Memory
Sandbox
Approval
Retry
Context
Session
Events
Persistence
```

现在 Codex 的思路是：

```text
Your Product
     ↓
Agent Harness
     ↓
Model
```

应用重点关注：

```text
业务上下文
企业数据
Tools
UI
Workflow
Policy
```

Harness 负责：

```text
Agent Runtime
```

这是一种非常明显的基础设施分层。

---

# 二十三、从长期看，Harness 甚至可能成为模型之上的稳定层

过去：

```text
Application
    ↓
Model API
```

未来越来越可能变成：

```text
Application
      ↓
Agent Harness
      ↓
Model
```

Harness 掌握：

```text
Context
State
Memory
Tools
Execution
Policy
Approval
Observability
```

模型负责：

```text
Reasoning
Planning
Decision
```

如果这个趋势继续发展，未来企业选择 Agent 技术栈时，问题可能逐渐从：

> 用 GPT、Claude 还是 Gemini？

变成两个问题：

```text
我们选择什么模型？
+
我们选择什么 Agent Runtime？
```

而 Codex Harness 正是在争夺第二个位置。

---

# 二十四、最后如何理解 Codex Harness 与 Headless CLI

可以用一个非常简单的类比。

Codex Harness 是：

> **Agent Engine**

而：

```text
codex exec
```

是：

> **Agent Engine 的 Batch Mode。**

类似：

```text
Container Runtime
      ↑
 docker run
```

`docker run` 完全可以启动一个很强的容器。

但当需求变成：

```text
大量实例
长期生命周期
状态管理
策略
事件
调度
控制
```

就需要更高层的 Control Plane。

Agent 也是一样。

因此：

```text
codex exec
=
Agent as a Job
```

而：

```text
App Server / Harness Integration
=
Agent as a Runtime
```

---

# 结语

2026 年 8 月 Codex Harness 的平台化，真正值得关注的并不是“OpenAI 又开源了一些 Codex 代码”，而是 OpenAI 正式把自己构建 Coding Agent 时形成的 Agent Runtime 抽象出来。

它包含的不只是模型调用，而是：

```text
Agent Loop
Context
Compaction
Thread
Tools
Sandbox
Approval
Persistence
Events
```

并且通过：

```text
codex exec
Codex SDK
Codex App Server
```

提供不同深度的集成能力。OpenAI 8 月 19 日的平台文章把脚本、CI Job 和一次性后台任务映射到 `codex exec`，把需要启动、恢复或流式消费任务的应用代码映射到 SDK，把持久会话、流式事件和 Approval 控制映射到 App Server；当前专项文档同时说明 SDK 也适合 CI/CD 编排，而 `codex exec` 适合直接嵌入 Pipeline 的非交互任务。因此真正边界不是“CI 与非 CI”，而是 **Shell 中的有界 Job、程序化 Thread 控制，以及产品级生命周期控制**。[OpenAI：Codex as a platform](https://developers.openai.com/blog/codex-as-a-platform)；[OpenAI：Non-interactive mode](https://learn.chatgpt.com/docs/non-interactive-mode)；[OpenAI：Codex SDK](https://learn.chatgpt.com/docs/codex-sdk)；[OpenAI：Codex App Server](https://learn.chatgpt.com/docs/app-server)

对于 CI/CD 来说，这件事情尤其值得关注。

因为它代表了两个完全不同的智能化阶段：

```text
第一阶段

在 CI/CD 中运行 Agent
        ↓
   codex exec
        ↓
Agent as a CI Job
```

以及：

```text
第二阶段

让 CI/CD 自身拥有 Agent Runtime
        ↓
 Codex Harness / App Server
        ↓
Agent as a Platform Capability
```

真正的 Agentic CI/CD，很可能并不是：

> **在每条 Pipeline 中增加一个 AI 节点。**

而是：

> **让 CI/CD 平台自身拥有一套长期存在、可恢复、可观察、可审批、可审计并能够跨越 Build、Test、Artifact、Deploy 和 Incident 生命周期的 Agent Runtime。**

这可能才是 Codex Harness 开放之后，对软件工程基础设施最值得关注的长期影响。

---

## 官方来源与核验边界

- [Codex as a platform: build on the open agent harness](https://developers.openai.com/blog/codex-as-a-platform)，发布于 2026-08-19，访问于 2026-08-24。
- [Codex App Server](https://learn.chatgpt.com/docs/app-server)，访问于 2026-08-24。
- [Non-interactive mode](https://learn.chatgpt.com/docs/non-interactive-mode)，访问于 2026-08-24。
- [Codex SDK](https://learn.chatgpt.com/docs/codex-sdk)，访问于 2026-08-24。
- [Open Source](https://learn.chatgpt.com/docs/open-source)，访问于 2026-08-24。

本文将“Codex Harness 是可复用 Agent Runtime”“三种入口代表不同控制粒度”视为基于官方机制的分析推断，不写成 OpenAI 的产品承诺；对 CI/CD 平台长期演进的判断属于企业架构推演，也不代表 Codex 当前已经原生提供跨 Build、Test、Artifact、Deploy 与 Incident 的完整控制面。
