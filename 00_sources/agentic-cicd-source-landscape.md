# Agentic CI/CD source landscape

**Evidence window:** 2025-07-01 through 2026-07-14  
**Research focus:** Agent technology applied after coding, from code review through release and production recovery.  
**Evidence policy:** Primary sources only: official documentation, official release notes/engineering blogs, first-party repositories, and original research papers. A vendor claim is recorded as a vendor claim, not independently validated fact.

## Reading guide

Availability labels are normalized as follows:

- **GA / available:** the source explicitly says generally available, available to all eligible customers, or gives production documentation without a preview qualifier.
- **Public preview / beta / early access / private preview:** preserved exactly when the source supplies that status.
- **Roadmap:** announced but not yet available, or explicitly described as planned/coming.
- **Mixed:** the source covers capabilities at different maturity levels; the entry names each material status.

CI/CD stages use the agreed eight-stage model:

1. Code review and quality checks
2. Static analysis, security, dependency and compliance checks
3. Automated testing, quality gates and risk decisions
4. Compile, build and packaging
5. Artifacts, software supply chain and version management
6. Environment preparation, infrastructure and deployment
7. Release strategy, approval and change management
8. Post-release verification, observability, rollback and recovery

## Curated source register

### GitHub and Microsoft

#### S01. GitHub Agentic Workflows are now in technical preview

- **Organization / project:** GitHub, GitHub Next, Microsoft Research, Azure Core Upstream
- **Publication / update date:** 2026-02-13
- **URL:** https://github.blog/changelog/2026-02-13-github-agentic-workflows-are-now-in-technical-preview/
- **Source type:** Official product changelog
- **Availability status:** Technical preview at publication
- **Relevant CI/CD stages:** 1, 2, 3, 4; cross-cutting workflow automation
- **Tool category:** Code repository, CI pipeline, agent orchestration and governance
- **Geography:** United States / global service
- **Key facts:** Natural-language Markdown workflows compile to GitHub Actions YAML; agents can triage issues, review pull requests, analyze CI failures and maintain repositories. Runs are read-only by default, sandboxed, network-isolated and use preapproved safe outputs for writes. GitHub MCP provides repository, issue, PR, Actions and security context.
- **Why it matters:** First-party evidence of an agent runtime being embedded into the established Actions execution and policy plane rather than replacing it.
- **Confidence / limitations:** **High** for architecture and preview status. This announcement supplies examples, not customer outcome measurements.

#### S02. GitHub Agentic Workflows is now in public preview

- **Organization / project:** GitHub Agentic Workflows
- **Publication / update date:** 2026-06-11
- **URL:** https://github.blog/changelog/2026-06-11-github-agentic-workflows-is-now-in-public-preview/
- **Source type:** Official product changelog
- **Availability status:** Public preview
- **Relevant CI/CD stages:** 1, 2, 3, 4; cross-cutting workflow automation
- **Tool category:** Code repository, CI pipeline, agent orchestration and governance
- **Geography:** United States / global service
- **Key facts:** Public preview supports reasoning-based repository tasks such as CI-failure analysis and documentation updates. Markdown definitions compile to standard Actions and reuse runner groups and policy constraints. The source lists integrity filters, read-only defaults, sandboxing, a workflow firewall, safe-output validation and threat detection.
- **Why it matters:** Marks movement from technical to public preview and documents the controls GitHub considers necessary for repository-level autonomous work.
- **Confidence / limitations:** **High** for product state and controls. Customer quotations are first-party testimonials and are not independent evaluations.

#### S03. Agentic workflows no longer need a personal access token

- **Organization / project:** GitHub Agentic Workflows
- **Publication / update date:** 2026-06-11
- **URL:** https://github.blog/changelog/2026-06-11-agentic-workflows-no-longer-need-a-personal-access-token/
- **Source type:** Official product changelog
- **Availability status:** Available across all Copilot plans, within the public-preview feature
- **Relevant CI/CD stages:** Cross-cutting governance for stages 1-8
- **Tool category:** CI pipeline, identity, cost and agent governance
- **Geography:** United States / global service
- **Key facts:** Agentic workflows can use Actions' built-in `GITHUB_TOKEN`, eliminating a long-lived PAT. Organization billing, cost centers, budgets and per-run token caps are documented.
- **Why it matters:** Shows identity and cost management becoming first-class requirements for unattended agent runs.
- **Confidence / limitations:** **High** for the authentication and billing change. It does not evaluate whether scoped tokens prevent every agent-specific threat.

#### S04. Copilot code review now runs on an agentic architecture

- **Organization / project:** GitHub Copilot code review
- **Publication / update date:** 2026-03-05
- **URL:** https://github.blog/changelog/2026-03-05-copilot-code-review-now-runs-on-an-agentic-architecture/
- **Source type:** Official product changelog
- **Availability status:** GA for eligible Copilot plans
- **Relevant CI/CD stages:** 1, 2
- **Tool category:** Code repository and code review
- **Geography:** United States / global service
- **Key facts:** Code review uses agentic tool calling to gather repository context beyond the diff and runs on GitHub Actions. Self-hosted-runner users need a one-time setup.
- **Why it matters:** Concrete GA example of a formerly assistive review feature becoming a tool-using agent tied to CI compute.
- **Confidence / limitations:** **High** for architecture and status. Claims of higher signal and lower noise are product claims without benchmark details in this source.

#### S05. Shape Copilot code review around your team

- **Organization / project:** GitHub Copilot code review
- **Publication / update date:** 2026-06-02
- **URL:** https://github.blog/changelog/2026-06-02-shape-copilot-code-review-around-your-team/
- **Source type:** Official product changelog
- **Availability status:** Public preview for agent skills, MCP support and medium analysis depth
- **Relevant CI/CD stages:** 1, 2, 3
- **Tool category:** Code review, security context, agent tools and governance
- **Geography:** United States / global service
- **Key facts:** Review agents can invoke repository skills and MCP servers for internal standards, service catalog, incident and issue context. Administrators can set review depth by repository; the deeper tier consumes more AI credits. Configuration can be shared with the Copilot cloud agent.
- **Why it matters:** Demonstrates review policy moving from generic model prompts toward organization-owned tools, context and risk-tiered compute.
- **Confidence / limitations:** **High** for preview behavior. No independent accuracy or false-positive measurements are supplied.

#### S06. How Microsoft is migrating repositories to GitHub

- **Organization / project:** Microsoft internal engineering; GitHub and Azure DevOps
- **Publication / update date:** 2026-06-03
- **URL:** https://devblogs.microsoft.com/devops/how-microsoft-is-migrating-repositories-to-github/
- **Source type:** Official first-party engineering case study
- **Availability status:** Current internal practice; Enterprise Live Migrator remains an evolving capability
- **Relevant CI/CD stages:** 1-4, 7; cross-cutting platform and people change
- **Tool category:** Code repository, CI pipeline, developer platform
- **Geography:** United States / multinational internal deployment
- **Key facts:** Microsoft's CAP organization reports migrating more than 1,600 repositories and 3,100 developers in six months while retaining Azure Boards and Azure Pipelines where needed. It reports agentic workflows scanning for security, performance and governance issues and routing remediation to Copilot Coding Agent.
- **Why it matters:** Rare first-party large-enterprise evidence that repository location is being changed to gain agent capabilities while CI/CD remains hybrid.
- **Confidence / limitations:** **Medium-high.** Scale figures and workflow descriptions are first-party and not independently audited; not all complex repositories had migrated.

#### S07. Azure DevOps and GitHub: Journeying into the AI Era

- **Organization / project:** Microsoft Azure DevOps and GitHub
- **Publication / update date:** 2026-06-02
- **URL:** https://devblogs.microsoft.com/devops/azure-devops-and-github-journeying-into-the-ai-era/
- **Source type:** Official product strategy and release blog
- **Availability status:** Mixed: newest agentic capabilities GA/preview on GitHub; Azure DevOps remote MCP preview; Enterprise Live Migration script preview; additional control-plane work announced
- **Relevant CI/CD stages:** 1-4, 7; cross-cutting context and governance
- **Tool category:** Code repository, CI pipeline, MCP and agent control plane
- **Geography:** United States / global services
- **Key facts:** Microsoft states that newest agentic development capabilities ship on GitHub while customers can keep Azure Boards and Pipelines. A hosted, stateless Azure DevOps MCP server exposes work items, builds, PRs and test plans. The post describes an agent control plane for visibility, audit and policy management.
- **Why it matters:** Primary evidence of Microsoft's product split: GitHub as agentic surface, Azure DevOps retained for planning and CI/CD in hybrid adoption.
- **Confidence / limitations:** **High** for stated strategy and statuses; forward-looking integration items may change.

#### S08. Azure Boards integration with GitHub Copilot

- **Organization / project:** Microsoft Azure Boards and GitHub Copilot coding agent
- **Publication / update date:** 2025-12-16
- **URL:** https://devblogs.microsoft.com/devops/github-copilot-for-azure-boards/
- **Source type:** Official product blog
- **Availability status:** GA rollout, continuing into early February 2026
- **Relevant CI/CD stages:** 1, 3; cross-cutting work intake and review
- **Tool category:** Work management, code repository, coding agent
- **Geography:** United States / global services
- **Key facts:** Azure Boards work items can be sent to the GitHub coding agent, which works in a GitHub repository and produces a pull request. Model selection was added; REST-triggered fully automated workflows were described as planned.
- **Why it matters:** Documents the shift from a human handoff between planning and code review to an asynchronous agent handoff with a PR as the review boundary.
- **Confidence / limitations:** **High** for GA workflow. REST automation is roadmap, not available capability in this source.

#### S09. Azure Copilot Observability Agent is generally available, with autonomous operations in preview

- **Organization / project:** Microsoft Azure Monitor / Azure Copilot Observability Agent
- **Publication / update date:** 2026-06-23
- **URL:** https://techcommunity.microsoft.com/blog/azureobservabilityblog/azure-copilot-observability-agent-is-generally-available-with-autonomous-operati/4528213
- **Source type:** Official product blog
- **Availability status:** Core Observability Agent GA; autonomous operations public preview
- **Relevant CI/CD stages:** 7, 8
- **Tool category:** Observability, SRE, incident investigation
- **Geography:** United States / global Azure service
- **Key facts:** The GA agent grounds investigations in Azure Monitor data and produces explainable reasoning and next steps. Autonomous operation remains preview; engineers retain responsibility for approvals and environmental changes.
- **Why it matters:** Clearly separates GA analysis from preview autonomy and preserves a human approval boundary for production changes.
- **Confidence / limitations:** **High** for product state. No controlled outcome data is provided.

### GitLab

#### S10. GitLab Duo Agent Platform general availability

- **Organization / project:** GitLab Duo Agent Platform
- **Publication / update date:** 2026-01-15
- **URL:** https://about.gitlab.com/press/releases/2026-01-15-gitlab-announces-duo-agent-platform-general-availability/
- **Source type:** Official product announcement
- **Availability status:** GA for Premium and Ultimate on GitLab.com and Self-Managed; Dedicated planned for 18.8 at announcement
- **Relevant CI/CD stages:** 1-4, 7; cross-cutting orchestration and governance
- **Tool category:** DevSecOps platform, CI pipeline, agent catalog and orchestration
- **Geography:** United States / global service
- **Key facts:** GA includes agentic chat, foundational/custom/external agents and multi-agent flows. Named flows include Code Review, Fix CI/CD Pipeline and Convert to GitLab CI/CD. The platform documents model selection, group access control and usage/activity visibility.
- **Why it matters:** One of the first full-lifecycle DevSecOps platforms to declare its agent orchestration layer GA.
- **Confidence / limitations:** **High** for offering and status. Customer and productivity quotations are vendor-selected and should not be treated as comparative evidence.

#### S11. GitLab 18.11: automated security remediation, pipeline setup and delivery analytics

- **Organization / project:** GitLab 18.11 / Duo Agent Platform
- **Publication / update date:** 2026-04-14 (announcement page posted 2026-04-16)
- **URL:** https://about.gitlab.com/press/releases/2026-04-16-gitlab-extends-agentic-ai-with-new-automated-security-remediation-pipeline-setup-delivery-analytics/
- **Source type:** Official release announcement
- **Availability status:** Mixed: Agentic SAST Vulnerability Resolution GA; Data Analyst Agent GA; CI Expert Agent beta
- **Relevant CI/CD stages:** 1, 2, 3, 4
- **Tool category:** Security remediation, CI pipeline creation, delivery analytics
- **Geography:** United States / global service
- **Key facts:** SAST findings can trigger a root-cause fix and ready-to-merge MR with confidence score. The beta CI Expert inspects language/framework and proposes build-and-test configuration. Data Analyst answers questions about MR cycle time, pipeline health and deployment frequency.
- **Why it matters:** Joins security repair, pipeline creation and delivery measurement in the same platform context.
- **Confidence / limitations:** **High** for feature state; the source does not give comparative fix-quality or pipeline-success benchmarks.

#### S12. GitLab 19.0 extends intelligent orchestration

- **Organization / project:** GitLab 19.0
- **Publication / update date:** 2026-05-21
- **URL:** https://about.gitlab.com/press/releases/2026-05-21-gitlab-19-extends-intelligent-orchestration-to-close-the-gap-between-writing-code-and-shipping-it/
- **Source type:** Official release announcement
- **Availability status:** Mixed: Developer Flow expansion released; Secrets Manager public beta; additional self-hosted model support released
- **Relevant CI/CD stages:** 1, 2, 3, 5, 7
- **Tool category:** Merge workflow, secrets, supply chain, agent orchestration
- **Geography:** United States / global service
- **Key facts:** Developer Flow handles reviewer feedback, conflict resolution and rebase-and-merge. Secrets Manager scopes credentials to authorized jobs and links audit records to pipelines. Components Analytics inventories CI/CD catalog component versions; self-hosted Duo adds more open models.
- **Why it matters:** Shows agent workflows becoming dependent on credential scoping, component inventory and regulated/self-hosted execution choices.
- **Confidence / limitations:** **High** for announced features; workflow effectiveness is not independently measured.

#### S13. GitLab: Built for the agentic engineering era

