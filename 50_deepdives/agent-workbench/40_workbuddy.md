---
title: WorkBuddy 产品功能详章
tags:
  - research/agentic-cicd
  - research/product-facts
  - topic/agent-workbench
status: complete
as_of: 2026-08-08
confidence: high
---

# WorkBuddy 产品功能详章

## 产品状态与入口

- WorkBuddy 4.5.0 的官方更新日志记录 2026-03-04 “正式发布”。专家团、Skill、连接器、项目和自动化等后续能力页面未分别标注 GA、Preview 或 Beta，因此本章将它们记为“官方公开文档能力，单项阶段未声明”。[更新日志](https://www.workbuddy.cn/docs/workbuddy/Changelog)
- 新任务栏是本地 AI 工作台入口。用户可选择默认、Plan 或 Ask 模式，设置工作目录、模型、Skill、连接器和权限，并同时运行多个相互独立的任务。[新建任务](https://cloud.tencent.com/document/product/1831/134391)
- 云端助手用于远程运行；自动化用于周期任务。两者的执行条件、身份和可用工具按各自页面记录，不能由本地任务能力推断。[产品页](https://cloud.tencent.com/product/workbuddy)

## 配置对象

| 对象 | 官方公开的配置或行为 |
|---|---|
| Task | Prompt、工作目录、模型、Skill、连接器、权限模式和运行状态 |
| Project | 项目指令、资料、连接器、专家、Skill 与任务；项目配置可注入新任务 |
| Expert | 名称、人设、方法论、工具链与适用任务 |
| Expert Team | 团长与多个专家；团长拆解、分配、并行执行并整合交付 |
| Skill | 可执行脚本和工作流包；支持导入、查找、创建、启用和停用 |
| Connector | OAuth 或 API Key、MCP Server/CLI、工具过滤和超时等接入配置 |
| Automation | 任务名称、Prompt、日程、工作目录、模型、Skill、状态与结果位置 |

项目、专家、Skill 和连接器是不同对象。专家描述角色与方法；Skill 封装可执行能力；连接器接入外部服务；专家团描述多专家协作。[专家](https://cloud.tencent.com/document/product/1831/134393)、[技能](https://cloud.tencent.com/document/product/1831/134432)、[连接器](https://cloud.tencent.com/document/product/1831/134525)

## 输入、上下文与任务状态

- 任务对话支持补充需求、上传文件或图片、查看中间步骤、停止任务后继续，以及从详情面板查看产物和文件变更。[任务对话](https://www.workbuddy.cn/docs/workbuddy/Conversation)
- 任务管理页按日期和状态组织任务。官方列出的状态包括进行中、已完成、失败、待处理、规划中和已归档；这些是 WorkBuddy 任务状态，不等于 Pipeline Job 或发布状态。[任务管理](https://www.workbuddy.cn/docs/workbuddy/Task-Management)
- Project 可把指令、资料、连接器、专家和 Skill 作为项目上下文。任务产物可保存到项目资料库，并作为后续任务的检索上下文。[项目](https://www.workbuddy.cn/docs/workbuddy/From-Beginner-to-Expert-Guide/Function-Description/Project)
- 多个独立任务有隔离的工作区和上下文。官方没有公开该隔离的底层容器、进程或租户实现。

## Agent 协作

- Expert 由“人设 + 方法论 + 工具链”组成；Expert Team 由多名专家与团长组成。
- 团长自动拆解任务、协调多位专家并行执行并整合结果。官方页面没有公开子任务依赖图、重试、失败补偿、冲突解决或跨专家文件隔离语义。[专家](https://cloud.tencent.com/document/product/1831/134393)
- 官方说明专家团通常产生多轮、多模型交互，并给出约为单专家 3—5 倍的积分消耗提示。该数字是厂商产品说明，不是任务质量或行业成本基准。

## Skill、连接器与授权

- Skill 可以包含脚本、工作流、领域知识和工具指令；安装或启用 Skill 不等于获得额外系统权限。Skill 以当前用户身份在已授权范围内工作。[技能](https://cloud.tencent.com/document/product/1831/134432)
- 连接器可采用 `MCP + CLI` 或 `Skill + CLI`，通过 OAuth 或 API Key 访问外部服务；连接器仅在指令触发时调用，且不能超出已有账户权限。[连接器](https://cloud.tencent.com/document/product/1831/134525)
- 企业连接器配置可包含凭据、MCP Server、工具权限过滤与超时，工具权限可由 Gateway 过滤。[Connector 管理](https://cloud.tencent.com/document/product/1831/134453)
- Project 连接器可采用公共授权或个人授权。公共授权共享票据，个人授权使用成员自己的票据；两者不等同于生产最小权限或职责分离证明。[项目](https://www.workbuddy.cn/docs/workbuddy/From-Beginner-to-Expert-Guide/Function-Description/Project)

## 触发与自动化

- 自动化支持按日程运行周期任务，可设置 Prompt、工作目录、模型和 Skill，并保存结果及发送通知。[自动化](https://cloud.tencent.com/document/product/1831/134399)
- 自动化使用当前登录身份，受频率、持续时间和并发限制；官方页面记录了运行状态和使用日志。
- 当前文档描述的是个人级定时 Prompt 任务，不应改写成企业事件总线或 CI/CD Trigger。

## 产物与交接

- 右侧栏将“产物、工作空间文件、变更、预览”分开展示。产物可包括 PPT、PDF、文档和网页；文件变更支持差异查看。[右侧边栏](https://cloud.tencent.com/document/product/1831/134400)
- 产物可保存到项目资产库，也可由用户下载或继续修改。文件存在或差异可查看，不代表内容质量、测试或发布已经通过。

## 权限、管理与审计

- 默认权限模式会对敏感路径、删除、脚本/命令和网络等操作请求确认；产品也提供 Full Access。确认提示是工作台权限机制，不等于企业生产审批。[权限模式](https://www.workbuddy.cn/docs/workbuddy/From-Beginner-to-Expert-Guide/Function-Description/Permission-Modes)
- 企业专家管理记录专家唯一 ID、版本、草稿/启用状态、可见范围和启停状态；管理员可上传专家包，按成员或部门分发，并使用白名单/黑名单策略。[企业专家管理](https://cloud.tencent.com/document/product/1831/134421)
- 企业专家包支持 MD5/SHA256 完整性校验。校验只能证明包字节一致，不能证明专家内容正确或安全。
- 自动化运行日志和任务历史提供产品内记录；公开文档没有证明这些记录满足所有企业变更审计或不可否认性要求。

## 端到端功能流程

`选择工作目录/Project → 输入目标与文件 → 选择模式、专家/专家团、Skill 和连接器 → 任务规划与执行 → 在对话中补充上下文或停止/继续 → 查看中间步骤、产物、变更和预览 → 保存到项目资料库或下载`

该流程是 WorkBuddy 工作台流程。官方资料没有证明它原生创建 CI/CD Pipeline、执行 Required Check 或批准生产发布。

## 明确限制

- 专家团调度、失败恢复、质量基准和隔离细节未公开。
- 细分功能没有统一生命周期标签。
- Skill、MCP 和连接器的可调用性不等于后端业务授权。
- WorkBuddy 与 CodeBuddy 的编码/研发能力不能互相补全。

## 主要证据入口

- [[00_sources/briefs/2026-tencent-workbuddy-agent-workbench|WorkBuddy Source Brief]]
- [[00_sources/research-agent-workbench-expert-team-2026-08-08|产品景观证据日志]]
