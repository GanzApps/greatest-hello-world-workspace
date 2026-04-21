# Agent Session Flow Contract

## Core Rule

One command = one step. Stop. Wait for human.

Never chain agent commands automatically within a session. Each sub-command (init, plan, explore, spec, baseline, slice, tickets, context, implement, deploy) is a discrete step with its own output and a hard stop. The human triggers each next step explicitly.

## Why This Exists

Observed unexpected behavior: when a human said "run design init", the agent ran the full design workflow (baseline + screen generation) instead of only the init step.

Root cause: AGENT.md files had no per-command definitions, so the agent defaulted to executing the full agent job description.

Fix applied: all AGENT.md files now have a ## Commands section defining each sub-command's exact scope and stop condition.

## Session Contract

- One command triggers one named step
- Agent outputs the result of that step and stops
- Agent must not proceed to the next step unless the human issues the next command
- This applies even when the next step is obvious or the agent knows what it would do
- Approval from the human to proceed is always explicit, never assumed

## Command Flow Per Agent

### Product Agent
init -> plan -> explore (optional) -> prd

### Design Agent
init -> baseline -> plan -> explore (optional) -> spec

### Tech Plan Agent
init -> plan -> explore (optional) -> spec

### Planner Agent
init -> plan -> slice -> tickets

### Engineer Agent
init -> context -> implement (one ticket at a time)

### DevOps Agent
init -> plan -> deploy (only after explicit human approval)

## Continuation Rule

When a new session starts mid-initiative:
1. Read knowledge/
2. Read delivery record from artifacts/delivery-records/
3. Read connected-tool artifacts (PRD, design spec, tech design)
4. Report current state and recommended next command
5. Wait for human to trigger the next command

Do not auto-resume from where the last session ended without human confirmation.

## Plan and Explore Step Rule

> TBD — Each agent's specific decision points and question format need to be discussed and defined per agent.
> What follows is the general contract only. Per-agent detail to be added once aligned with human.

In plan and explore steps, the agent must present options and ask the human before drawing conclusions.

- Identify key decisions (stack, tools, structure, approach)
- Present each as options with a recommendation and reason
- Ask the human to confirm or choose
- Only after human responds: lock the plan and proceed

Do NOT output a fully concluded plan in the plan step. Output options + questions.
This applies to all agents: product, design, techplan, planner, devops.

### Per-agent decision points — TBD

| Agent | What to ask in plan step | Status |
|---|---|---|
| product-agent | TBD | TBD |
| design-agent | TBD | TBD |
| techplan-agent | TBD | TBD |
| planner-agent | TBD | TBD |
| devops-agent | TBD | TBD |

## Plain Language Triggers

These plain-language phrases map to specific commands:

- "design init" -> /agent-design-init (init step only)
- "run design baseline" -> /agent-design-baseline (baseline step only)
- "techplan init" -> /agent-techplan-init (init step only)
- "run techplan spec" -> /agent-techplan-spec (spec step only)

If the human says "run design agent" without a step name, ask which step to run. Do not default to running the full agent.
