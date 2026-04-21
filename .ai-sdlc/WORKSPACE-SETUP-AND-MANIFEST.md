# Workspace Setup And Manifest

This document consolidates the workspace setup, manifest, and sync model for the AI Agentic Delivery Framework.

## Global setup commands

Use a small global setup layer:
- `/ai-init`
- `/ai-status`
- `/ai-sync`

Rule:
- global commands initialize and maintain the framework
- agent commands do the delivery work

## `/ai-init`

`/ai-init` has 2 modes.

### First run

Use `/ai-init` as workspace setup.

It should:
1. detect the workspace root
2. ask for workspace and project identity when missing
3. determine which connectors are required
4. inspect available MCP and tool connectivity when possible
5. create or update `.agent-config.yml`
6. create or update `ai-sdlc.manifest.yml`
7. scaffold `knowledge/` if missing
8. verify connector readiness
9. return setup status and next recommended command

### Re-run

Use `/ai-init` as a gap-check and repair step.

It should:
1. inspect current workspace state
2. compare config, manifest, connectors, and known baselines
3. identify what is missing or drifted
4. ask only for missing blocking setup inputs
5. update config safely
6. return current readiness and next recommended step

## What `/ai-init` writes

Recommended minimum write set:
- `.agent-config.yml`
- `ai-sdlc.manifest.yml`
- `knowledge/` scaffold if missing
- optional starter framework docs only if missing

Recommended rule:
- do not create delivery artifacts such as BRDs, PRDs, tickets, or technical designs during `/ai-init`
- create delivery-record local mirrors only when the first initiative starts

## `.agent-config.yml`

This file is the active workspace configuration.

It is workspace-local and must never be auto-overwritten during sync.

Recommended schema:

```yaml
version: "1.0"

workspace:
  name: "Your Company"
  project: "Your Project"

connectors:
  docs:
    tool: outline
    url: ""
    mcp_url: ""
    api_key: "${DOCS_API_KEY}"
    status: not-configured

  tickets:
    tool: plane
    url: ""
    mcp_url: "stdio"
    mcp_command: ""
    api_key: "${TICKETS_API_KEY}"
    workspace_slug: ""
    status: not-configured

  code:
    tool: github
    url: ""
    mcp_url: ""
    default_branch: "main"
    skills_repo: ""
    status: not-configured

  design:
    tool: figma
    url: ""
    mcp_url: ""
    status: not-configured

executor:
  primary:
    tool: claude-code
    model: ""
  fallback:
    tool: gemini-cli
    model: ""
  zero_cost:
    tool: opencode
    model: ""

handoff:
  store: docs
  folder: "AI-SDLC / Handoffs"
```

## `ai-sdlc.manifest.yml`

This file tracks framework installation ownership and workspace setup readiness.

It should stay focused on framework state, not delivery-state details.

Recommended schema:

```yaml
framework:
  name: ai-sdlc
  version: "0.1.0"
  installed_at: "2026-04-17"
  install_mode: git

workspace:
  name: "Your Company"
  project: "Your Project"
  initialized: true
  initialized_at: "2026-04-17"

executors:
  primary: claude-code
  fallback: gemini-cli
  zero_cost: opencode

connectors:
  docs:
    required: true
    status: connected
  tickets:
    required: true
    status: connected
  code:
    required: true
    status: connected
  design:
    required: false
    status: not-configured

framework_managed:
  - AGENTS.md
  - agents/
  - skills/
  - .agent-config.example.yml
  - PHASES.md
  - COMMANDS.md
  - READY-CHECK.md
  - DELIVERY-RECORD.md

workspace_local:
  - .agent-config.yml
  - knowledge/
  - delivery-records/
  - project-docs/
  - local-skills/
  - local-tools/

capabilities:
  enabled_agents:
    - product-agent
    - design-agent
    - techplan-agent
    - planner-agent
    - engineer-agent
    - incident-engineer-agent
    - devops-agent

setup:
  delivery_record_store: docs
  design_baseline_known: false
  setup_status: ready
```

## Managed vs local ownership

Framework-managed:
- agent definitions
- agent support docs
- reusable skills
- command specs
- install scripts
- templates
- `.agent-config.example.yml`
- other reusable files that define the framework itself

Workspace-local:
- `.agent-config.yml`
- project knowledge base
- delivery records
- project-specific docs and notes
- artifact indexes
- team-specific overrides
- project-specific execution context
- local skills
- local helper tools

Rule:
- framework-managed files may be updated by install, sync, or upgrade
- workspace-local files must never be overwritten automatically

## Installed workspace structure

Do not use `node_modules` as the framework home.

Recommended structure:

```text
.ai-sdlc/
  AGENTS.md
  agents/
  skills/
  templates/
  commands/
  manifests/
  .agent-config.example.yml
.agent-config.yml
knowledge/
delivery-records/
local-skills/
local-tools/
```

Rule:
- `.ai-sdlc/` is the framework-managed layer
- project knowledge, config, delivery records, and local extensions stay outside it

Important distinction:
- this repository is the framework source repository
- an installed target workspace should place the framework-managed static files under `.ai-sdlc/`

## `/ai-sync`

`/ai-sync` should never silently overwrite framework files.

Recommended sync flow:
1. inspect installed framework version and manifest
2. compare framework-managed files with source
3. show the user what would change
4. explain risks if local divergence exists
5. ask for permission before applying updates
6. apply only approved framework-managed changes

Rule:
- `/ai-sync` informs first
- `/ai-sync` requires user approval before changing framework-managed files
- workspace-local files are never touched by sync

## `knowledge/` scaffold

Recommended scaffold created by `/ai-init`:

```text
knowledge/
  workspace/README.md
  product/README.md
  architecture/README.md
  design/README.md
  engineering/README.md
  operations/README.md
```

## Delivery-record local scaffold

Recommended local scaffold created when the first initiative starts:

```text
artifacts/
  delivery-records/
    <delivery-id>/
      record.yml
      handoff.md
```
