---
name: Human Review Gate
description: Every artifact-producing command must prompt human to review and approve before marking the phase done
type: feedback
---

After every `*-spec` / `*-prd` / `*-tickets` command writes an artifact, the agent must NOT mark the phase done automatically.

**Why:** Human is the reviewer. No artifact is done until the human explicitly approves.

**How to apply:**
- End every artifact-producing command with a review prompt
- Wait for human to reply "approve" or "request changes: [what]"
- Only mark phase done and output handoff packet after explicit approval
- If changes requested: apply changes, re-prompt review

## Review prompt format

```
**Review required**
Doc written: [URL]

Please open the doc and confirm:
- [ ] Content is accurate and complete
- [ ] Stack / decisions match what was agreed
- [ ] No missing sections

Reply **approve** to mark done, or **request changes: [what]** to revise.
```