- **Organization / project:** GitLab Transcend announcements
- **Publication / update date:** 2026-06-10
- **URL:** https://about.gitlab.com/blog/gitlab-transcend-announcements/
- **Source type:** Official product and roadmap blog
- **Availability status:** Mixed: Duo Agent Platform GA; GitLab Orbit public beta; next-generation SCM and governance for agents private beta
- **Relevant CI/CD stages:** 1-8; cross-cutting context, identity, policy and audit
- **Tool category:** DevSecOps platform, context graph, agent governance, source control
- **Geography:** United States / global service
- **Key facts:** GitLab describes Orbit as a lifecycle context graph and previews agent identity, policy, audit and approval controls. A next-generation source system is positioned for agent-scale concurrency.
- **Why it matters:** Documents the platform primitives GitLab believes are required when agent volume exceeds human-paced repository workflows.
- **Confidence / limitations:** **High** for status labels; performance claims and future plans remain vendor assertions.

#### S14. GitLab Duo Agent Platform complete getting-started guide

- **Organization / project:** GitLab Duo Agent Platform
- **Publication / update date:** 2026-05-27
- **URL:** https://about.gitlab.com/blog/gitlab-duo-agent-platform-complete-getting-started-guide/
- **Source type:** Official product guide
- **Availability status:** Mixed: foundational agents/flows released; custom flows beta; status varies by feature
- **Relevant CI/CD stages:** 1-4, 7; cross-cutting agent administration
- **Tool category:** Agent catalog, orchestration, MCP, audit and triggers
- **Geography:** United States / global service
- **Key facts:** The guide enumerates agents, flows, event triggers, session logs, an AI catalog and MCP client/server roles. Named security flows cover SAST vulnerability resolution and SAST/secret false-positive detection.
- **Why it matters:** Useful first-party inventory of platform objects and the operational surface used to administer agent work.
- **Confidence / limitations:** **High** for product vocabulary and documented features; a guide is not evidence of adoption or effectiveness.

### Harness

#### S15. Worker Agents

- **Organization / project:** Harness Autonomous Worker Agents
- **Publication / update date:** 2026-07-08
- **URL:** https://developer.harness.io/docs/platform/harness-ai/harness-agents/
- **Source type:** Official product documentation
- **Availability status:** Available to accounts with AI Agents enabled; access may require administrator/support enablement
- **Relevant CI/CD stages:** 1-8
- **Tool category:** CI/CD pipeline, agent runtime, MCP and governance
- **Geography:** United States / global service
- **Key facts:** Reusable agents combine instructions, a model connector and optional MCP servers, then run as an `Agent` step in CI, CD, IaC, security testing, supply-chain security or custom stages. Execution is containerized in an isolated VM on Harness Cloud or customer Kubernetes; definitions are versioned separately from pipeline references.
- **Why it matters:** Direct evidence that an agent can now be a typed, governed pipeline step across the entire delivery lifecycle.
- **Confidence / limitations:** **High** for documented operation. Account enablement means availability is not identical to unrestricted GA access.

#### S16. Harness Code Quality Agents

- **Organization / project:** Harness Code Review, Code Coverage and AutoFix Agents
- **Publication / update date:** 2026-07-02
- **URL:** https://developer.harness.io/3k-docs/platform/getting-started/agents/code-quality/
- **Source type:** Official product documentation
- **Availability status:** Documented product capability; Harness AI must be enabled
- **Relevant CI/CD stages:** 1, 2, 3, 4
- **Tool category:** Code review, testing and CI failure remediation
- **Geography:** United States / global service
- **Key facts:** Code Review posts PR feedback; Code Coverage generates tests and a PR; AutoFix analyzes build failures and proposes changes through a PR. The review flow includes prompt generation, iterative model review and comment posting.
- **Why it matters:** Covers three recurring human bottlenecks—review, coverage and broken builds—with a common PR-based human-control boundary.
- **Confidence / limitations:** **High** for workflow. The page supplies no comparative accuracy, acceptance-rate or failure-rate data.

#### S17. Harness AI Test Automation overview

- **Organization / project:** Harness AI Test Automation
- **Publication / update date:** 2026-07-06
- **URL:** https://developer.harness.io/docs/ai-test-automation/get-started/overview/
- **Source type:** Official product documentation
- **Availability status:** Limited / contact Harness to enable
- **Relevant CI/CD stages:** 3, 7, 8
- **Tool category:** End-to-end testing, multi-agent execution and self-healing tests
- **Geography:** United States / global service
- **Key facts:** Natural-language tests are interpreted against a dynamic UI; specialized navigation and date agents orchestrate execution. The product documents self-healing and CI/CD integration.
- **Why it matters:** Shows test authoring and maintenance moving from scripted selectors to intent-driven, multi-agent execution.
- **Confidence / limitations:** **High** for design and access requirement; production scale and test reliability are not quantified.

#### S18. Harness AI overview

- **Organization / project:** Harness AI
- **Publication / update date:** 2026-07-08
- **URL:** https://developer.harness.io/docs/platform/harness-ai/overview/
- **Source type:** Official product documentation
- **Availability status:** Mixed by module and account entitlement
- **Relevant CI/CD stages:** 1-8
- **Tool category:** Software-delivery AI platform
- **Geography:** United States / global service
- **Key facts:** Harness documents natural-language pipeline creation, failure RCA, automated review, event triggers and autonomous agents inside pipeline steps. The capability map separates DevOps Agent, Worker Agents, rules and delivery modules.
- **Why it matters:** Provides the vendor's current scope boundary for “AI after code” across platform modules.
- **Confidence / limitations:** **High** for inventory; marketing-level breadth must be checked against module-specific documentation before treating a feature as available.

#### S19. Harness June 2026 product updates

- **Organization / project:** Harness Software Delivery Platform
- **Publication / update date:** 2026-07-03
- **URL:** https://www.harness.io/blog/shipped-in-june-2026
- **Source type:** Official product release roundup
- **Availability status:** Mixed; Worker Agents described as available to all Harness customers, while individual June features vary
- **Relevant CI/CD stages:** 1-8
- **Tool category:** CI/CD, testing, security, deployment and engineering analytics
- **Geography:** United States / global service
- **Key facts:** Harness states that pipeline steps for testing, security, deployment and remediation can run as reasoning agents under scoped credentials, OPA policy, approval gates and audit trails. It also adds metrics for agent adoption, AI-committed code, spend and PR cycle time.
- **Why it matters:** Connects agent execution with the existing delivery governance plane and with management metrics for AI-generated work.
- **Confidence / limitations:** **Medium-high.** Feature inventory is first-party; availability wording is broad and outcome claims are not independently validated.

#### S20. Investigator Agent Pipelines

- **Organization / project:** Harness AI Investigator
- **Publication / update date:** 2026-07-02
- **URL:** https://developer.harness.io/3k-docs/ai-sre/ai-agent/investigator-agent-pipelines/
- **Source type:** Official product documentation
- **Availability status:** Early access
- **Relevant CI/CD stages:** 7, 8
- **Tool category:** SRE investigation and custom agent workflows
- **Geography:** United States / global service
- **Key facts:** Teams can add custom investigation pipelines beside the built-in RCA Change Agent, connect internal tools and enrich incidents with domain context. The page says the core flow works while UI builder, marketplace and error-handling productionization are planned.
- **Why it matters:** Separates a functional agent workflow from the operational tooling still required for production maturity.
- **Confidence / limitations:** **High** for early-access boundary. The page contains a Q2 2026 planning reference even though it was updated in July; roadmap timing may be stale.

### AWS

#### S21. AWS DevOps Agent public preview

- **Organization / project:** AWS DevOps Agent
- **Publication / update date:** 2025-12-02; updated 2026-03-31
- **URL:** https://aws.amazon.com/blogs/aws/aws-devops-agent-helps-you-accelerate-incident-response-and-improve-system-reliability-preview/
- **Source type:** Official launch blog
- **Availability status:** Public preview at launch; page updated to note GA on 2026-03-31
- **Relevant CI/CD stages:** 7, 8
- **Tool category:** SRE, incident response, topology and CI/CD correlation
- **Geography:** United States / multiple AWS regions
- **Key facts:** The agent connects observability data with GitHub Actions and GitLab CI/CD deployment context to investigate incidents and recommend preventive improvements. AWS describes long-running autonomous work over hours or days.
- **Why it matters:** Establishes the initial scope and evolution path from preview to GA for AWS's post-deployment agent.
- **Confidence / limitations:** **High** for launch facts; later capabilities should be read from the GA and release-management sources below.

#### S22. AWS DevOps Agent general availability

- **Organization / project:** AWS DevOps Agent
- **Publication / update date:** 2026-03-31
- **URL:** https://aws.amazon.com/blogs/mt/announcing-general-availability-of-aws-devops-agent/
- **Source type:** Official GA announcement
- **Availability status:** GA for production-operations capabilities
- **Relevant CI/CD stages:** 7, 8
- **Tool category:** SRE, incident investigation and prevention
- **Geography:** United States / multiple AWS regions
- **Key facts:** The GA agent learns application relationships and correlates telemetry, code and deployment data across AWS, multicloud and on-premises systems. Integrations include major observability, source-control, ITSM and collaboration tools.
- **Why it matters:** A hyperscaler offering moves agentic incident investigation from preview to a supported service.
- **Confidence / limitations:** **High** for GA scope. The announcement does not establish comparative MTTR improvements across customers.

#### S23. AWS DevOps Agent release management capability

- **Organization / project:** AWS DevOps Agent release management
- **Publication / update date:** 2026-06-17
- **URL:** https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-release-management/
- **Source type:** Official What's New announcement
- **Availability status:** Preview; US East (N. Virginia) at announcement
- **Relevant CI/CD stages:** 1, 2, 3, 6, 7
- **Tool category:** Release readiness, autonomous testing and risk review
- **Geography:** United States; region-limited preview
- **Key facts:** Release readiness evaluates code changes against standards, dependencies and access controls. Release testing generates and runs test plans for web and API applications in customer-provisioned environments. Repository and pipeline connection is required.
- **Why it matters:** Extends AWS's agent from post-deployment incident work into pre-production risk review and testing.
- **Confidence / limitations:** **High** for preview scope. Claims about catching issues humans miss are not accompanied by evaluation data.

#### S24. AWS DevOps Agent What's New

- **Organization / project:** AWS DevOps Agent
- **Publication / update date:** 2026-07-07 (page includes dated changes through 2026-07-07)
- **URL:** https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html
- **Source type:** Official versioned product changelog
- **Availability status:** Mixed by dated feature; core operations GA, release management preview
- **Relevant CI/CD stages:** 1-8, especially 7-8
- **Tool category:** Agent platform, topology, release management, skills and governance
- **Geography:** United States / supported AWS regions
- **Key facts:** June-July entries add custom agents, A2A connections, repository-imported skills, pipeline topology, release-readiness reviews on GitHub/GitLab, public asset APIs and CloudTrail events for asset changes. The page records scope and dates at feature level.
- **Why it matters:** Best primary source for tracing how AWS is adding agent control-plane, context and extensibility capabilities after GA.
- **Confidence / limitations:** **High** for change chronology. Status must be checked per feature because the single page mixes GA, preview and incremental updates.

#### S25. How AWS DevOps Agent uses multi-agent reasoning to find root causes

- **Organization / project:** AWS DevOps Agent
- **Publication / update date:** 2026-05-27
- **URL:** https://aws.amazon.com/blogs/devops/how-aws-devops-agent-uses-multi-agent-reasoning-to-find-root-causes/
- **Source type:** Official architecture / engineering blog
- **Availability status:** GA operations architecture
- **Relevant CI/CD stages:** 7, 8
- **Tool category:** Multi-agent SRE, topology, audit and mitigation planning
- **Geography:** United States / global architecture
- **Key facts:** AWS describes specialist capabilities for triage, multi-hypothesis investigation, mitigation and prevention, all grounded in topology and an investigation journal. CI/CD data links deployments and code changes. The agent recommends mitigation but does not execute production remediation; the operator remains responsible.
- **Why it matters:** Supplies the clearest first-party architecture and human-control boundary for AWS's operational agent.
- **Confidence / limitations:** **High** for stated design. The illustrative incident is explanatory, not a controlled production benchmark.

### Google Cloud and DORA

#### S26. 2025 DORA State of AI-assisted Software Development

- **Organization / project:** DORA / Google Cloud
- **Publication / update date:** 2025-09-23
- **URL:** https://cloud.google.com/blog/products/ai-machine-learning/announcing-the-2025-dora-report
- **Source type:** Primary industry research summary; underlying report based on survey and qualitative research
- **Availability status:** Published research
- **Relevant CI/CD stages:** 1-8; organizational and workflow evidence
- **Tool category:** Software-delivery research and measurement
- **Geography:** Global sample
- **Key facts:** Based on nearly 5,000 survey responses and more than 100 hours of qualitative data. DORA reports positive associations between AI adoption and throughput/product performance but a negative association with delivery stability. It identifies internal platforms, automated testing, version control and fast feedback as amplifiers or safeguards.
- **Why it matters:** Primary empirical counterweight to vendor feature announcements; it measures system-level delivery outcomes and people/platform conditions.
- **Confidence / limitations:** **High** for research provenance; findings are observational associations and do not isolate agentic CI/CD products specifically.

#### S27. Application Design Center now GA

- **Organization / project:** Google Cloud Application Design Center and Gemini Cloud Assist Design Agent
- **Publication / update date:** 2025-12-08
- **URL:** https://cloud.google.com/blog/products/application-development/application-design-center-now-ga/
- **Source type:** Official GA announcement
- **Availability status:** Application Design Center GA; integrated agent capability described as available with the service
- **Relevant CI/CD stages:** 3, 5, 6, 7
- **Tool category:** Infrastructure design, GitOps, deployment and governance
- **Geography:** United States / global Google Cloud service
- **Key facts:** The Design Agent can create deployable infrastructure templates exportable as Terraform. Git changes go through PR review and existing CI/CD builds, tests and deploys app and infrastructure together. GA adds APIs, CLI, VPC Service Controls, BYO Terraform and GitOps support.
- **Why it matters:** Shows an agent generating infrastructure while deterministic PR and pipeline controls remain the promotion path.
- **Confidence / limitations:** **High** for GA features. The post does not isolate the agent's contribution to delivery outcomes.

#### S28. Gemini Cloud Assist at Next '26

