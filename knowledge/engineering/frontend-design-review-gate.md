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

## What counts as the design source-of-truth

In priority order:
1. Live Stitch / Figma screen (always prefer over exports)
2. Exported HTML from design tool
3. PNG/screenshot exports (use only as supporting reference when live source matches)

Never use only the tech design spec as the visual reference — it describes intent, not exact pixel values.
