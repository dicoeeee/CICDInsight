---
title: 大模型时代的 CI/CD 基础设施问题树
tags:
  - research/agentic-cicd
  - research/deep-dive
  - research/question-tree
status: complete
as_of: 2026-07-28
topic_id: llm-era-cicd-infrastructure
---

# 问题树

## Q0：基础设施真的“变了”吗？

### Q0.1 什么算变化

- 新产品或新平台能力；
- 既有 API/构建/缓存能力被封装成 Agent 原生接口；
- 同一基础设施因负载、风险或控制目标改变而承担新角色；
- 治理边界从流程约定下沉为技术强制。

### Q0.2 什么不算变化

- 只在既有产品上增加聊天入口；
- 模型能生成 YAML，但平台执行与治理没有改变；
- 路线图或营销页描述，尚无可用产品与状态证据；
- 既有 API 被 Agent 调用，却没有新的身份、授权、审计或反馈闭环。

## Q1：代码仓从“存储和协作工具”变成了什么？

1. Issue、PR、评论和 Workflow 是否成为 Agent 的任务队列与状态机？
2. 仓库是否开始保存 Agent Instructions、Skills、Workflow Prompt 和执行配置？
3. Code Host 是否提供独立 Agent Session、临时环境、日志、预算和审计？
4. Agent 写入是否通过 PR、Safe Output、Service Account 或 Composite Identity 约束？
5. Ruleset、Required Check、CODEOWNERS 和审批能否继续作为 Agent 外部控制？

**待验证假设 H1：** 代码仓正在成为软件工程 Agent 的协作与治理控制面，但 Source of Truth 角色没有改变，Agent 只增加了新的工作负载与身份类型。

## Q2：流水线从“终点门禁”变成了什么？

1. Agent 能否读取构建状态、日志、测试结果并主动触发验证？
2. Agent 是否作为 Pipeline Step 运行，还是只在外部调用 CI？
3. 一次性全量 CI 能否承受多 Agent、多轮迭代的延迟、并发与成本？
4. 是否出现更快、可复用、可暂停的隔离验证环境或 Microbuild？
5. 失败是否被分类后分流到重试、修复、人工接管，而不是全部交给模型？

**待验证假设 H2：** CI 正形成双层结构：高频、低延迟的 Agent 验证内环，以及决定合并/发布的完整确定性外环。

## Q3：构建系统新增了什么，哪些只是旧能力升值？

1. 构建图和影响分析是否为 Agent 提供比日志更深的结构化上下文？
2. 增量构建、远程缓存和远程执行是否因机器生成变更增多而成为成本基础？
3. Agent 沙箱与正式构建环境是否分离？
4. 自愈构建如何限定到 Task、分支、文件范围和最大轮次？
5. 缓存隔离、可复现性和 Provenance 是否能防止 Agent 或租户污染验证结果？

**待验证假设 H3：** 构建系统的主要变化不是“模型参与编译”，而是图、缓存、隔离和可复现性被重组为 Agent 的快速反馈与可信验证底座。

## Q4：制品仓从“二进制存储”变成了什么？

1. Agent 能否查询可信来源、版本、漏洞、许可证、维护状态和组织策略？
2. Agent 能否上传、标记、复制、移动、删除、隔离或晋级制品？
3. 哪些写操作已产品化，哪些仍只允许非破坏性动作或人工激活？
4. SBOM、Provenance、Attestation 和签名如何绑定 Commit、Builder 和 Artifact Digest？
5. 依赖选择前情报、构建后扫描和发布前策略能否形成多层 Oracle？

**待验证假设 H4：** 制品仓正在变成机器可消费的供应链信任系统，并开始出现 Agent 行动面；但可信性、签名和晋级权仍应由外部策略决定。

## Q5：横切治理发生了什么？

1. 如何区分触发人、Agent、执行器、验证器和发布者身份？
2. Token 是否按 Run、Tool、Action、Environment 和 TTL 限定？
3. Secret 是否留在 Broker 或下游确定性 Job，而不是进入模型上下文？
4. 网络、文件系统、进程和 Tool Scope 是否默认拒绝并逐层求交集？
5. 审计能否回答“谁委托、哪个 Agent、调用什么工具、修改哪个 Commit、由谁验证、发布哪个 Digest”？
6. 成本是否能按 Agent Session、CI Run、缓存、推理和 Verified Outcome 归因？

**待验证假设 H5：** 静态 Bot Token 与流水线级授权不足以承接 Agent；基础设施需要任务级委托身份、策略求交、独立验证和完整 Lineage。

## Q6：变化成熟到什么程度？

| 判断对象 | 验收问题 |
|---|---|
| 产品存在性 | 是否有官方文档、版本记录或可检查实现？ |
| 产品状态 | GA、Preview、Beta、Experimental、Roadmap 分别是什么？ |
| 机制完整度 | 是否覆盖上下文、执行、验证、写回、审计和停止条件？ |
| 自治等级 | 只读、建议、PR 写回、自动应用、生产动作分别到哪一级？ |
| 效果证据 | 是否有公开样本、基线、失败率、成本与长期结果？ |
| 行业普遍性 | 是否至少有两家独立平台或一份原始研究支持？ |

## Q7：哪些结论可能被反例推翻？

- Agent 修改 CI/CD 配置仍可能只是少数场景；
- Agent 修复在格式/Lint 外的真实成功率可能很低；
- MCP 只是新接口，底层权限仍完全取决于旧 API 凭据；
- 高并发 Agent 可能放大 Runner、缓存、依赖源和审计成本；
- 更快反馈环境可能不具备正式 CI 的完整门禁和可复现性；
- Provenance 只能证明从何处、如何构建，不能证明制品安全或业务正确；
- 平台集成越深，Agent 上下文越完整，但供应商锁定和集中爆炸半径也越高。
