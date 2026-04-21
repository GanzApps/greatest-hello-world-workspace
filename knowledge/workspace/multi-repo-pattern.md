---
name: Multi-Repo Pattern
description: How repos are declared and resolved across agent-config, System Catalog, and tickets
type: project
---

## Pattern

`agent-config.yml` code connector has two roles:
1. `url` — the AI SDLC workspace repo (where framework artifacts and knowledge live)
2. `catalog_url` — Notion System Catalog page (source of truth for all service repos)

Tickets reference a **service name** (e.g. `tghw_fe`) not a raw repo URL.
Engineer-agent resolves the actual repo URL by looking up the service name in System Catalog.

**Why:** Single source of truth for service→repo mapping. Config doesn't grow per service. Catalog already exists in the framework.

**How to apply:**
- When creating tickets, set Target Repository = service name from catalog (e.g. `tghw_fe`)
- Engineer-agent fetches System Catalog, finds the service entry, reads repo URL from there
- When a new service/repo is created, add it to System Catalog — tickets auto-resolve

## Service names for this project

| Service | Catalog Name | Repo |
|---|---|---|
| Next.js frontend | `tghw_fe` | TBD — created in ticket DLV-001-1 |
| AI SDLC workspace | `tghw_workspace` | TBD — workspace repo |