- **Organization / project:** Google Cloud Gemini Cloud Assist
- **Publication / update date:** 2026-04-22
- **URL:** https://cloud.google.com/blog/products/application-development/gemini-cloud-assist-at-next26
- **Source type:** Official product announcement
- **Availability status:** Mixed; redesigned capabilities and proactive agents announced, with some components/previews varying
- **Relevant CI/CD stages:** 6, 7, 8
- **Tool category:** Application deployment, cloud operations, troubleshooting and FinOps
- **Geography:** United States / global Google Cloud service
- **Key facts:** Google describes a multi-agent approach spanning application design, deployment, troubleshooting and optimization, with tools for `gcloud`, `kubectl` and Terraform and capabilities exposed through MCP servers.
- **Why it matters:** Places agentic operations above multiple cloud and IaC tools rather than inside Cloud Build alone.
- **Confidence / limitations:** **Medium-high.** The blog mixes available and preview capabilities; component documentation is required before procurement decisions.

#### S29. How Google SRE is deploying agentic AI

- **Organization / project:** Google Site Reliability Engineering
- **Publication / update date:** 2026-05-28
- **URL:** https://cloud.google.com/blog/products/devops-sre/how-google-sre-is-using-agentic-ai-to-improve-operations/
- **Source type:** Official first-party engineering / strategy article
- **Availability status:** Internal deployment and work in progress; not a single commercial-product status
- **Relevant CI/CD stages:** 3, 6, 7, 8
- **Tool category:** SRE agents, reliability design, runbooks and incident response
- **Geography:** United States / Google's global internal systems
- **Key facts:** Google SRE describes agents for investigation, mitigation, reliability design and continuous improvement of playbooks, while explicitly retaining human involvement for higher-risk services and changes. The work frames security, safety and privacy as equivalent requirements for agents and humans.
- **Why it matters:** Rare large-company practice evidence covering people/process changes and the boundary between routine auto-addressing and high-risk review.
- **Confidence / limitations:** **Medium-high.** It is first-party practice guidance; detailed system metrics and deployment coverage are not disclosed.

#### S30. Gemini CLI DevOps Extension

- **Organization / project:** Google Cloud Gemini CLI DevOps Extension / DevOps MCP server
- **Publication / update date:** 2026-05-08
- **URL:** https://cloud.google.com/blog/topics/developers-practitioners/ship-code-within-minutes-with-the-gemini-cli-devops-extension/
- **Source type:** Official technical tutorial and product blog
- **Availability status:** Available extension / reference implementation; production support level not stated
- **Relevant CI/CD stages:** 3, 4, 5, 6, 7
- **Tool category:** CI/CD generation, Artifact Registry, Cloud Build and deployment
- **Geography:** United States / global Google Cloud service
- **Key facts:** An agent proposes a pipeline design, awaits approval, provisions Artifact Registry and repository connectivity, generates `cloudbuild.yaml`, and creates a Cloud Build trigger via typed MCP tools. Actions use the permissions of local Application Default Credentials.
- **Why it matters:** Direct Google example of an agent constructing Cloud Build and deployment infrastructure while a human approves the design.
- **Confidence / limitations:** **High** for the documented example. It is a tutorial, not evidence that arbitrary repositories are production-ready after one run.

#### S31. Cloud Build release notes

- **Organization / project:** Google Cloud Build
- **Publication / update date:** 2026-06-29 (latest page update in evidence window)
- **URL:** https://docs.cloud.google.com/build/docs/release-notes
- **Source type:** Official product release notes
- **Availability status:** GA production updates unless individual entry says otherwise
- **Relevant CI/CD stages:** 4, 5, 6
- **Tool category:** CI build, artifacts, attestations and repository triggers
- **Geography:** United States / global Google Cloud service
- **Key facts:** 2026 updates add build-result data in attestations, generic artifacts, OCI artifacts and least-privilege permission checks for third-party repository connections. The release notes show Cloud Build continuing as the deterministic build and artifact execution layer consumed by agent tooling.
- **Why it matters:** Establishes what changed in Cloud Build itself and helps avoid falsely attributing Gemini/agent capabilities to the underlying CI engine.
- **Confidence / limitations:** **High** for dated platform changes. The final sentence is a scope distinction based on the linked Google DevOps Extension architecture, not a Cloud Build product claim.

### Atlassian / Bitbucket

#### S32. Introducing Agentic Pipelines

- **Organization / project:** Atlassian Bitbucket Cloud Agentic Pipelines
- **Publication / update date:** 2026-04-14
- **URL:** https://www.atlassian.com/blog/bitbucket/introducing-agentic-pipelines-ai-automation
- **Source type:** Official product launch blog
- **Availability status:** Open beta
- **Relevant CI/CD stages:** 1-7
- **Tool category:** Code repository, CI pipeline and agent orchestration
- **Geography:** Australia / United States; global cloud service
- **Key facts:** Agent definitions run as pipeline steps and can be triggered by PR or schedule events. First-party examples cover code review, vulnerability triage/fix, test generation, release notes and failed-pipeline repair. Proposed changes are routed through PR review.
- **Why it matters:** Another major code host embeds noninteractive agents directly inside its existing CI containers and event model.
- **Confidence / limitations:** **High** for beta scope. Examples are reference workflows, not measured production outcomes.

#### S33. Bitbucket Agentic Pipelines documentation

- **Organization / project:** Atlassian Bitbucket Cloud Agentic Pipelines
- **Publication / update date:** 2026 (live documentation observed within scope; beta status current through 2026-07-14)
- **URL:** https://support.atlassian.com/bitbucket-cloud/docs/agentic-pipelines/
- **Source type:** Official product documentation
- **Availability status:** Beta
- **Relevant CI/CD stages:** 1-7
- **Tool category:** CI pipeline, MCP, agent identity and permissions
- **Geography:** Australia / United States; global cloud service
- **Key facts:** Agents run in the build container with the repository, build tools and a Bitbucket MCP server. OAuth scopes control writes; Rovo Dev, Claude and Codex providers are documented. Atlassian explicitly says agent output should not be used as a release gate without human verification; self-hosted runners and multiple agents per step are current limitations.
- **Why it matters:** Unusually explicit primary evidence about where nondeterministic agent output should not replace deterministic release gates.
- **Confidence / limitations:** **High** for current behavior and limitations. The page does not expose an original publication date, so the date field records its current 2026 documentation state rather than a launch date.

#### S34. Rovo Dev Code Reviewer internal evaluation

- **Organization / project:** Atlassian Rovo Dev Code Reviewer
- **Publication / update date:** 2026-04-07
- **URL:** https://www.atlassian.com/blog/artificial-intelligence/developer-productivity-improved-with-rovo-dev
- **Source type:** Official first-party engineering study; linked ICSE 2026 research
- **Availability status:** Code Reviewer GA since October 2025
- **Relevant CI/CD stages:** 1, 2, 3
- **Tool category:** Code review and developer productivity measurement
- **Geography:** Australia / United States; Atlassian internal global repositories
- **Key facts:** Atlassian reports a year-long online evaluation across more than 1,900 repositories, a 30.8% reduction in PR cycle time and 35.6% fewer human-written review comments. The agent checks quality, security, performance, engineering standards and Jira acceptance criteria, with suggestions remaining human-reviewed.
- **Why it matters:** One of the stronger first-party quantitative studies of an agent-mediated post-coding workflow at enterprise scale.
- **Confidence / limitations:** **Medium-high.** The study is first-party and the blog summarizes rather than reproduces the full statistical design; causality and transferability require the linked paper.

### CircleCI and Buildkite

#### S35. Introducing Chunk: autonomous validation

- **Organization / project:** CircleCI Chunk
- **Publication / update date:** 2025-09-23
- **URL:** https://circleci.com/blog/introducing-chunk/
- **Source type:** Official product launch blog
- **Availability status:** Available to all CircleCI users at publication
- **Relevant CI/CD stages:** 3, 4
- **Tool category:** Test reliability, build repair and pipeline optimization
- **Geography:** United States / global service
- **Key facts:** Chunk targets flaky tests, failed builds and configuration optimization, opening PRs for fixes. CircleCI reports that private-beta runs opened PRs for 90% of analyzed flaky tests and quotes one customer's approximate 60% fix success.
- **Why it matters:** Early available example of a specialized validation agent operating proactively rather than only answering CI questions.
- **Confidence / limitations:** **Medium.** Product availability is clear; effectiveness figures are vendor-reported and the sample size and measurement methodology are not disclosed.

#### S36. Agentic validation needs different infrastructure

- **Organization / project:** CircleCI Chunk sidecars and microbuilds
- **Publication / update date:** 2026-06-05
- **URL:** https://circleci.com/blog/agentic-validation-needs-different-infrastructure/
- **Source type:** Official engineering blog
- **Availability status:** Chunk sidecar tooling available through the Chunk CLI; maturity not labeled GA
- **Relevant CI/CD stages:** 3, 4
- **Tool category:** Agent-first test/build infrastructure
- **Geography:** United States / global service
- **Key facts:** CircleCI describes decoupling an agent from reproducible remote validation environments and using incremental microbuilds. It reports 3x more token-efficient failure output and 10-20x better core-cost efficiency than full CI in its internal measurements.
- **Why it matters:** Direct evidence that agent-generated change volume is driving a new low-latency validation tier between local tools and full CI.
- **Confidence / limitations:** **Medium-high.** Architecture and internal results are first-party; the benchmarking workload and external reproducibility are limited.

#### S37. What AI is teaching us about CI

- **Organization / project:** Buildkite agentic workflow components
- **Publication / update date:** 2025-12-01; updated 2025-12-03
- **URL:** https://buildkite.com/resources/blog/what-ai-is-teaching-us-about-ci/
- **Source type:** Official product/architecture blog
- **Availability status:** Announced available components; no unified GA label
- **Relevant CI/CD stages:** 1-4; cross-cutting governance
- **Tool category:** CI platform, MCP, dynamic pipelines and model plugins
- **Geography:** Australia / global service
- **Key facts:** Buildkite announces MCP access, model providers, universal triggers, a multi-language pipeline SDK and AI plugins for summaries and build-error diagnosis. It frames scalability, composability, adaptability, programmability and governability as agentic CI requirements.
- **Why it matters:** Shows a CI vendor offering composable primitives rather than a single closed agent, aimed at platform teams building their own workflows.
- **Confidence / limitations:** **High** for announced components; the five-requirement framework is vendor analysis, not empirical research.

#### S38. Agentic CI with Buildkite: three practical examples

- **Organization / project:** Buildkite agentic examples
- **Publication / update date:** 2025-12-01
- **URL:** https://buildkite.com/resources/blog/building-ai-powered-ci-workflows-three-practical-examples/
- **Source type:** Official technical blog with first-party open-source examples
- **Availability status:** Reference implementations available
- **Relevant CI/CD stages:** 1, 3, 4
- **Tool category:** CI pipeline, code review and build-failure remediation
- **Geography:** Australia / global open-source examples
- **Key facts:** Working templates demonstrate GitHub PR review, broken-PR-build repair and issue-to-draft-PR generation using pipeline triggers, SDKs, MCP and model plugins.
- **Why it matters:** Provides inspectable implementations that can be separated from product marketing claims.
- **Confidence / limitations:** **High** for what the examples implement; no evidence is provided on reliability at enterprise scale.

### Security and software supply chain

#### S39. Introducing Semgrep Custom Workflows

- **Organization / project:** Semgrep Custom Workflows
- **Publication / update date:** 2026-03-18
- **URL:** https://semgrep.dev/blog/2026/introducing-semgrep-custom-workflows/
- **Source type:** Official product/engineering blog
- **Availability status:** Launched product capability; exact entitlement not stated in source
- **Relevant CI/CD stages:** 1, 2, 3
- **Tool category:** Application security, programmable security CI and agent orchestration
- **Geography:** United States / global service
- **Key facts:** A Python SDK composes typed, testable workflow steps that combine deterministic Semgrep analysis with coding models. The design targets organization-specific triage, investigation and remediation rather than fixed vendor workflows.
- **Why it matters:** Shows security automation shifting from scanner output to programmable agent-plus-deterministic workflows.
- **Confidence / limitations:** **High** for architecture; status and commercial packaging need separate confirmation.

#### S40. Introducing Semgrep Guardian

- **Organization / project:** Semgrep Guardian
- **Publication / update date:** 2026-06-23
- **URL:** https://semgrep.dev/blog/2026/introducing-semgrep-guardian-real-time-security-for-ai-written-code/
- **Source type:** Official product launch blog
- **Availability status:** Available, including a free installation path
- **Relevant CI/CD stages:** 1, 2, 3
- **Tool category:** IDE/agent security, code, supply chain and secret scanning
- **Geography:** United States / global service
- **Key facts:** Hooks, MCP and skills scan every file an agent touches and can steer fixes before commit. Semgrep reports more than 3 million weekly scans, 95% under five seconds, and customer examples of prevented critical issues.
- **Why it matters:** Moves deterministic security controls inside the agent work loop instead of waiting for a downstream CI gate.
- **Confidence / limitations:** **Medium-high.** Product mechanism is clear; scale and customer-impact metrics are vendor-reported without full methodology.

#### S41. New agentic architecture for Snyk Agent Fix

- **Organization / project:** Snyk Agent Fix
- **Publication / update date:** 2026-04-27; rollout stated for 2026-05-26
- **URL:** https://snyk.io/blog/snyk-agent-fix-agentic-architecture/
- **Source type:** Official product/engineering blog
- **Availability status:** Announced production rollout
- **Relevant CI/CD stages:** 1, 2, 3
- **Tool category:** Security remediation and agent evaluation
- **Geography:** United States / Israel / global service
- **Key facts:** Snyk moved from static fine-tuning to dynamic few-shot prompting and agentic retries, expanding to all Snyk Code-supported languages. Evaluation covers security integrity, functional logic and fix applicability.
- **Why it matters:** Provides an explicit example of remediation quality being treated as a multi-axis evaluation problem rather than code generation alone.
- **Confidence / limitations:** **Medium-high.** Architecture and evaluation dimensions are first-party; comparative performance data are vendor-generated.

#### S42. Snyk Agent Security

