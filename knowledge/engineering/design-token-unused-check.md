# Design Tokens Marked "Unused" Are Never Truly Unused

## Rule

When a design source fetch reports a color, variable, or token as "defined but unused" — never accept that at face value. Always cross-check manually.

## Why

In DLV-001-4/5, `--accent: #F59E0B` (amber) was summarized as "defined but unused." It was actually the color for an amber pulse dot above the "Click Anywhere" CTA, tied to the `pulse-orb` animation. The element was missed entirely until human review.

## Cross-check steps

For every token flagged as unused during pre-PR design review:

1. Search CSS for animations referencing elements without explicit color — they may inherit the token
2. Check if any interactive state (hover, click, focus, active) applies the token
3. Look for elements whose semantic role matches the token name (accent = indicator dot, highlight, CTA marker)
4. If still unclear — flag in the PR: *"Accent token `#F59E0B` present but usage unconfirmed — verify with design before merge"*

## Applies to

Any design token type: colors, spacing, border-radius, font-weight, opacity, z-index.

A token defined in `:root` or a Tailwind config always has intent. If you can't find where it's used, you haven't looked hard enough — or you've found a gap to flag.
