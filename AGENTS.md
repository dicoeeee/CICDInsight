# CICDInsight 项目作业说明

## 项目目标

本仓库研究 2025 年下半年以来、重点是 2026 年的 Agentic CI/CD 产品、技术与实践。研究起点是编码完成后的代码检查、门禁、构建、制品、部署、发布和恢复流程，不单独复述传统 CI/CD。

默认受众是 CTO、研发效能负责人和平台工程负责人。所有结论应区分产品状态、任务自治等级、证据类型和时间点。

## 交付层级

- `00_sources/`：一手资料、Source Brief 和事实证据。
- `05_case_library/`：可跨专题复用的规范案例。
- `10_summaries/` 至 `40_summaries/`：工具、公司、阶段和横向维度总结。
- `50_deepdives/`：专题问题树、证据矩阵、案例、实验、Findings、Playbook 和完整专题报告。
- `60_tutorials/`：关键配置和使用速查，不重复完整研究论证。
- `80_presentations/`：面向演示的叙事、页面主张、作业流和来源映射。
- `90_report/`：跨维度主报告和决策结论。

## 补充洞察的回流规则

1. 新增或刷新洞察时，默认先补充到最相关的 `50_deepdives/<topic>/`。根据内容更新相应的证据矩阵、案例、实验、Findings、Playbook 或 `90_report.md`，不要只在演示文稿中新增孤立事实。
2. 如果洞察来自新的外部证据，同时在 `00_sources/` 保留可追溯来源；Deep Dive 负责分析，Source Brief 负责事实，不相互替代。
3. 如果没有匹配的专题，先判断是否值得建立新的 Deep Dive。范围尚不充分时，可先在 `50_deepdives/README.md` 登记为 `proposed`，不要强行塞入不相关专题。
4. 完成 Deep Dive 更新后，评估是否影响 Presentation。以下变化可以同步到 `80_presentations/`：
   - 改变某页的唯一主张、论证链或主作业流；
   - 改变产品状态、任务自治等级、成熟度或证据强度；
   - 改变身份、权限、工具、Oracle、Approval、写入或回退边界；
   - 形成对目标受众有直接决策价值的新差异化或企业启示；
   - 使现有页面内容过时、失真或缺少必要来源映射。
5. 同步 Presentation 时，更新对应 `slides/*.md` 和 `source-map.md`；必要时再调整 Deck Brief 或 Slide Outline。Presentation 不建立第二套事实源，每个关键判断必须回链到 Deep Dive、总结或 Source Brief。
6. 如果补充内容只是实现细节、旁支事实或不改变演示主张，可以只更新 Deep Dive，不必修改 Presentation。

## 写作与证据约束

- 优先使用官方文档、规范、源代码、官方工程实践和原始研究。
- 明确区分 GA、Preview、Beta、EA、Experimental、Research 和 Roadmap。
- 厂商指标和案例标注为厂商自述或第一方研究，不外推为行业平均值。
- MCP、CLI/API 或 Tool 可调用不等于获得授权；Agent 自我判断不能替代外部 Test、Scan、Policy、Signature、SLO 或人工 Oracle。
- 维护现有 Obsidian Frontmatter、Wikilink 和目录分层；更新时同步 `as_of` 或状态字段。

## Git 协作规则

- 所有项目变更最终直接进入 `main` 分支，不新建额外的功能分支、`codex/*` 分支或临时研究分支。
- 如果任务要求提交或推送，直接提交并推送到 `main`，无需创建 Pull Request。
- 开始工作前检查当前分支；若不在 `main` 且工作区存在未提交变更，不得丢弃或覆盖已有内容，应先安全迁移变更或向用户说明当前状态。