- **Organization / project:** Snyk Agent Security / Evo
- **Publication / update date:** 2026-03-23
- **URL:** https://snyk.io/blog/introducing-agent-security/
- **Source type:** Official product announcement
- **Availability status:** Mixed: Evo AI-SPM GA; Agent Scan and Agent Red Teaming open preview; Agent Guard private preview; Snyk Studio deployed product
- **Relevant CI/CD stages:** 1-8; especially 2, 3, 5 and 7
- **Tool category:** Agent security posture, supply chain, policy and runtime guardrails
- **Geography:** United States / Israel / global service
- **Key facts:** The portfolio inventories models, agents, skills and MCP tools; scans agent dependencies; embeds security into coding-agent workflows; and previews real-time action controls and multi-step red teaming. Policies can be defined and enforced before production.
- **Why it matters:** Broadens software-supply-chain governance from source/artifacts to the tools and behaviors of the agents producing them.
- **Confidence / limitations:** **High** for mixed status labels. Vendor-reported scan and adoption figures are not independently audited.

#### S43. JFrog Platform Skills and MCP tools

- **Organization / project:** JFrog Platform
- **Publication / update date:** 2026-04-21
- **URL:** https://jfrog.com/blog/ai-agents-jfrog-skills-mcp-tools/
- **Source type:** Official product launch blog
- **Availability status:** Launched official skills and expanded MCP tools/plugins
- **Relevant CI/CD stages:** 2, 3, 4, 5, 7
- **Tool category:** Artifact repository, software supply chain, agent skills and MCP
- **Geography:** Israel / United States / global service
- **Key facts:** JFrog supplies agent skills and MCP tools that expose approved packages, vulnerabilities, governance policies and JFrog-managed supply-chain context to external agents.
- **Why it matters:** Indicates artifact repositories evolving from passive storage into context and policy providers for agent decisions.
- **Confidence / limitations:** **High** for launch. The source does not quantify whether agent-generated dependency choices become safer.

### Observability, release verification and incident response

#### S44. Datadog Bits AI SRE architecture

- **Organization / project:** Datadog Bits Investigation
- **Publication / update date:** 2026-01-12
- **URL:** https://www.datadoghq.com/blog/building-bits-ai-sre/
- **Source type:** Official engineering blog
- **Availability status:** Product capability in customer use; source does not use a GA label
- **Relevant CI/CD stages:** 7, 8
- **Tool category:** SRE investigation, evaluation and observability
- **Geography:** United States / global service
- **Key facts:** Bits autonomously reasons over telemetry and produces audit-ready RCA. Datadog describes an evaluation pipeline using human-labeled incidents, archived telemetry and multi-criterion judging; it reports customer time-to-resolution reductions of up to 95%.
- **Why it matters:** Supplies both a multi-agent operational pattern and a concrete evaluation loop for nondeterministic SRE behavior.
- **Confidence / limitations:** **Medium-high.** Architecture is detailed; outcome maximum and evaluation results remain first-party.

#### S45. Bits Agent Builder

- **Organization / project:** Datadog Bits Agent Builder
- **Publication / update date:** 2026-06-04
- **URL:** https://www.datadoghq.com/blog/bits-agent-builder/
- **Source type:** Official product launch blog
- **Availability status:** Product introduced; Case Management integration preview in companion docs
- **Relevant CI/CD stages:** 2, 3, 7, 8
- **Tool category:** Agent builder, workflow automation and remediation
- **Geography:** United States / global service
- **Key facts:** Teams define agents in natural language, choose accessible data/tools and use more than 2,000 actions across observability, CI/CD, security and cloud systems. Examples include evidence-linked incident analysis, GitHub remediation PRs and service-documentation PRs.
- **Why it matters:** Demonstrates observability platforms becoming action/orchestration planes, not just telemetry stores.
- **Confidence / limitations:** **High** for feature design; examples are illustrative and status varies by integration.

#### S46. Datadog DASH 2026 product roundup

- **Organization / project:** Datadog Bits Detection, Investigation, Remediation and Infrastructure Operations
- **Publication / update date:** 2026-06-09
- **URL:** https://www.datadoghq.com/blog/dash-2026-new-feature-roundup-keynote/
- **Source type:** Official release roundup
- **Availability status:** Mixed: Bits Detection preview; Bits Remediation preview; Bits Infrastructure Operations preview; other Bits capabilities vary
- **Relevant CI/CD stages:** 2, 3, 6, 7, 8
- **Tool category:** Release validation, testing, observability and autonomous remediation
- **Geography:** United States / global service
- **Key facts:** Bits Detection maintains monitoring coverage; Investigation links evidence; Remediation calls APIs/scripts or opens code-fix PRs; Infrastructure Operations can act inside defined guardrails or await approval. The release also flags risky IaC changes in PRs.
- **Why it matters:** Presents a continuous detection-to-remediation loop with configurable human approval based on resource and risk.
- **Confidence / limitations:** **High** for preview labels. The page is a broad roundup and must be decomposed before comparing maturity across capabilities.

#### S47. Dynatrace foundation for agentic AI at Perform 2026

- **Organization / project:** Dynatrace Intelligence and Smartscape
- **Publication / update date:** 2026-01-28; updated 2026-03-04
- **URL:** https://www.dynatrace.com/news/blog/dynatrace-introduces-a-new-foundation-for-agentic-ai-at-perform-2026/
- **Source type:** Official product announcement
- **Availability status:** Mixed; announcement spans shipped platform changes and developing agent ecosystem without per-item uniform status
- **Relevant CI/CD stages:** 6, 7, 8
- **Tool category:** Observability, topology, agent orchestration and MCP
- **Geography:** United States / Europe / global service
- **Key facts:** Dynatrace Intelligence combines deterministic analysis with agentic reasoning; Smartscape provides real-time dependency context; Intelligence Agents target auto-remediation, prevention and optimization; MCP connects external agents to platform evidence.
- **Why it matters:** Shows observability graphs and deterministic causal analysis being positioned as grounding/control infrastructure for operational agents.
- **Confidence / limitations:** **Medium-high.** Architecture is clear, but the article does not consistently label availability for every component.

#### S48. PagerDuty Spring 2026 release

- **Organization / project:** PagerDuty Operations Cloud / SRE Agent
- **Publication / update date:** 2026-03-12
- **URL:** https://www.pagerduty.com/newsroom/pagerduty-operations-cloud-spring-2026-release/
- **Source type:** Official release announcement
- **Availability status:** Primarily roadmap/upcoming release; individual features include GA, early access and planned states
- **Relevant CI/CD stages:** 7, 8
- **Tool category:** Incident management, ChatOps, SRE agent and A2A
- **Geography:** United States / global service
- **Key facts:** PagerDuty describes an SRE Agent that investigates/resolves incidents, Slack-native agentic workflows and A2A/MCP interoperability with hyperscaler agents. Companion release details label an incident-responder template GA and A2A early access with planned GA.
- **Why it matters:** Indicates incident platforms becoming coordinators among operational agents rather than only routing alerts to humans.
- **Confidence / limitations:** **High** for announced roadmap; low as proof of presently available autonomous operations because much of the announcement is forward-looking.

#### S49. New Relic Agentic Platform

- **Organization / project:** New Relic Agentic Platform
- **Publication / update date:** 2026-02-24
- **URL:** https://newrelic.com/blog/ai/advancing-observability-into-the-agentic-era
- **Source type:** Official product announcement
- **Availability status:** Preview
- **Relevant CI/CD stages:** 7, 8
- **Tool category:** Agentic observability and operational context
- **Geography:** United States / global service
- **Key facts:** New Relic positions the platform as an observability foundation for systems whose paths and dependencies change through agent action; sign-up is for preview.
- **Why it matters:** Confirms broad observability-market movement toward supervising and supporting autonomous systems.
- **Confidence / limitations:** **High** for preview status; the short announcement lacks detailed workflow, control and outcome evidence.

### China-based platform evidence

#### S50. Alibaba Cloud DevOps monthly updates: AI code review

- **Organization / project:** Alibaba Cloud Yunxiao Codeup
- **Publication / update date:** 2025-10 (feature entry; current update index includes 2026 revisions)
- **URL:** https://help.aliyun.com/zh/yunxiao/product-overview/monthly-product-update-overview
- **Source type:** Official product update index
- **Availability status:** Released to eligible Yunxiao editions
- **Relevant CI/CD stages:** 1, 2, 3
- **Tool category:** Code repository, AI review and merge workflow
- **Geography:** China
- **Key facts:** AI review analyzes merge-request changes, summarizes implementation, identifies issues, suggests fixes and provides an overall quality assessment. Companion documentation supports repository/global YAML rules and optional sandboxed cross-file analysis.
- **Why it matters:** Primary evidence that a major Chinese DevOps platform integrated AI review into the merge flow during the evidence window.
- **Confidence / limitations:** **High** for feature release. The source does not describe multi-step execution beyond review/suggestion or provide outcome metrics.

#### S51. Huawei Cloud CodeArts Agent CLI

- **Organization / project:** Huawei Cloud CodeArts Agent (码道) CLI
- **Publication / update date:** 2026-07-02
- **URL:** https://support.huaweicloud.com/usermanual-cli/codeartsagent_cli_0001.html
- **Source type:** Official product documentation
- **Availability status:** Available documented CLI; packaging varies by edition
- **Relevant CI/CD stages:** 1, 3, 4; potential scripted integration across later stages
- **Tool category:** Coding/analysis agent, CLI and workflow integration
- **Geography:** China
- **Key facts:** The agent supports code analysis, optimization and generation in TUI/CLI forms. Noninteractive command parameters and script integration permit batch and automated execution; product materials also describe unit-test generation and automatic fixes.
- **Why it matters:** Provides a scriptable Chinese enterprise-agent interface that can be inserted into delivery automation rather than remaining IDE-only.
- **Confidence / limitations:** **High** for CLI functions. The source does not document a native Agent step inside CodeArts Pipeline or production deployment autonomy.

#### S52. Tencent Cloud CloudQ DevOps Agent

- **Organization / project:** Tencent Cloud CloudQ
- **Publication / update date:** 2026-06-03
- **URL:** https://cloud.tencent.com/document/product/1264/132437
- **Source type:** Official product documentation
- **Availability status:** Documented service; some multicloud capabilities explicitly marked as opening progressively
- **Relevant CI/CD stages:** 4, 6, 7, 8
- **Tool category:** ChatOps, AIOps, CloudOps and DevOps agent
- **Geography:** China
- **Key facts:** CloudQ covers build, inspection, monitoring, logs, diagnosis and reporting through natural-language tasks; it supports scheduled/combined automation and multiple collaboration channels. Multicloud management is described as progressively available.
- **Why it matters:** First-party China evidence of a cloud-operations agent spanning collaboration, proactive diagnosis and resource operations.
- **Confidence / limitations:** **High** for documented scope; status granularity and production outcome metrics are limited, and some “first” claims are marketing assertions.

### Open-source projects and primary research

#### S53. `github/gh-aw` — GitHub Agentic Workflows

- **Organization / project:** GitHub / `gh-aw`
- **Publication / update date:** Public-preview release line as of 2026-06-11; repository snapshot checked 2026-07-14
- **URL:** https://github.com/github/gh-aw
- **Source type:** First-party open-source repository (MIT)
- **Availability status:** Open source; product capability public preview
- **Relevant CI/CD stages:** 1-8, depending on workflow definition
- **Tool category:** Agentic workflow compiler, GitHub Actions and security guardrails
- **Geography:** United States / global open source
- **Key facts:** The repository compiles natural-language workflows for Copilot, Claude, Codex or Gemini into Actions. It documents read-only defaults, safe outputs, sandboxing, input sanitization, network isolation, SHA-pinned dependencies, allowlists and compile-time validation. GitHub showed approximately 4.8k stars at the research snapshot.
- **Why it matters:** High-interest, inspectable implementation of a repository-native agent workflow format and its security architecture.
- **Confidence / limitations:** **High** for code and repository metadata. Star count is a volatile popularity signal, not production-maturity evidence; public-preview risk warnings apply.

#### S54. Nx Self-Healing CI

- **Organization / project:** Nx / `nrwl/nx` ecosystem
- **Publication / update date:** 2025-10-14
- **URL:** https://nx.dev/blog/whats-new-in-nx-self-healing-ci
- **Source type:** Official engineering/product blog tied to a first-party open-source project
- **Availability status:** Available on Nx Cloud Hobby, Team and Enterprise plans
- **Relevant CI/CD stages:** 3, 4
- **Tool category:** Build graph, CI failure diagnosis, verification and auto-fix
- **Geography:** United States / global service and open-source community
- **Key facts:** The agent proposes fixes for failed tasks, supports accept/reject/local refinement, verifies changes by rerunning CI and can auto-apply selected low-risk fixes. Task patterns let teams exclude deployment or high-risk test classes. The `nrwl/nx` repository showed approximately 29.2k stars at the 2026-07-14 snapshot.
- **Why it matters:** High-star project ecosystem with a concrete bounded-autonomy design: graph context, deterministic verification, task-level authority and reversible commits.
- **Confidence / limitations:** **High** for workflow and availability; Nx Cloud's self-healing service is commercial even though the core Nx project is open source. Vendor acceptance figures are not included here.

#### S55. HolmesGPT — CNCF SRE Agent

- **Organization / project:** HolmesGPT / CNCF Sandbox
- **Publication / update date:** Release 0.31.1 on 2026-05-28; repository snapshot checked 2026-07-14
- **URL:** https://github.com/HolmesGPT/holmesgpt
- **Source type:** First-party open-source repository and release history (Apache-2.0)
- **Availability status:** Active open-source project; CNCF Sandbox maturity
- **Relevant CI/CD stages:** 7, 8
- **Tool category:** SRE investigation, Kubernetes/cloud observability and remediation PRs
- **Geography:** Global open source; originally Robusta, with Microsoft contributions
- **Key facts:** HolmesGPT investigates production incidents across Kubernetes, VMs, cloud, databases and SaaS. Operator mode runs in the background and can notify Slack or open GitHub PRs for fixes. The repository showed approximately 2.8k stars at the snapshot.
- **Why it matters:** High-interest open-source alternative for post-release investigation, useful for inspecting tool integrations and control boundaries outside closed platforms.
- **Confidence / limitations:** **High** for repository features and community status. CNCF Sandbox denotes early project maturity; star count and release activity do not prove enterprise reliability.

