---
name: Ticket Lifecycle
description: How Status and Execution Status fields move through the engineer-agent ticket lifecycle
type: project
---

## Fields

Notion Tasks DB has two status fields:

| Field | Type | Purpose |
|---|---|---|
| `Status` | Notion status | Coarse state — visible at board level |
| `Execution Status` | Select | Fine-grained execution gate |

## Lifecycle

| Stage | Status | Execution Status |
|---|---|---|
| Ticket created by planner | Not started | Ready |
| Engineer claims ticket | In progress | Ready |
| PR opened | In progress | In Review |
| PR merged | Done | Done |
| Blocked (any reason) | In progress | Blocked |

## Rules

- Engineer sets `Status = In progress` when claiming
- Engineer sets `Execution Status = In Review` + adds evidence comment with PR URL when PR is opened
- Engineer sets `Status = Done` + `Execution Status = Done` only after PR is confirmed merged
- Never mark `Done` before merge is confirmed
- If blocked: set `Execution Status = Blocked`, add comment with reason

## Execution Status options

- `Ready` — ticket is ready to be picked up
- `In Review` — PR is open, waiting for merge
- `Blocked` — blocked by missing input, contract issue, or dependency
- `Done` — PR merged, ticket complete
