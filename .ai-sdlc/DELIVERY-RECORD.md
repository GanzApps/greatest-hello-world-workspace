# Delivery Record

The delivery record is the initiative-level state tracker for this framework.

Use it to track:
- what is being delivered
- the current phase
- artifact links
- readiness
- blockers
- current and next role

Do not use it as a workspace knowledge base.

## Separation of responsibility

- `knowledge/` = workspace memory
- delivery record = initiative state
- `artifacts/` = role output and evidence

## Recommended location

- primary source of truth in the docs connector
- local mirror under `artifacts/delivery-records/`

## Recommended schema

```yaml
id: DLV-001
title: Example initiative
type: feature
status: draft
current_phase: initiation
work_mode: new-feature

workspace:
  company: Your Company
  project: Your Project

source:
  intake_type: brd
  intake_url: <url>

artifacts:
  delivery_record_url: <url>
  prd_url: ""
  design_baseline_url: ""
  design_spec_url: ""
  techplan_url: ""
  tickets_url: ""
  code_urls: []
  evidence_urls: []
  deploy_url: ""

readiness:
  connectors:
    docs: connected
    tickets: connected
    code: connected
    design: connected
  design_baseline_status: unknown
  setup_status: ready
  execution_status: blocked

context:
  summary: ""
  assumptions: []
  decisions: []
  risks: []
  blockers: []

ownership:
  current_role: product-agent
  next_role: product-agent
  approver: human

milestones:
  initiation: draft
  product_definition: pending
  design_baseline: pending
  feature_design: pending
  technical_design: pending
  planning: pending
  execution: pending
  verification: pending
  release_deploy: pending

updated_at: "2026-04-17"
```

## Recommended statuses

- `draft`
- `ready`
- `review`
- `done`
- `blocked`

## Recommended work modes

- `new-product`
- `new-feature`
- `change-existing-product`
- `update-existing-plan`
- `incident`
- `infra-change`

## Local mirror

```text
artifacts/
  delivery-records/
    <delivery-id>/
      record.yml
      handoff.md
```

## Rules

- store links and summaries in the delivery record
- store full documents in connected tools
- store role evidence and execution output under `artifacts/`