#### S56. AIOpsLab

- **Organization / project:** Microsoft Research / AIOpsLab
- **Publication / update date:** Active repository snapshot checked 2026-07-14; associated research revision available in 2026
- **URL:** https://github.com/microsoft/AIOpsLab
- **Source type:** First-party open-source research framework
- **Availability status:** Research / experimental open source
- **Relevant CI/CD stages:** 8; evaluation applicable to 6-8
- **Tool category:** Autonomous AIOps-agent design and evaluation
- **Geography:** United States / global research
- **Key facts:** The framework provisions reproducible cloud-native incident environments, injects faults, exposes operations through an agent interface and evaluates detection, localization, diagnosis and mitigation. The repository showed approximately 917 stars at the snapshot.
- **Why it matters:** One of the few open frameworks aimed at repeatable operational-agent evaluation rather than demo-only incident narratives.
- **Confidence / limitations:** **High** for framework scope. It is a research testbed, not a production incident-management platform; its star count is below the highest-popularity projects in this register.

#### S57. From Assistance to Agency: Rethinking Autonomy and Control in CI/CD Pipelines

- **Organization / project:** Academic authors Marcus Emmanuel Barnes, Taher A. Ghaleb and Safwat Hassan
- **Publication / update date:** 2026-05-08
- **URL:** https://arxiv.org/abs/2605.07062
- **Source type:** Primary academic preprint / vision paper
- **Availability status:** Preprint; not peer-reviewed in the source
- **Relevant CI/CD stages:** 1-8; especially governance and release authority
- **Tool category:** Agentic CI/CD theory, autonomy and governance
- **Geography:** International academic research
- **Key facts:** The paper defines authority transfer and distinguishes data-plane authority (patches, retries) from control-plane authority (pipeline, policy and approval changes). It finds current systems concentrated in bounded data-plane autonomy with safety supplied by external governance.
- **Why it matters:** Provides a precise vocabulary for comparing products that all call themselves agentic but delegate very different decision rights.
- **Confidence / limitations:** **Medium-high** for conceptual analysis. It is a non-systematic synthesis and preprint, not an empirical benchmark.

#### S58. LLM-Augmented Release Intelligence

- **Organization / project:** Happy Bhati; production Kubernetes-native release platform case
- **Publication / update date:** 2026-03-15
- **URL:** https://arxiv.org/abs/2603.14619
- **Source type:** Primary academic/engineering preprint and production case study
- **Availability status:** Implemented case study; preprint
- **Relevant CI/CD stages:** 5, 7
- **Tool category:** Release intelligence, impact analysis and promotion communication
- **Geography:** Not specified in the source
- **Key facts:** A post-promotion GitHub Actions step combines deterministic commit filtering, LLM summaries and static task-to-pipeline dependency analysis. The reported platform contains more than 60 Tekton tasks across more than 20 release pipelines and retains provenance links.
- **Why it matters:** Concrete example of combining deterministic blast-radius analysis with LLM communication instead of asking a model to infer everything.
- **Confidence / limitations:** **Medium-high.** Architecture is described in detail, but evaluation is qualitative and single-environment.

#### S59. AutoPipelineAI

- **Organization / project:** Academic authors led by Youssef Mohamed Aboelfotoh
- **Publication / update date:** 2026-06-04
- **URL:** https://arxiv.org/abs/2606.06662
- **Source type:** Primary academic preprint and prototype evaluation
- **Availability status:** Research prototype
- **Relevant CI/CD stages:** 3, 4, 6
- **Tool category:** Natural-language pipeline generation and validation
- **Geography:** International academic research
- **Key facts:** The prototype analyzes repository structure, generates GitHub Actions/GitLab CI configurations and validates syntax and functional requirements. Tests on two repositories show high aggregate scoring can coexist with low raw functional pass rates and multiple omissions/configuration errors.
- **Why it matters:** Supplies direct evidence that generated YAML validity or LLM-judge scores are insufficient proxies for executable pipeline correctness.
- **Confidence / limitations:** **Medium.** The evaluation covers only two projects and is a preprint; it is valuable mainly for its failure taxonomy and cautionary results.

#### S60. Agentic Coding Needs Proactivity, Not Just Autonomy

- **Organization / project:** Google Research
- **Publication / update date:** 2026 (to appear; available during evidence window)
- **URL:** https://research.google/pubs/agentic-coding-needs-proactivity-not-just-autonomy/
- **Source type:** Primary research position paper
- **Availability status:** Research publication, to appear
- **Relevant CI/CD stages:** 1-8 where agents run on schedules/events
- **Tool category:** Human-agent interaction and evaluation theory
- **Geography:** United States / global research
- **Key facts:** The paper separates reactive, scheduled and situation-aware agents and proposes evaluating insight decision quality, context grounding and learning lift for proactive behavior.
- **Why it matters:** Provides evaluation criteria for agents that initiate repository, CI or operational work without a direct prompt.
- **Confidence / limitations:** **Medium-high** for the conceptual framework. Publication venue/date and empirical validation remain incomplete in the source.

#### S61. Claude Code GitHub Action v1.0

- **Organization / project:** Anthropic / `claude-code-action`
- **Publication / update date:** GA release 2025-08-26; release history current through 2026-07-11
- **URL:** https://github.com/anthropics/claude-code-action/releases
- **Source type:** First-party open-source release history and documentation
- **Availability status:** GA; active v1 release line
- **Relevant CI/CD stages:** 1, 2, 3, 4
- **Tool category:** Repository agent, pull-request review, security scanning and CI repair
- **Geography:** United States / global open source and service integrations
- **Key facts:** The action supports interactive and automated workflows, configurable models/tools/system prompts, automated PR review, CI-failure fixing, issue triage and security scanning. It can run with Anthropic's API or through Amazon Bedrock and Google Vertex AI. The repository showed approximately 8.3k stars at the 2026-07-14 research snapshot.
- **Why it matters:** Shows a general-purpose engineering agent becoming a standard, reusable pipeline step rather than remaining confined to an IDE or chat surface.
- **Confidence / limitations:** **High** for availability and documented functions; low for comparative outcome claims. Teams remain responsible for runner isolation, credentials, untrusted pull-request input and action permissions. Star count is volatile and is not maturity evidence.

#### S62. Remote GitHub MCP Server is generally available

- **Organization / project:** GitHub / GitHub MCP Server
- **Publication / update date:** 2025-09-04
- **URL:** https://github.blog/changelog/2025-09-04-remote-github-mcp-server-is-now-generally-available/
- **Source type:** Official GA announcement with linked first-party open-source implementation
- **Availability status:** Remote server GA; local server open source
- **Relevant CI/CD stages:** 1, 2, 3, 4, 5, 7
- **Tool category:** Repository, Actions, code-security and release Agent tool layer
- **Geography:** United States / global service
- **Key facts:** The remote server uses OAuth 2.1 with PKCE and short-lived credentials, while enterprise policy can centrally govern access. Agents can inspect Actions logs, analyze build failures, work with releases and access code/secret-scanning functions. Toolsets, individual tools and read-only mode let administrators reduce exposed authority.
- **Why it matters:** Evidence that code-hosting platforms are standardizing repository, CI, security and release capabilities as governed Agent-callable tools.
- **Confidence / limitations:** **High** for GA and security controls. MCP availability is an integration primitive, not proof that an attached Agent can execute those workflows reliably; some capabilities require paid GitHub licenses.

#### S63. SWE-CI: Evaluating Agent Capabilities in Maintaining Codebases via Continuous Integration

- **Organization / project:** Alibaba Group and Sun Yat-sen University / SWE-CI
- **Publication / update date:** 2026-03-04; revised 2026-04-01
- **URL:** https://arxiv.org/abs/2603.03823
- **Source type:** Primary academic preprint with public benchmark code and data
- **Availability status:** Preprint under review; open evaluation artifacts
- **Relevant CI/CD stages:** 2, 3, 4
- **Tool category:** Continuous-integration Agent benchmark and regression evaluation
- **Geography:** China
- **Key facts:** The benchmark contains 100 tasks from 68 Python repositories, each spanning an average 233 days and 71 successive commits. Agents repeatedly analyze failed tests, modify code and verify again for up to 20 rounds. Reported zero-regression and maintainability results remain materially below human oracle implementations for most tested models.
- **Why it matters:** Directly tests sustained CI maintenance rather than one-shot issue resolution, supporting a distinction between impressive patch generation and dependable long-horizon delivery ownership.
- **Confidence / limitations:** **Medium-high.** The artifacts and task design are inspectable, but the paper is not yet peer reviewed, covers only Python and uses future-test performance as a maintainability proxy. It does not evaluate packaging or deployment.

#### S64. WhatsCode: Large-Scale GenAI Deployment for Developer Efficiency at WhatsApp

- **Organization / project:** Meta / WhatsApp engineering
- **Publication / update date:** 2025-12-04; accepted for ICSE-SEIP 2026
- **URL:** https://arxiv.org/abs/2512.05314
- **Source type:** First-party longitudinal enterprise practice study
- **Availability status:** Deployed internal systems; research publication
- **Relevant CI/CD stages:** 1, 2, 3, 4, 8
- **Tool category:** Enterprise engineering agents, privacy/static checking, test automation and incident investigation
- **Geography:** United States / global engineering organization
- **Key facts:** The study covers 25 months and more than 3,000 accepted changes. It reports privacy-verification coverage increasing from 15% to 53%, two common collaboration patterns—approximately 60% one-click rollout and 40% commandeer-and-revise—and an investigation system identifying root causes for 21% of production incidents.
- **Why it matters:** Rare longitudinal evidence connecting platform architecture, human correction patterns, process design and measured enterprise outcomes; full autonomy was not necessary for material value.
- **Confidence / limitations:** **High-value first-party evidence**, but it comes from one company and the authors acknowledge selection and generalizability limits. Reported results do not establish how an external product would perform in another organization.

#### S65. Accelerating the Adoption of Software and AI Agent Identity and Authorization

- **Organization / project:** United States NIST National Cybersecurity Center of Excellence
- **Publication / update date:** 2026-02-05
- **URL:** https://csrc.nist.gov/pubs/other/2026/02/05/accelerating-the-adoption-of-software-and-ai-agent/ipd
- **Source type:** Government initial public draft / concept paper
- **Availability status:** Initial public draft; not a finalized standard or mandate
- **Relevant CI/CD stages:** 1-8
- **Tool category:** Agent identity, delegated authorization, audit and non-repudiation
- **Geography:** United States; globally relevant governance reference
- **Key facts:** The paper frames Agent identity, authorization, auditability, non-repudiation and prompt-injection controls as adoption problems. It explores fixed versus task-level ephemeral identity, identity metadata, OAuth/OIDC, delegated principals and the authority an Agent exercises on another actor's behalf.
- **Why it matters:** Supports concrete enterprise controls for delivery Agents: unique task identity, least privilege, short-lived credentials, explicit delegation and auditable actions across repositories, pipelines, artifact stores and deployment targets.
- **Confidence / limitations:** **High** as an authoritative problem statement, but it is a concept draft and question set, not a validated reference architecture or binding NIST standard.

#### S66. Kubernetes MCP Server v0.0.62

- **Organization / project:** Containers community / `kubernetes-mcp-server`
- **Publication / update date:** 2026-05-05
- **URL:** https://github.com/containers/kubernetes-mcp-server/releases/tag/v0.0.62
- **Source type:** First-party open-source release
- **Availability status:** Active open-source release
- **Relevant CI/CD stages:** 6, 7, 8
- **Tool category:** Kubernetes/OpenShift/Helm Agent tool server
- **Geography:** Global open-source community
- **Key facts:** The server exposes Kubernetes resources, Helm and related cluster operations to MCP clients. Its 2026 release line adds Entra ID on-behalf-of flows, session rate limiting, pre-execution validation, transactional hot reload and continuous MCP evaluation. The repository showed approximately 1.6k stars at the research snapshot.
- **Why it matters:** Illustrates deployment platforms being refactored into identity-aware, rate-limited, validated Agent tool APIs before autonomous planners themselves become mature.
- **Confidence / limitations:** **High** for implementation and release facts. It is a tool server, not a planning Agent, so it should not be counted as evidence of autonomous deployment judgment. Popularity is not production proof.

#### S67. SWE-EVO: Benchmarking Coding Agents in Long-Horizon Software Evolution Scenarios

- **Organization / project:** International academic authors / SWE-EVO
- **Publication / update date:** 2025-12-20; revised 2026-05-22
- **URL:** https://arxiv.org/abs/2512.18470
- **Source type:** Primary academic preprint with MIT-licensed benchmark repository
- **Availability status:** Preprint; open benchmark and evaluation adapters
- **Relevant CI/CD stages:** 2, 3, 4, 5
- **Tool category:** Long-horizon software evolution and regression benchmark
- **Geography:** International research
- **Key facts:** The benchmark reconstructs 48 release-evolution tasks from seven mature Python projects. Each task changes an average 21 files and is evaluated by roughly 874 tests. The 2026 revision reports GPT-5.4 with OpenHands completing 25%, highlighting a gap between single-issue benchmark success and release-scale evolution.
- **Why it matters:** Provides a caution against treating one-patch coding scores as sufficient evidence for version evolution, accumulated regression control or sustained CI responsibility.
- **Confidence / limitations:** **Medium.** It covers only 48 Python tasks, is an offline reconstruction and will drift as models and scaffolds improve; it does not reproduce live enterprise deployment constraints.

### Incremental evidence added on 2026-07-14

#### S68. Manage your supply chain using natural language with MCP

- **Organization / project:** Cloudsmith CLI / MCP Server
- **Publication / update date:** 2026-03-02
- **URL:** https://cloudsmith.com/changelog/manage-your-supply-chain-using-natural-language-with-mcp
- **Source type:** Official product changelog
- **Availability status:** Available initial release; local MCP process
- **Relevant CI/CD stages:** 2, 5
- **Tool category:** Artifact repository, package management, supply-chain security and MCP
- **Geography:** United Kingdom / global service
- **Key facts:** Cloudsmith exposes repository and package-management actions through its CLI-based MCP server. Agents can query vulnerabilities, list package versions and manage artifacts. API keys, SSO login and multiple profiles support separate production and sandbox contexts.
- **Why it matters:** Adds rare direct evidence that an artifact repository is becoming an Agent action surface rather than only a read-only context source.
- **Confidence / limitations:** **High** for documented functionality. The server is local, advanced workspace policies and usage metrics are not yet exposed, and the source does not demonstrate autonomous promotion or policy approval.

