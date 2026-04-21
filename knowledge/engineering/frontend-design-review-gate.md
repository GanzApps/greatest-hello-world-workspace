# Frontend Design Review Gate (Pre-PR)

## Rule

Before opening a PR on any frontend ticket, the engineer-agent must fetch the live design source and diff every visual property against the implementation. Do not rely solely on the tech design spec.

## Why

In DLV-001-3, visual mismatches (wrong background, missing gradient blobs, wrong font, wrong text gradient) were only caught after the human reviewer flagged them post-PR. The AGENT.md rule existed but was not enforced during implementation.

## Checklist (run before every frontend PR)

- [ ] Fetch live design source (Stitch screen HTML, Figma frame, etc.)
- [ ] List every visual property: background color, gradients, fonts, font weights, colors, animations, layout, spacing
- [ ] For each property — confirm the implementation matches
- [ ] For canvas/WebGL contexts — confirm CSS assets are converted to WebGL equivalents (see `webgl-canvas-css-gap.md`)
- [ ] Screenshot or visual evidence attached to PR
- [ ] If design source is unreachable — set ticket to Blocked, do not open PR on assumption

## Scope

Applies to all tickets tagged `[FE]`, `[UI]`, `[DESIGN]`, or any ticket that produces a user-visible rendered output.

## How to fetch the raw design HTML (Google Stitch)

```bash
# Step 1 — get the download URL
mcp__stitch__get_screen → copy htmlCode.downloadUrl

# Step 2 — download raw HTML
curl -s "<downloadUrl>"
```

**Never use WebFetch on Stitch/design URLs.** WebFetch summarizes the content and loses critical detail: exact Tailwind classes, shadow values, font weights, token usage in markup. `curl` returns the full raw source.

## What counts as the design source-of-truth

In priority order:
1. Raw HTML from `curl` on Stitch `htmlCode.downloadUrl` — exact classes and styles
2. Live Stitch / Figma screen (visual reference)
3. PNG/screenshot exports (supporting reference only)

Never use only the tech design spec as the visual reference — it describes intent, not exact pixel values.
