# Wizard Options Pattern

Status: active — applied to all agent *-plan commands

## Concept

When an agent presents decisions, it goes **one question at a time**:

1. Ask one decision with options + inline pros/cons
2. Wait for human answer
3. Ask next decision
4. After all decisions answered → output a summary of all choices
5. Ask human to review and approve before proceeding

Never present multiple decisions at once.

## Question format

```
**Decision N — Topic**

**[A] Option name** — `+ Pro: ...` / `- Con: ...`
**[B] Option name** — `+ Pro: ...` / `- Con: ...`
**[M] Enter manually** — type your own answer

Your choice (A / B / M):
```

Rules:
- One question per message. Stop and wait.
- Keep pros/cons to one line each
- Max 3 options (A/B/C) before [M]
- [M] always present as last option

## Conclude + review step

After all decisions are answered, output a summary:

```
**Summary — all decisions**

| Decision | Choice | Value |
|---|---|---|
| Animation library | A | GSAP 3 |
| Deploy target | B | Netlify |
| Build step | A | None (CDN) |

Approve to proceed? (yes / change N):
```

Human can say "yes" to approve or "change 2" to revisit a specific decision.

## Where to apply

All `*-plan` commands in agent AGENT.md files:
- `/agent-product-plan`
- `/agent-design-plan`
- `/agent-techplan-plan`
- `/agent-planner-plan`