#### S69. Sonatype Guide for secure agentic development

- **Organization / project:** Sonatype Guide
- **Publication / update date:** 2025-12-09; product guidance updated through 2026-06-18
- **URL:** https://www.sonatype.com/press-releases/introducing-guide-for-secure-agentic-development
- **Source type:** Official product announcement and documentation
- **Availability status:** Available; official source does not use GA terminology
- **Relevant CI/CD stages:** 2, 5
- **Tool category:** Dependency intelligence, component-version guidance, compliance and MCP
- **Geography:** United States / global service
- **Key facts:** Guide gives coding agents real-time component, vulnerability, malicious-package, license, maintainer-health and recommended-version information through MCP. It is positioned to influence dependency selection before code enters downstream scans.
- **Why it matters:** Shows supply-chain governance moving upstream into the Agent decision loop, complementing post-build scanning and artifact controls.
- **Confidence / limitations:** **High** for availability and intended function; low for independent outcome evidence. It is not evidence of autonomous artifact promotion or release-version authority.

#### S70. Dependabot alerts assignable to coding agents

- **Organization / project:** GitHub Dependabot and coding agents
- **Publication / update date:** 2026-04-07
- **URL:** https://github.blog/changelog/2026-04-07-dependabot-alerts-are-now-assignable-to-ai-agents-for-remediation/
- **Source type:** Official product changelog
- **Availability status:** Available on GitHub.com for eligible GitHub Code Security and Copilot plans
- **Relevant CI/CD stages:** 2, 3, 5
- **Tool category:** Repository, dependency security, version remediation and coding agents
- **Geography:** United States / global service
- **Key facts:** A Dependabot alert can be assigned to Copilot, Claude or Codex. The selected agent analyzes advisory and repository usage, opens a draft pull request and attempts to repair test failures caused by the update; supported patterns include breaking upgrades and reverting to a known-safe version.
- **Why it matters:** Provides a concrete Analyzer-to-Agent pattern: deterministic vulnerability evidence initiates a multi-file repair and test loop, while the PR remains the human review boundary.
- **Confidence / limitations:** **High** for available workflow. GitHub explicitly warns that fixes may be incomplete or wrong and requires review and test verification; no auto-merge or release authority is shown.

#### S71. Octopus Intelligence Agent App integration with GitHub

- **Organization / project:** Octopus Deploy / GitHub Agent HQ
- **Publication / update date:** 2026-06-04
- **URL:** https://octopus.com/blog/octopus-intelligence-integration-with-github-agent-hq
- **Source type:** Official integration announcement and demonstration
- **Availability status:** Available; agentic features included in eligible Octopus use
- **Relevant CI/CD stages:** 6, 7, 8
- **Tool category:** Deployment orchestration, release context, runbooks and recovery
- **Geography:** Australia / United States / global service
- **Key facts:** The app combines Octopus MCP with specialized skills. From GitHub, it can show where a PR was deployed, diagnose a failed tenant deployment, invoke the appropriate runbook and redeploy the affected tenant.
- **Why it matters:** Connects repository context to deployment status and bounded recovery actions, reducing the traditional handoff between development and release operations.
- **Confidence / limitations:** **High** for product availability and demonstrated workflow. The source is a vendor demo, not a controlled reliability study; permissions, approval policies and runbook quality still determine the real autonomy boundary.

#### S72. Octopus Claude Agent Step

- **Organization / project:** Octopus Deploy / Claude Agent Step
- **Publication / update date:** 2026-07-07; getting-started material updated 2026-07-09
- **URL:** https://octopus.com/docs/octopus-ai/claude-agent-step
- **Source type:** Official product documentation
- **Availability status:** Alpha
- **Relevant CI/CD stages:** 6, 7, 8
- **Tool category:** Deployment pipeline Agent step, runbooks, validation and governance
- **Geography:** Australia / global product
- **Key facts:** Claude Code can run as a native deployment-process or runbook step with release, environment, variable and preceding-log context. Controls include tool allowlists, sandboxing, prompt-injection checks, token/turn budgets and session audit. The Agent can signal a failed deployment for downstream deterministic handling.
- **Why it matters:** One of the clearest examples of an Agent becoming a first-class deployment pipeline step rather than an external chat assistant.
- **Confidence / limitations:** **High** for documented Alpha behavior. Octopus advises against critical unattended automation; normal Agent exit is not proof of task success, and mid-run human approval is not yet available.

#### S73. Terraform MCP Server v1.0 infrastructure patterns and reference

- **Organization / project:** HashiCorp / IBM Terraform MCP Server
- **Publication / update date:** 2026-06-26
- **URL:** https://www.hashicorp.com/en/blog/terraform-mcp-server-four-real-world-ai-infrastructure-patterns
- **Source type:** Official engineering guidance with linked product reference
- **Availability status:** Available v1.0.x; official material does not use a separate GA label
- **Relevant CI/CD stages:** 3, 5, 6, 7
- **Tool category:** IaC, private module registry, Policy-as-Code and MCP
- **Geography:** United States / global open source and enterprise products
- **Key facts:** Agents can access public/private registries, modules, Sentinel policies, workspaces and runs. Default run types include plan-only and plan-and-apply-if-approved; auto-approve, destroy and other destructive operations require explicitly setting `ENABLE_TF_OPERATIONS=true`.
- **Why it matters:** Makes autonomy layering explicit: trusted context and Plan are broadly callable, Apply retains approval, and high-risk actions require deliberate permission expansion.
- **Confidence / limitations:** **High** for tool behavior and controls. The MCP server supplies tools, not a complete decision Agent; the four patterns are reference scenarios rather than measured customer outcomes.

#### S74. uReview: Scaling GenAI-based code review at Uber

- **Organization / project:** Uber Engineering / uReview
- **Publication / update date:** 2025-08-12
- **URL:** https://www.uber.com/tr/en/blog/ureview/
- **Source type:** Official first-party engineering case study
- **Availability status:** Deployed internal production system
- **Relevant CI/CD stages:** 1, 2, 3
- **Tool category:** Enterprise code review, validation, deduplication and evaluation
- **Geography:** United States / multinational engineering organization
- **Key facts:** Uber describes a multi-stage review system covering generation, filtering, validation and deduplication. It reports analysis of more than 90% of roughly 65,000 weekly diffs, 75% comment usefulness and more than 65% of comments addressed, across six monorepositories with a median four-minute latency.
- **Why it matters:** Adds large-enterprise operating evidence and shows that model selection, golden benchmarks and signal filtering matter as much as comment generation.
- **Confidence / limitations:** **Medium-high.** Scale and outcome figures are first-party and not independently audited; usefulness definitions and counterfactual quality effects may not transfer to other organizations.

#### S75. Solving the identity crisis for AI Agents at Uber

- **Organization / project:** Uber Engineering / Agent Identity architecture
- **Publication / update date:** 2026-05-21
- **URL:** https://www.uber.com/au/en/blog/solving-the-agent-identity-crisis/
- **Source type:** Official first-party production architecture case study
- **Availability status:** Internal production foundation; higher dynamic-access layers partly roadmap
- **Relevant CI/CD stages:** 1-8
- **Tool category:** Agent registry, SPIFFE/SPIRE identity, token exchange, MCP gateway, audit and authorization
- **Geography:** United States / multinational engineering organization
- **Key facts:** Uber binds an Agent registry to SPIFFE workload identity, uses an STS to mint short-lived single-hop JWTs and carries the full user-to-Agent-to-Agent-to-tool actor chain. Its MCP Gateway enforces tool policy and audit. Uber reports adoption by thousands of internal Agents and STS token-exchange P99 consistently below 40 ms.
- **Why it matters:** Rare production evidence for identity and delegated authorization at enterprise Agent scale, directly applicable to repository, CI and operations tool calls.
- **Confidence / limitations:** **High-value first-party evidence.** Metrics are vendor/internal claims. The identity foundation is current; the dynamic access-control and unified policy layers are presented as longer-term direction and must not be described as fully deployed.

#### S76. Pomona: AI Agents for software maintenance at Bloomberg

- **Organization / project:** Bloomberg Engineering / Pomona
- **Publication / update date:** 2026-06-04
- **URL:** https://arxiv.org/abs/2606.06752
- **Source type:** Original enterprise research preprint
- **Availability status:** Under review; one-team internal deployment study
- **Relevant CI/CD stages:** 1, 2, 3, 4
- **Tool category:** Technical-debt scanning, prioritized backlog and small repair pull requests
- **Geography:** United States / multinational financial technology organization
- **Key facts:** Pomona separates a scanning skill that creates a prioritized maintenance backlog from a repair skill tuned for small pull requests. In a one-month Bloomberg team study, 15 of 17 PRs were merged, median close time was below two hours, and 8 of 10 surveyed senior engineers wanted continued adoption.
- **Why it matters:** Shows a practical pattern for continuous maintenance: discover small repairable debt, create reviewable patches and learn from human acceptance.
- **Confidence / limitations:** **Medium.** The sample is one team, 17 PRs and 10 survey respondents; the paper is under review and cannot support broad productivity claims.

#### S77. Managing AI Coding with Agent evaluation in a 310k-line Meituan refactor

- **Organization / project:** Meituan engineering practice
- **Publication / update date:** 2026-05-07
- **URL:** https://tech.meituan.com/2026/05/07/Agent-AI-Coding.html
- **Source type:** Official first-party engineering case study
- **Availability status:** Current internal practice
- **Relevant CI/CD stages:** 1, 2, 3; cross-cutting workflow and people change
- **Tool category:** AI code review, Pre-PR, rules, evaluation and technical-debt management
- **Geography:** China
- **Key facts:** The team reports a codebase that grew from below 50k to above 310k lines, with more than 90% of code AI-assisted. It formalized team consensus as AI-executable rules, requires iterative AI self-review before PR, and shifts human review toward architecture, business intent and alignment with the technical design.
- **Why it matters:** Direct Chinese large-company evidence that faster AI coding moves the bottleneck to review and forces teams to redesign both Pre-PR workflow and human reviewer responsibilities.
- **Confidence / limitations:** **Medium-high** as a first-party process account. It is not a controlled Agent product study, and it supplies limited defect-rate or delivery-stability measurements.

#### S78. Dual-RAG intelligent code review with JoyAgent at JD

- **Organization / project:** JD Supply Chain Technology / JoyAgent
- **Publication / update date:** 2026-01-13
- **URL:** https://developer.jdcloud.com/article/4424
- **Source type:** Official first-party engineering practice article
- **Availability status:** Internal exploratory practice
- **Relevant CI/CD stages:** 1, 2
- **Tool category:** Merge-request review, code knowledge engineering, RAG and organizational rules
- **Geography:** China
- **Key facts:** A webhook parses merge requests and diffs, then combines code-knowledge retrieval with a JoyAgent knowledge base. The design recognizes project type, chunks code at semantic boundaries and reranks business and review-rule context before publishing line-level comments through the coding-platform API.
- **Why it matters:** Demonstrates that enterprise review quality depends on business context, project identity and retrieval quality, not only larger prompts or base models.
- **Confidence / limitations:** **Medium.** The article offers concrete architecture and examples but no controlled precision, recall, defect-escape or productivity dataset; it should be treated as exploratory practice rather than proven autonomous review.

#### S79. AuthZEN Agent authorization drafts: AARP and COAZ

- **Organization / project:** OpenID Foundation AuthZEN Working Group
- **Publication / update date:** 2026-06-15
- **URL:** https://openid.net/openid-foundation-advances-authorization-for-the-agent-era-with-new-authzen-working-group-drafts/
- **Source type:** Official standards-body announcement and Working Group Drafts
- **Availability status:** Official Working Group Drafts; not final standards
- **Relevant CI/CD stages:** 1-8
- **Tool category:** Authorization, approval prerequisites, MCP tool policy and gateways
- **Geography:** International standards community
- **Key facts:** AARP defines patterns for requesting, satisfying and re-evaluating approval, consent, delegated authority, attestation, risk assessment and justification before policy decides. COAZ maps source information into AuthZEN's Subject-Action-Resource-Context structure for authorization at gateways, service meshes and downstream systems, initially targeting MCP tools.
- **Why it matters:** Provides a standards direction for replacing ad-hoc approval prompts with external policy re-evaluation at the moment an Agent action is enforced.
- **Confidence / limitations:** **High** for draft content and status. These are Working Group Drafts, not certified interoperability, final specifications or proof of enterprise deployment.

#### S80. Google Cloud Agent Identity overview

- **Organization / project:** Google Cloud IAM / Agent Identity
- **Publication / update date:** Live documentation verified 2026-07-14
- **URL:** https://docs.cloud.google.com/iam/docs/agent-identity-overview
- **Source type:** Official product documentation
- **Availability status:** Mixed; core identity documented for supported runtimes, Auth Manager and several external-auth models are Preview
- **Relevant CI/CD stages:** 1-8
- **Tool category:** SPIFFE identity, IAM, delegated credentials, policy and audit
- **Geography:** United States / global cloud service
- **Key facts:** Each Agent receives a strongly attested SPIFFE identity tied to its lifecycle. Agents can act on their own authority or on behalf of an end user; in the Gateway path, raw end-user credentials are encrypted and decrypted outside the Agent. IAM, deny policies, Principal Access Boundary, VPC Service Controls and audit logs provide external controls.
- **Why it matters:** Confirms major-cloud convergence on per-Agent cryptographic identity, delegated authority and gateway-mediated credentials instead of shared service accounts.
- **Confidence / limitations:** **High** for documentation. Preview applies at feature granularity, service support is currently limited, and the live page can change; it is not evidence of cross-cloud interoperability.

#### S81. Malicious Agent Skills in the Wild

