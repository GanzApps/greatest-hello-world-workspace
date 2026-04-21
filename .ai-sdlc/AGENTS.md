# AGENTS.md

This workspace is for AI-assisted SDLC using the configured agent executor.

General rules:
- Always create artifacts, not just chat output.
- Start user discovery with `/help` when the next command is unclear.
- Treat `/help` as the main command-discovery tool for this framework.
- Use workspace knowledge, delivery records, connected artifacts, and local artifacts to continue work instead of relying on prior chat.
- Use Product Agent to create PRDs in the documentation tool (docs connector).
- Use Design Agent to create design output in the design tool (design connector).
- Use Tech Plan Agent to create technical documentation in the documentation tool (docs connector).
- Use Engineer Agent to consume approved tickets from the ticket tool and implement code in the target repository.
- For Engineer work, use `artifacts/engineering/` as the workspace handoff/evidence layer, not as the application source tree.
- Engineer Agent must treat the ticket's `Target Repository` as the source-of-truth for where implementation happens.
- If the current workspace does not match the ticket target repository, Engineer Agent should block on repository/workspace alignment and pass an artifact handoff instead of implementing in the wrong repo.
- Use Incident Engineer Agent to handle monitoring-driven incident tickets created by self-healing intake service.
- Use DevOps Agent to accept requests such as `provide <project_name> <environment>`, prepare Terraform branch/PR/plan in the infrastructure repository, then apply and deploy through the CI/CD pipeline only after approval.
- DevOps Agent must update the linked infrastructure ticket status as work moves from design -> implementation -> verification, and should add concise execution comments when a decision or milestone is completed.
- For shared-host app deployments, prefer stable public routing contracts and document final service endpoints explicitly, for example frontend on the root domain and backend on a dedicated API subdomain or reverse-proxy path.
- For Azure Container Apps migration design, prefer ACA ingress over a custom reverse-proxy container, prefer internal ingress for non-public services, prefer managed identity over registry passwords, and prefer Key Vault backed secrets over direct secret values.
- For non-disruptive platform migration, build the ACA path beside the existing VM path first, keep current production-like endpoints untouched until smoke verification passes, and gate first-time ACA applies on hard dependencies such as Key Vault secrets and managed database readiness.
- Ask for approval before moving to the next stage.
- Pass artifacts (URLs, IDs, summaries), not whole chat history.

Workflow:
0. User runs `/help` to see available commands and the recommended next step.
1. Product Agent creates or updates the PRD in the documentation tool (docs connector).
2. Design Agent reads the approved PRD and creates design output in the design tool (design connector).
3. Tech Plan Agent reads the PRD and design output, then writes HLD + impacted services + module details + sequence diagrams in the documentation tool (docs connector).
4. Planner Agent breaks work into engineering tickets in the ticket tool (tickets connector) or the configured tracker with strict FE/BE split and complete ticket body sections.
5. Planner Agent creates or refreshes tickets from approved PRD and technical docs and runs the ticket quality gate before handoff.
6. Engineer Agent consumes `Ready` tickets and moves them through `In Progress` -> `Review` -> `Done` (or `Blocked` if ticket contract is incomplete).
7. Incident Engineer Agent consumes monitoring incident tickets from the documentation tool and delivers fix PRs with evidence back to the ticket tool.
8. DevOps Agent accepts an infrastructure request such as `provide tasktify staging`, prepares the Terraform branch/PR/plan in the infrastructure repository, waits for approval, then applies the target environment and verifies deployment health.

## Configuration

Before starting any work, the agent must:

1. Read `.agent-config.yml` from the repo root
2. Verify all required connectors for the current agent or skill are `connected`
3. Resolve all tool URLs and MCP endpoints from the config
4. If any required connector is not connected, output setup instructions and stop

See `.agent-config.example.yml` for the full config schema and supported tools.

## Command Usage

Setup commands:
- `/help`
- `/ai-init`
- `/ai-status`
- `/ai-sync`

Agent commands:
- Product Agent:
  - `/agent-product-init`
  - `/agent-product-plan`
  - `/agent-product-explore`
  - `/agent-product-prd`
- Design Agent:
  - `/agent-design-init`
  - `/agent-design-baseline`
  - `/agent-design-plan`
  - `/agent-design-explore`
  - `/agent-design-spec`
- Tech Plan Agent:
  - `/agent-techplan-init`
  - `/agent-techplan-plan`
  - `/agent-techplan-explore`
  - `/agent-techplan-spec`
- Planner Agent:
  - `/agent-planner-init`
  - `/agent-planner-plan`
  - `/agent-planner-slice`
  - `/agent-planner-tickets`
- Engineer Agent:
  - `/agent-engineer-init`
  - `/agent-engineer-context`
  - `/agent-engineer-implement`
- Incident Engineer Agent:
  - `/agent-incident-init`
  - `/agent-incident-context`
  - `/agent-incident-implement`
- DevOps Agent:
  - `/agent-devops-init`
  - `/agent-devops-plan`
  - `/agent-devops-deploy`

Recommended usage flow:
1. Run `/help`.
2. Run `/ai-init`.
3. Follow the role commands phase by phase.
4. Run `/ai-status` whenever you need to confirm current state, blockers, or the next command.

## Continuation Rule

Any new AI agent or executor should continue work by reading:
1. `knowledge/`
2. delivery record
3. connected-tool artifacts
4. local `artifacts/`
5. ask only for missing blocking context

Do not depend on full prior chat history for continuation.

## Agent executor

The executor is defined in `.agent-config.yml` under `executor`.

Default priority:
1. Primary   — Claude Code (pay per token, full MCP support)
2. Fallback  — Gemini CLI (1,000 free req/day, MCP supported)
3. Zero-cost — OpenCode (open source, any model via OpenRouter)
4. Emergency — Aider + Ollama (local only, no MCP, last resort)

Agents execute the same role workflows regardless of which executor is used.

## Handoff packet format

Every agent or skill produces this packet at the end of its output:

  type:         [artifact type]
  title:        [artifact name]
  status:       [current status - agent never sets "approved"]
  produced-by:  [agent role]
  next-role:    [next agent role - reminder for human]
  url:          [direct link to artifact in the configured tool]
  depends-on:   [upstream artifact URLs or descriptions]
  instruction:  [complete ready-to-paste prompt for next Claude Code thread]
  blockers:     [empty = green light | describe if blocked]

The instruction field must be a complete, self-contained prompt.
The human pastes it into the next thread without any additional context.
Only the human sets status to "approved". Agents set: draft, ready, review, done.
