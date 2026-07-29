---
title: CLI 软件交付链上半图命令来源
as_of: 2026-07-28
artifact:
  - cli-upper-half-c-final-light.png
  - cli-software-delivery-agent-interface-insight-final.pptx
---

# 命令与一手来源

| 作业层 / 阶段 | CLI | 图中示例 | 一手来源 |
|---|---|---|---|
| 跨阶段编排 | Codex CLI | `codex exec "Review latest change"` | [OpenAI Codex repository](https://github.com/openai/codex/blob/main/codex-rs/README.md) |
| 跨阶段编排 | Claude Code | `claude -p "Review latest change"` | [Anthropic CLI reference](https://docs.anthropic.com/en/docs/claude-code/cli-usage) |
| 跨阶段编排 | GitHub Copilot CLI | `copilot -p "Review latest change" -s` | [GitHub programmatic reference](https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-programmatic-reference) |
| 跨阶段编排 | OpenCode | `opencode run "Review latest change"` | [OpenCode CLI](https://opencode.ai/docs/cli/) |
| 编码与协同 | `gh` | `gh pr create --fill` | [GitHub CLI manual](https://cli.github.com/manual/gh_pr_create) |
| 编码与协同 | `glab` | `glab mr create --fill` | [GitLab CLI docs](https://docs.gitlab.com/cli/mr/create/) |
| 检查与门禁 | `sonar-scanner` | `sonar-scanner -Dsonar.token=$TOKEN` | [SonarScanner CLI](https://docs.sonarsource.com/sonarqube-cloud/analyzing-source-code/scanners/sonarscanner-cli) |
| 检查与门禁 | `snyk` | `snyk test` | [Snyk test](https://docs.snyk.io/snyk-cli/commands/test) |
| 检查与门禁 | `trivy` | `trivy fs .` | [Trivy filesystem target](https://trivy.dev/docs/latest/target/filesystem/) |
| 构建与验证 | `circleci` | `circleci local execute build` | [CircleCI local execution](https://circleci.com/docs/guides/execution-managed/run-a-job-in-a-container/) |
| 构建与验证 | `docker` | `docker build -t app:v1 .` | [Docker build](https://docs.docker.com/reference/cli/docker/buildx/build/) |
| 制品与供应链 | `jf` | `jf rt upload "dist/*.jar" libs-release/` | [JFrog CLI command reference](https://docs.jfrog.com/integrations/docs/jfrog-cli-command-reference) |
| 制品与供应链 | `docker` | `docker push $IMAGE:$TAG` | [Docker push](https://docs.docker.com/reference/cli/docker/image/push/) |
| 制品与供应链 | `cosign` | `cosign sign --yes $IMAGE@$DIGEST` | [Sigstore container signing](https://docs.sigstore.dev/cosign/signing/signing_with_containers/) |
| 部署与发布 | `terraform` | `terraform plan -out=tfplan` | [Terraform plan](https://developer.hashicorp.com/terraform/cli/commands/plan) |
| 部署与发布 | `kubectl` | `kubectl apply -f deploy.yaml` | [kubectl apply](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_apply/) |
| 部署与发布 | `helm` | `helm upgrade --install app ./chart` | [Helm upgrade](https://docs.helm.sh/docs/helm/helm_upgrade/) |
| 部署与发布 | `argocd` | `argocd app sync app` | [Argo CD sync](https://argo-cd.readthedocs.io/en/latest/user-guide/commands/argocd_app_sync/) |
| 观测与恢复 | `sentry-cli` | `sentry-cli releases new $RELEASE` | [Sentry CLI repository](https://github.com/getsentry/sentry-cli) |
| 观测与恢复 | `datadog-ci` | `datadog-ci sourcemaps upload dist …` | [Datadog source-map upload](https://docs.datadoghq.com/real_user_monitoring/guide/upload-javascript-source-maps/) |

## 表达边界

- 图中的 `$TOKEN`、`$IMAGE`、`$TAG`、`$DIGEST` 和 `$RELEASE` 是需要替换的运行上下文。
- Agent CLI 的 Headless 入口不自动获得写入、部署或发布授权；认证、权限、审批和外部门禁仍由宿主与下层系统控制。
- GitHub Copilot CLI 的 `-p` prompt mode 在当前命令参考中仍标为实验性，因此图中保留 `EXP` 标记。
- `terraform plan -out=tfplan` 用于强调可审阅的计划工件，不把 Agent 的判断直接等同于生产变更。