- **Organization / project:** International academic researchers / SkillScan dataset
- **Publication / update date:** 2026-02-06; v4 2026-06-10
- **URL:** https://arxiv.org/abs/2602.06547
- **Source type:** Original security research; accepted to USENIX Security 2026
- **Availability status:** Peer-reviewed conference acceptance with public dataset/detection pipeline
- **Relevant CI/CD stages:** 1-8
- **Tool category:** Agent Skill supply-chain security, static/dynamic analysis and threat taxonomy
- **Geography:** International research
- **Key facts:** The study analyzes 98,380 Skills and confirms 157 malicious Skills containing 632 vulnerabilities across 13 attack techniques. Dominant strategies include credential theft through code execution and Agent manipulation through adversarial documentation; registries removed all disclosed malicious Skills.
- **Why it matters:** Converts the Skill/MCP supply-chain risk from a hypothetical concern into measured ecosystem evidence, relevant wherever CI/CD Agents install executable Skills.
- **Confidence / limitations:** **High** for the studied snapshot and accepted paper. Registry populations and attacker behavior change quickly, and prevalence in an enterprise-curated catalog may differ materially.

#### S82. CodeThread: Is Agent code less maintainable than human code?

- **Organization / project:** Academic authors / CodeThread
- **Publication / update date:** 2026-06-19
- **URL:** https://arxiv.org/abs/2606.21804
- **Source type:** Original research preprint
- **Availability status:** Preprint with experimental framework
- **Relevant CI/CD stages:** 1, 3, 4, 5
- **Tool category:** Agent-generated-code maintainability and longitudinal regression evaluation
- **Geography:** International research
- **Key facts:** Across four frontier coding Agents and four benchmarks, Agents resolving later tasks on top of Agent-authored code show success-rate drops of up to 13.1% compared with building on human-authored code. Traditional maintainability metrics do not fully explain the difference.
- **Why it matters:** Supports evaluating the accumulated effects of Agent changes across releases, not only whether each isolated PR passes current CI.
- **Confidence / limitations:** **Medium.** Results come from controlled benchmarks and a fast-moving model snapshot; they do not yet establish production defect rates or causality for every codebase.

#### S83. Agentgateway v1.3

- **Organization / project:** Linux Foundation / `agentgateway`
- **Publication / update date:** 2026-06-17
- **URL:** https://agentgateway.dev/blog/2026-06-17-agentgateway-v1.3.0/
- **Source type:** First-party open-source release announcement and repository
- **Availability status:** Active Apache-2.0 open-source release line
- **Relevant CI/CD stages:** Cross-cutting stages 1-8
- **Tool category:** MCP/A2A/LLM gateway, authentication, policy, rate limits and observability
- **Geography:** Global open-source community
- **Key facts:** The gateway provides MCP, A2A and LLM traffic control with JWT/API-key/OAuth mechanisms, CEL-based authorization, rate limiting, TLS, OpenTelemetry, cost tracking, guardrails and fine-grained MCP policies. The repository showed roughly 2.9k stars at the research snapshot.
- **Why it matters:** Represents the emerging open-source Agent Tool Gateway category that separates Agent reasoning from enterprise policy enforcement and audit.
- **Confidence / limitations:** **High** for code and release facts; low for production maturity inference. Active development and community attention do not prove enterprise reliability or complete protocol security.

#### S84. Governing AI assets at scale with AWS MCP Gateway and Registry

- **Organization / project:** AWS open-source MCP Gateway and Registry
- **Publication / update date:** 2026-06-17
- **URL:** https://aws.amazon.com/blogs/opensource/governing-ai-assets-at-scale-with-mcp-gateway-and-registry/
- **Source type:** Official open-source architecture and project announcement
- **Availability status:** Active open-source project; frequent release cadence
- **Relevant CI/CD stages:** Cross-cutting stages 1-8
- **Tool category:** MCP catalog, gateway, authentication, authorization, audit and high availability
- **Geography:** United States / global open-source community
- **Key facts:** The project provides a central MCP server/tool catalog, authentication and fine-grained invocation controls, records tool calls for audit and documents scalable/high-availability deployment patterns. AWS states a roughly two-week release cadence.
- **Why it matters:** Shows that Agent tooling is becoming a governed enterprise asset plane rather than a collection of developer-local server configurations.
- **Confidence / limitations:** **High** for architecture and project availability. A July 2026 security bulletin fixed a vulnerability in versions 1.0.3-1.0.12, illustrating that the control plane itself is part of the software supply chain and requires rapid patching.

#### S85. Agent Audit: CI-integrated security analysis for AI Agent code

- **Organization / project:** Academic authors / Agent Audit
- **Publication / update date:** 2026-03-24
- **URL:** https://arxiv.org/abs/2603.22853
- **Source type:** Original research preprint with open-source prototype
- **Availability status:** Research prototype
- **Relevant CI/CD stages:** 2, 3
- **Tool category:** Agent-code/configuration security analysis and SARIF CI integration
- **Geography:** International research
- **Key facts:** Agent Audit analyzes Python Agent code, configuration and deployment artifacts and emits SARIF for CI/CD. Its benchmark contains 22 samples and 42 known vulnerabilities; the paper reports detecting 40 with six false positives.
- **Why it matters:** Suggests that CI/CD will need purpose-built checks for Agent-specific code and configuration, not only traditional application SAST.
- **Confidence / limitations:** **Low-medium.** The benchmark is very small, the work is a preprint and the prototype should not be treated as production-grade or comprehensive.

#### S86. ServiceNow Agentic AI in Change Management

- **Organization / project:** ServiceNow Now Assist for ITSM Change Management
- **Publication / update date:** 2026-03-12
- **URL:** https://www.servicenow.com/docs/r/it-service-management/change-management/now-assist-itsm-agentic-ai-in-change.html
- **Source type:** Official product documentation
- **Availability status:** Available in the Australia release with required Now Assist ITSM and Change Management components
- **Relevant CI/CD stages:** 7
- **Tool category:** ITSM, change-risk analysis, scheduling and change-record workflow
- **Geography:** United States / global enterprise service
- **Key facts:** Agentic workflows can assess conflicts and information quality, identify affected configuration items/services, find change windows, create standard/normal/emergency change requests and propose standard-change templates from history.
- **Why it matters:** Extends the Agentic CI/CD view beyond code and deployment platforms into the enterprise change-management system that often controls production release approval.
- **Confidence / limitations:** **High** for documented capability. The source does not show autonomous approval, end-to-end pipeline integration or customer outcome data.

#### S87. Automic Automation V26

- **Organization / project:** Broadcom Automic Automation
- **Publication / update date:** 2026-04-09
- **URL:** https://support.broadcom.com/web/ecx/support-content-notification/-/external/content/ReleaseAnnouncements/0/37310
- **Source type:** Official GA release announcement
- **Availability status:** GA
- **Relevant CI/CD stages:** 4, 5, 6, 7, 8
- **Tool category:** Enterprise workload automation, AI Job, MCP client/server, RBAC and audit
- **Geography:** United States / global enterprise product
- **Key facts:** V26 introduces MCP client/server capabilities and an AI Job type so model reasoning can be embedded as a governed orchestration object. External models can trigger deterministic Automic actions under existing RBAC, security policy and audit controls.
- **Why it matters:** Adds a traditional enterprise-automation example of the same two-layer architecture: probabilistic reasoning inside a deterministic, governed execution system.
- **Confidence / limitations:** **High** for GA and documented components. Automic spans far beyond software delivery, and the source does not provide CI/CD-specific customer outcome evidence.

#### S88. LaunchDarkly AgentControl and Adaptive Triggers

- **Organization / project:** LaunchDarkly AgentControl
- **Publication / update date:** 2026-05-12; Adaptive Triggers announced 2026-05-18
- **URL:** https://launchdarkly.com/blog/introducing-agentcontrol/
- **Source type:** Official product announcements
- **Availability status:** AgentControl available; Adaptive Triggers closed beta
- **Relevant CI/CD stages:** 7, 8; adjacent Agent release/operations governance
- **Tool category:** Agent configuration versioning, runtime controls, experimentation, monitoring and automated fallback
- **Geography:** United States / global service
- **Key facts:** AgentControl versions and changes prompts, models, tools and runtime parameters, adds traces and experiments, and manages runtime configuration. Adaptive Triggers connects predefined metric thresholds to automatic configuration or fallback changes and is explicitly a closed beta.
- **Why it matters:** Shows release engineering expanding to the Agents themselves: prompts, tools and model settings become versioned production assets with observe-act rollback loops.
- **Confidence / limitations:** **High** for product/status facts. This is adjacent evidence about operating deployed AI Agents, not evidence that an Agent autonomously operates a conventional CI/CD pipeline.

#### S89. JFrog Fly agentic artifact repository

- **Organization / project:** JFrog Fly
- **Publication / update date:** 2025-10-28
- **URL:** https://jfrog.com/blog/introducing-jfrog-fly-agentic-artifact-repository/
- **Source type:** Official product blog
- **Availability status:** Beta
- **Relevant CI/CD stages:** 4, 5, 6, 7
- **Tool category:** Semantic artifact repository, version context, deployment descriptors and MCP
- **Key facts:** Fly associates artifacts with commits, pull requests, issues and change metadata, exposes context through MCP and supports natural-language version discovery and generation of Helm, manifest and Kubernetes descriptors.
- **Why it matters:** Illustrates an artifact repository evolving into a semantic version knowledge base for Agents.
- **Confidence / limitations:** **High** for Beta feature description; low for production effects. It does not demonstrate autonomous promotion or approval.

#### S90. JFrog Agent packages, plugins and Skill repositories

- **Organization / project:** JFrog Artifactory SaaS
- **Publication / update date:** 2026-03-31 through 2026-06-29
- **URL:** https://docs.jfrog.com/releases/docs/artifactory-saas-releases
- **Source type:** Official release notes
- **Availability status:** Mixed; Skills repositories Open Beta, Agent package/plugin capabilities released without one common GA label
- **Relevant CI/CD stages:** 2, 5; cross-cutting governance
- **Tool category:** Agent component repository, AI supply chain, semantic malicious-Skill scanning and audit
- **Key facts:** Private repositories can distribute Skills, plugins, prompts, hooks, MCP servers, instructions and Agents. AI Catalog adds semantic malicious-Skill scanning, while worker/webhook context improves action attribution.
- **Why it matters:** Expands the governed software-supply-chain object set from libraries and containers to Agent assets themselves.
- **Confidence / limitations:** **High** for release-note facts. Component maturity varies and none of this proves artifact release autonomy.

#### S91. Octopus MCP Server and Agent service accounts

- **Organization / project:** Octopus Deploy MCP Server
- **Publication / update date:** 2025-10-08; identity guidance updated 2026-06-19
- **URL:** https://octopus.com/docs/octopus-ai/mcp
- **Source type:** Official product and security documentation
- **Availability status:** MCP available and open source; hosted remote MCP roadmap
- **Relevant CI/CD stages:** 6, 7, 8
- **Tool category:** Release orchestration, MCP, Agent service accounts and audit
- **Key facts:** Agents can query and operate projects, releases, deployments and machines. Octopus recommends a separate Agent service account/API key so permissions can be narrowed/revoked and Agent activity distinguished from human operations.
- **Why it matters:** Shows release platforms treating Agents as separately governed actors rather than users' credential proxies.
- **Confidence / limitations:** **High** for documented interfaces. MCP availability is not proof of safe autonomous production deployment.

#### S92. Release safety with LaunchDarkly Vega and GitHub Copilot

- **Organization / project:** LaunchDarkly / GitHub Copilot
- **Publication / update date:** 2025-10-27
- **URL:** https://launchdarkly.com/blog/release-safety-vega-github-copilot/
- **Source type:** Official integration and workflow article
- **Availability status:** Reference integration; source gives no single GA/Preview label
- **Relevant CI/CD stages:** 1, 7, 8
- **Tool category:** Feature flags, progressive delivery, rollout feedback and cleanup PRs
- **Key facts:** Copilot can use LaunchDarkly MCP to inspect flag configuration and create a cleanup PR after full rollout. Vega connects rollout alerts to commits and flags to support pause, rollback or continue decisions.
- **Why it matters:** Connects runtime rollout evidence back to source changes and reviewable cleanup work.
- **Confidence / limitations:** **Medium-high** for the described workflow. Public outcome data and exact availability boundaries are missing.

#### S93. Microsoft Entra Agent ID

- **Organization / project:** Microsoft Entra Agent ID
- **Publication / update date:** Documentation updated 2026-05-01
- **URL:** https://learn.microsoft.com/en-us/entra/agent-id/agent-identities
- **Source type:** Official product documentation
- **Availability status:** Preview
- **Relevant CI/CD stages:** 1-8
- **Tool category:** Agent identity, delegated access, centralized revocation and human sponsorship
- **Key facts:** An Agent identity is a dedicated service principal whose Blueprint acquires application or user-on-behalf-of tokens. Blueprint policies can disable Agents, revoke access and retain the responsible human Sponsor.
- **Why it matters:** Turns Agent identity, delegation and accountability into manageable IAM objects.
- **Confidence / limitations:** **High** for Preview behavior. It is a general Agent identity platform and still requires mapping into each CI/CD tool's authorization model.

#### S94. OAuth support for AWS MCP Server

- **Organization / project:** AWS MCP Server
- **Publication / update date:** 2026-07-09
- **URL:** https://aws.amazon.com/blogs/security/introducing-oauth-support-for-aws-mcp-server/
- **Source type:** Official security engineering blog
- **Availability status:** OAuth support available; parent AWS MCP Server GA
- **Relevant CI/CD stages:** 1-8
- **Tool category:** Headless Agent authorization, short-lived OAuth tokens, IAM and CloudTrail
- **Key facts:** Headless Agents can exchange SigV4 credentials for one-hour OAuth tokens without static client secrets. Requests remain subject to IAM, SCP, RCP and permission boundaries and gain introspection, revocation and additional CloudTrail fields.
- **Why it matters:** Directly addresses short-lived authorization for unattended CI/CD Agent tasks.
- **Confidence / limitations:** **High** for AWS resource access. It does not by itself establish task-level authorization or cross-platform identity.

#### S95. Amazon Bedrock AgentCore Evaluations GA

