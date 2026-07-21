# CICDInsight 项目作业说明

## 项目的三个核心作用

1. **批量洞察，形成观点。** 面向多公司、行业趋势或多个 CI/CD 阶段，批量收集、排重和归类信息，形成跨维度判断与决策结论。
2. **针对单一对象进行深度洞察。** 围绕某一公司、某一种功能、技术、治理问题或端到端场景，建立问题树、证据矩阵和专题报告，并按需补充案例与实验。
3. **基于深度洞察形成汇报思路。** 从已经完成且适合汇报的 Deep Dive 中提炼页面主张、作业流、控制边界和企业启示，不在 Presentation 中重新发明事实。

默认受众是 CTO、研发效能负责人和平台工程负责人。研究起点是编码完成后的代码检查、门禁、构建、制品、部署、发布和恢复流程，不单独复述传统 CI/CD。

## 任务路由

开始工作前先选择一种主作业流，不把三种任务混成一条线性流程。

| 任务信号 | 主作业流 | 主要输出 |
|---|---|---|
| 多公司、全景扫描、趋势更新、阶段对比、批量来源 | 批量洞察 | `00_sources/`、`10_summaries/`—`40_summaries/`、`90_report/` |
| 单一公司、功能、技术、协议、治理问题或具体场景 | 深度洞察 | `50_deepdives/<topic>/`，必要时衍生 `60_tutorials/` |
| 汇报、PPT、页面主张、叙事或演讲结构 | 汇报思路 | `80_presentations/`，前提是存在可用 Deep Dive |

如果 Presentation 选题没有对应 Deep Dive，先将页面标为 `blocked-by-deep-dive`，再完成或补齐专题研究；不能直接用一个 Source Brief 或公司总结生成正式页面。

## 目录所有权

- `00_sources/`：三条作业流共用的一手资料、Source Brief 和事实证据。
- `05_case_library/`：可跨批量洞察和专题复用的规范案例，是共享资产，不是每条流程的必经步骤。
- `10_summaries/`—`40_summaries/`：批量洞察的工具、公司、阶段和横向分析视图。
- `50_deepdives/`：单一公司、功能、技术或场景的分析事实源。
- `60_tutorials/`：Deep Dive 的可选实践衍生物，不构成独立主作业流。
- `80_presentations/`：汇报叙事、页面主张、作业流和来源映射，不维护独立研究事实。
- `90_report/seven-dimension-analysis.md`：批量洞察的交叉分析框架和候选观点工作台。
- `90_report/README.md`：批量洞察形成的最终跨行业观点和决策结论。
- `outputs/`：完成渲染的演示成品；是否入库按具体交付任务决定。

## 作业流一：批量洞察

`范围与观察窗口 → 批量收集 → 排重与 Source Brief → 维度归类 → 交叉分析 → 形成观点 → 主报告`

完成条件：

- 记录研究范围、观察窗口、来源数量和排重口径；
- 新事实能回链到 `00_sources/`，案例事实能回链到 Source Brief；
- 根据变化更新一个或多个 `10_summaries/`—`40_summaries/` 视图；
- 在七维分析中检验跨维度关系，在 `90_report/README.md` 中形成少量明确观点；
- 重要观点包含反例或限制、置信度和 `as_of`；
- 批量洞察不要求为每个公司或案例创建 Deep Dive。

## 作业流二：深度洞察

`Charter → Question Tree → Evidence Map → 案例/实验（按需）→ Findings → 专题报告 → Presentation-ready 判断`

必需交付物：

- `README.md`、`00_charter.md`、`10_question-tree.md`、`20_evidence-map.md`、`50_findings.md`、`90_report.md`。

按需交付物：

- `30_case-map.md`：需要跨案例比较时；
- `40_labs/`：能够或必须通过学习、配置、复现验证时；
- `60_playbook.md`：需要给出企业试点和治理建议时；
- `60_tutorials/`：配置或实践细节具有复用价值时；
- `assets/`：需要图片、截图或附件时。

专题完成后在 README Frontmatter 标记 `presentation_ready: true/false`。该字段只表示能否支撑汇报，不等于研究质量或产品成熟度。

## 作业流三：汇报思路

`选择 presentation-ready Deep Dive → Deck Brief → Slide Outline → 单页主张 → Source Map → 内容评审 → 成品`

完成条件：

- 每个正式公司页或功能页至少有一个 `presentation_ready: true` 的 Deep Dive 作为主要分析来源；
- 每页只有一个主张，并能由 Deep Dive 中的机制、证据和边界直接证明；
- Source Map 以 Deep Dive 为主入口，分类总结和 Source Brief 作为辅助证据；
- 产品状态、自治等级、成熟度、证据强度和时间点均已核对；
- Presentation 只做压缩、比较和叙事；发现事实缺口时先返回 Deep Dive，而不是直接补写页面。

## 增量更新规则

- 批量研究中发现的新洞察，更新 `00_sources/`、相关分类总结和主报告；只有需要单对象深研时才创建或更新 Deep Dive。
- Deep Dive 中发现的新洞察，先更新专题；若它改变已有页面主张、作业流、状态、控制边界或企业启示，再同步 Presentation。
- Presentation 制作中发现的新问题，回到对应 Deep Dive 补证据；没有专题则将页面保持阻塞。
- 跨专题结论只有在改变批量观点时才回流 `90_report/`，不要求每次专题更新都修改主报告。

## 写作与证据约束

- 优先使用官方文档、规范、源代码、官方工程实践和原始研究。
- 明确区分 GA、Preview、Beta、EA、Experimental、Research 和 Roadmap。
- 厂商指标和案例标注为厂商自述或第一方研究，不外推为行业平均值。
- MCP、CLI/API 或 Tool 可调用不等于获得授权；Agent 自我判断不能替代外部 Test、Scan、Policy、Signature、SLO 或人工 Oracle。
- 维护现有 Obsidian Frontmatter、Wikilink 和目录分层；更新时同步 `as_of`、状态与置信度。

## Git 协作规则

- 所有项目变更最终直接进入 `main` 分支，不新建额外的功能分支、`codex/*` 分支或临时研究分支。
- 如果任务要求提交或推送，直接提交并推送到 `main`，无需创建 Pull Request。
- 开始工作前检查当前分支；若不在 `main` 且工作区存在未提交变更，不得丢弃或覆盖已有内容，应先安全迁移变更或向用户说明当前状态。