- **Organization / project:** AWS AgentCore Evaluations
- **Publication / update date:** 2026-03-31
- **URL:** https://aws.amazon.com/about-aws/whats-new/2026/03/agentcore-evaluations-generally-available/
- **Source type:** Official GA announcement
- **Availability status:** GA
- **Relevant CI/CD stages:** Cross-cutting 1-8
- **Tool category:** Agent regression evaluation, behavior assertions, production sampling and observability
- **Key facts:** The service supports on-demand CI/CD regression tests and online production sampling, with built-in and custom evaluators, ground truth, behavior assertions and expected tool sequences.
- **Why it matters:** Makes the Agent itself a release artifact whose behavior and tool trajectory require pre-release and post-release quality gates.
- **Confidence / limitations:** **High** for service availability. It does not validate LLM-judge accuracy, cost or CI/CD-specific long-task coverage.

#### S96. Google Cloud Agent Gateway

- **Organization / project:** Google Cloud Gemini Enterprise Agent Platform
- **Publication / update date:** Documentation updated 2026-07-11
- **URL:** https://docs.cloud.google.com/gemini-enterprise-agent-platform/govern/gateways/agent-gateway-overview
- **Source type:** Official product documentation
- **Availability status:** Documented available; page does not supply one common GA/Preview label
- **Relevant CI/CD stages:** Cross-cutting 1-8
- **Tool category:** Agent/Tool/Agent-to-Agent gateway, identity, semantic policy and audit-only dry runs
- **Key facts:** The Gateway governs user-Agent, Agent-tool and Agent-Agent traffic. It can use Agent Identity as the principal, combine IAM/IAP and semantic policies, deny unregistered tools by default and authorize by Agent, tool and read/write scope.
- **Why it matters:** Resembles the control plane required for CI/CD Agents spanning repositories, pipelines, artifacts and clouds.
- **Confidence / limitations:** **High** for documentation. It is general Agent infrastructure and public production-adoption scale is absent.

#### S97. Google Cloud Online Monitors for production Agents

- **Organization / project:** Google Cloud Agent evaluation
- **Publication / update date:** Documentation updated 2026-07-10
- **URL:** https://docs.cloud.google.com/gemini-enterprise-agent-platform/optimize/evaluation/evaluate-online
- **Source type:** Official product documentation
- **Availability status:** Preview / Pre-GA
- **Relevant CI/CD stages:** Cross-cutting 1-8
- **Tool category:** Production trace sampling, Agent evaluation, drift monitoring and alerting
- **Key facts:** Scheduled monitors sample production traces, score them with built-in or custom metrics and write results into Logging/Monitoring. Sampling and per-round limits manage cost, while OpenTelemetry Agent/tool/message signals provide behavior context.
- **Why it matters:** Extends Agent release gates into ongoing production behavior and quality-drift monitoring.
- **Confidence / limitations:** **High** for Preview behavior. Trace capture may expose source code, credentials or private content; CI/CD-specific templates are not shown.

#### S98. GenAI observability in OpenTelemetry

- **Organization / project:** OpenTelemetry
- **Publication / update date:** 2026-05-14
- **URL:** https://opentelemetry.io/blog/2026/genai-observability/
- **Source type:** Official open-source project blog and semantic conventions
- **Availability status:** Experimental / active development
- **Relevant CI/CD stages:** Cross-cutting 1-8
- **Tool category:** Agent traces, tool calls, model metrics, cost and evaluation telemetry
- **Key facts:** Emerging conventions cover Agent invocation, model calls, tool call/result events, tokens, latency and finish reasons, enabling a cross-vendor call tree and evaluation trail.
- **Why it matters:** Provides a vendor-neutral observability substrate for multi-platform CI/CD Agent workflows.
- **Confidence / limitations:** **High** for current experimental specifications. The standard is moving and data capture can create new secret/privacy exposure.

#### S99. MCP Authorization specification 2025-11-25

- **Organization / project:** Model Context Protocol
- **Publication / update date:** 2025-11-25
- **URL:** https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization
- **Source type:** Official protocol specification
- **Availability status:** Published specification
- **Relevant CI/CD stages:** Cross-cutting 1-8
- **Tool category:** OAuth 2.1, PKCE, audience binding, incremental scopes and token separation
- **Key facts:** The specification requires protected-resource metadata, PKCE, resource audience binding, incremental step-up scopes and separation between MCP and downstream-resource tokens; token passthrough is forbidden.
- **Why it matters:** Supplies a concrete minimum authorization checklist for remote repository, artifact and deployment tool servers.
- **Confidence / limitations:** **High** as a protocol baseline. Compliance does not prove safe implementation or business-level authorization.

#### S100. OWASP Top 10 for Agentic Applications 2026

- **Organization / project:** OWASP GenAI Security Project
- **Publication / update date:** 2025-12-09
- **URL:** https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/
- **Source type:** Community security framework
- **Availability status:** Published guidance; not a certification standard
- **Relevant CI/CD stages:** Cross-cutting 1-8
- **Tool category:** Agent threat modeling, tool misuse, identity, supply chain and code execution
- **Key facts:** The taxonomy covers goal hijacking, tool misuse, identity/privilege abuse, Agentic supply chain and unexpected code execution, with operational mitigation guidance.
- **Why it matters:** Provides a common threat vocabulary for high-privilege CI/CD Agents and third-party Skill/MCP adoption.
- **Confidence / limitations:** **Medium-high** as expert consensus. It does not measure prevalence or provide executable conformance tests.

#### S101. Spotify: Coding is no longer the constraint

- **Organization / project:** Spotify engineering
- **Publication / update date:** 2026-06-03
- **URL:** https://engineering.atspotify.com/2026/6/code-with-claude-coding-is-no-longer-the-constraint
- **Source type:** Official first-party engineering case study
- **Availability status:** Internal production practice
- **Relevant CI/CD stages:** 1-5, 7
- **Tool category:** Internal developer platform, fleet changes, repository automation and review workflow
- **Key facts:** Spotify reports weekly AI use above 99%, 94% self-reported productivity improvement and 76% higher PR frequency. Backstage exposes component ownership, documentation and tools through MCP/CLI, while golden state and lint feedback support fleet-wide change automation.
- **Why it matters:** Shows coding acceleration shifting the bottleneck to review capacity, decision quality and machine-readable platform context.
- **Confidence / limitations:** **Medium-high.** Metrics are first-party/self-reported; more than 2.5 million automated maintenance PRs include deterministic automation and cannot all be attributed to AI.

#### S102. Spotify Honk feedback loops for background coding Agents

- **Organization / project:** Spotify Honk
- **Publication / update date:** 2025-12-09
- **URL:** https://engineering.atspotify.com/2025/12/feedback-loops-background-coding-agents-part-3
- **Source type:** Official first-party engineering case study
- **Availability status:** Internal production practice
- **Relevant CI/CD stages:** 1, 3, 4
- **Tool category:** Background coding Agent, repository-specific verification, LLM judge and sandbox
- **Key facts:** Honk selects repository-specific format/build/test verifiers and withholds PR creation on deterministic failure, then uses an LLM judge to detect scope expansion. Spotify reports the judge rejecting about 25% of thousands of sessions and the Agent correcting roughly half of those rejections.
- **Why it matters:** Provides concrete operating evidence for deterministic checks followed by semantic judgment, with PR as the final boundary.
- **Confidence / limitations:** **Medium-high.** Judge evaluation remained incomplete; functional correctness beyond tests remained a major failure mode.

#### S103. Meta performance-regression Agent and fix-forward PRs

- **Organization / project:** Meta capacity efficiency / AI Regression Solver
- **Publication / update date:** 2026-04-16
- **URL:** https://engineering.fb.com/2026/04/16/developer-tools/capacity-efficiency-at-meta-how-unified-ai-agents-optimize-performance-at-hyperscale/
- **Source type:** Official first-party engineering case study
- **Availability status:** Internal production practice
- **Relevant CI/CD stages:** 1, 3, 7, 8
- **Tool category:** Performance regression detection, deployment feedback, code attribution and repair PR
- **Key facts:** Shared MCP tools and Skills support detection and repair. The system can identify regressions, trace them to a PR and send a fix-forward PR to the original author; Meta reports diagnosis dropping from about ten hours to roughly thirty minutes.
- **Why it matters:** Shows runtime performance feedback closing the loop back to code while preserving author review.
- **Confidence / limitations:** **Medium-high.** All figures are first-party; the broader recovered-capacity claim cannot be attributed solely to the Agent.

#### S104. Alibaba AACR-Bench and enterprise Agentic Code Review

- **Organization / project:** Alibaba / AACR-Bench
- **Publication / update date:** 2026-03-12
- **URL:** https://developer.aliyun.com/article/1716140
- **Source type:** Official developer-community article and first-party open-source benchmark
- **Availability status:** Internal practice plus open research benchmark
- **Relevant CI/CD stages:** 1, 2
- **Tool category:** Repository-level Agentic review, tool use and evaluation
- **Key facts:** The article reports internal review assistance serving tens of thousands of developers and AI contributing more than half of effective review comments. AACR-Bench spans ten languages, repository-level context and expert-curated evaluation.
- **Why it matters:** Combines Chinese enterprise adoption evidence with a reproducible review evaluation asset.
- **Confidence / limitations:** **Medium.** Internal metrics are not independently audited and community-article claims should be kept distinct from benchmark evidence.

### General-purpose Agent harnesses and tool interfaces

#### S105. Claude Code CLI reference

- **Organization / project:** Anthropic / Claude Code
- **Publication / update date:** Live documentation, accessed 2026-07-14
- **URL:** https://docs.anthropic.com/en/docs/claude-code/cli-usage
- **Source type:** Official product documentation
- **Availability status:** Available product documentation
- **Relevant CI/CD stages:** Cross-cutting 1-8; direct automation evidence is strongest for stages 1-4
- **Tool category:** Terminal Agent harness, non-interactive execution, MCP and session management
- **Geography:** United States / global service
- **Key facts:** Claude Code supports an interactive terminal session, non-interactive print mode, stdin pipelines, session continuation and MCP configuration. These interfaces allow the same Agent harness to be used by a developer locally or wrapped by a CI runner and GitHub Action.
- **Why it matters:** Separates the general-purpose Agent harness from the CI/CD platform in which it is hosted and provides primary evidence for the Tool-layer taxonomy.
- **Confidence / limitations:** **High** for documented interfaces. Product documentation is mutable and does not prove reliable autonomous operation in a particular enterprise pipeline; permissions, isolation and release gates remain external responsibilities.

#### S106. OpenAI Codex CLI

- **Organization / project:** OpenAI / `openai/codex`
- **Publication / update date:** Active repository and documentation, accessed 2026-07-14
- **URL:** https://github.com/openai/codex
- **Source type:** First-party open-source repository and documentation
- **Availability status:** Available open source
- **Relevant CI/CD stages:** 1, 2, 3, 4; cross-cutting local and runner execution
- **Tool category:** Local coding Agent, CLI, sandbox and approval modes
- **Geography:** United States / global open source
- **Key facts:** Codex CLI is an open-source coding Agent that runs locally and can inspect, modify and execute code in a working directory. Its sandbox and approval modes distinguish Agent reasoning from the authority to perform local actions.
- **Why it matters:** Provides a first-party example of a terminal Agent harness that can be embedded in a controlled runner without being a CI/CD platform itself.
- **Confidence / limitations:** **High** for repository-documented behavior. The repository does not by itself provide enterprise pipeline orchestration, deployment authorization or outcome evidence; these must be supplied by the surrounding platform.

#### S107. OpenCode open-source coding Agent

- **Organization / project:** Anomaly / `anomalyco/opencode`
- **Publication / update date:** Active repository, accessed 2026-07-14
- **URL:** https://github.com/anomalyco/opencode
- **Source type:** First-party open-source repository and documentation
- **Availability status:** Available open source
- **Relevant CI/CD stages:** 1, 2, 3, 4; cross-cutting local and remote Agent execution
- **Tool category:** Provider-agnostic terminal Agent, TUI, client/server runtime and tool permissions
- **Geography:** Global open source
- **Key facts:** OpenCode presents a provider-agnostic coding Agent with a terminal UI and client/server architecture. Its built-in Build and Plan Agents and per-tool permission configuration separate read-oriented planning from broader workspace actions.
- **Why it matters:** Demonstrates that the Agent harness layer can be open, model-provider independent and separately deployed from code repositories or pipeline products.
- **Confidence / limitations:** **High** for repository-documented architecture. It is not evidence of a complete enterprise CI/CD control plane; identity, centralized policy, audit, evaluation and production support require separate assessment. The similarly named archived `opencode-ai/opencode` repository is a different project and is not used here.

## Coverage gaps and cautions

- **Outcome evidence remains thin.** Most sources prove that capabilities exist, but few provide controlled or longitudinal enterprise outcome data. Vendor-reported productivity, acceptance or incident figures should not be treated as causal comparisons.
- **Autonomy is uneven by stage.** Strongest evidence clusters around pull-request review, CI-failure diagnosis/repair and post-release investigation. Native Agent evidence for artifact packaging, repository promotion, semantic version decisions and production release authorization is much thinner.
- **Control-plane autonomy is mostly absent.** Many products let Agents propose patches, retry jobs, summarize evidence or execute bounded tools; few permit an Agent to alter pipeline policy, approval rules or production gates without deterministic external controls and human responsibility.
- **China evidence is less granular.** Public primary material confirms Alibaba AI review, Huawei scriptable Agent interfaces and Tencent CloudQ operations workflows, but release-state precision and outcome studies are scarcer than for US vendors. Current public evidence from other large Chinese engineering organizations is a gap.
- **Artifact action surfaces now exist, but promotion authority remains unproven.** JFrog, Cloudsmith and Sonatype show Agent-readable and partly actionable supply-chain interfaces; no strong primary source demonstrates reliable autonomous provenance approval, retention policy change or cross-environment promotion.
- **Open-source attention is not maturity.** Star counts are 2026-07-14 snapshots and can change quickly; they measure attention, not security, operational reliability, enterprise support or autonomy level.
- **Preview terminology is inconsistent.** Technical preview, public preview, beta, early access and roadmap/upcoming were preserved as each source uses them. They are not normalized to one common readiness scale in this L0 register.
- **Live documentation can drift.** Some product documentation lacks an immutable publication date or can be updated in place. Such entries are marked with lower date confidence and should be rechecked before final publication.
- **Research results age quickly.** 2026 model and benchmark results are useful snapshots, not enduring capability ceilings; main-report conclusions should rely on architectural and governance patterns more than a single model score.
