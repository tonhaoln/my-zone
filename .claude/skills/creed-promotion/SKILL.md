---
name: creed-promotion
description: Surface #creed-adjacent-marked signals as a batch with proposed creed-shaped reformulations; operator approves, edits, or rejects per item. Triggered by "creed-promotion [in <target>] [over <time-range>]" or natural equivalents like "promote creed candidates", "review my creed-adjacent signals". Marker-only: only tagged signals appear. Reformatting, not interpretation. No in-session creed writes without per-item operator approval.
---

## Trigger condition

Operator writes `creed-promotion [in <target>] [over <time-range>]` or natural-language equivalents ("promote creed candidates", "review my creed-adjacent signals", "run creed promotion"). All parameters optional; bare invocation scans default scope.

## Behavior

Scan signals files for entries marked `#creed-adjacent`. Surface as a batch with proposed creed-shaped reformulations. Operator approves, edits-and-approves, or rejects per item.

**Source: marker-only.** Only entries explicitly tagged `#creed-adjacent` at log time appear. Skill does not surface untagged signals or judge which untagged signals might be creed-adjacent.

Retroactive tagging is the documented path for "I realized later" cases: operator edits the signal entry to add the marker, then re-runs creed-promotion.

**Reformatting-not-interpreting (the load-bearing constraint):**

The proposed reformulation is reformatting only. Remove date and domain. Restate as a present-tense governing principle. Do not expand, condense, or shift meaning. Operator audits via substance comparison.

**Falsifiability test with concrete cases:**
- *Clean reformatting:* Signal "I work better when allowed to disagree with framing" → Creed "I do my best work when I can disagree with framing rather than reflexively accept it." (Date/domain removed; substance unchanged.) ✓
- *Drift via expansion:* same signal → Creed "Disagreement with framing is essential to good work." (Expanded "I work better" to "essential"; interpretation, not reformatting.) ✗
- *Drift via dropped qualifier:* "I sometimes work better..." → "Push back early on requirements." (Dropped "sometimes"; substance changed.) ✗

**Reformulation-note discipline:** when a note accompanies the reformulation, describe transformation mechanics only — what was removed, restructured, what shape it now has. Notes that describe *meaning* are interpretation, even when the entry beneath them is correctly reformatted.

**Per-candidate output format:**

```
Signal: YYYY-MM-DD | domain | observation #creed-adjacent
Proposed: [reformulated creed entry]
Note: [transformation mechanics — optional]
Source: [file + line reference]
```

**Per-item operator approval:** for each candidate, operator approves, edits-and-approves, or rejects. On approval: write proposed entry to creed.md. On rejection: skip; move to next. On edit-and-approve: write operator's edited version.

**Source signal preservation:** signals remain in their file after promotion; promoted signals are not marked or removed. The operator tracks promotion history (e.g., via commit messages or personal notes).

**Falsifiability test:** substance comparison. Operator reads proposed creed entry and compares against source signal. If it says more, less, or differently than the signal substance → drift; reject or edit.

## Output format

Batch header naming scope (file(s) scanned, count of #creed-adjacent entries found). Candidates one by one, each with the per-item approval gate. After all candidates processed: summary of approvals and rejections.

Empty result: *"No creed-adjacent signals found in this scope."*

## Error and edge-case handling

Named target doesn't exist or isn't active: error with active targets. Sensitivity files exist and excluded: footer. No #creed-adjacent markers in scope: uniform empty message. Creed.md doesn't exist yet: propose creating it before writing the first entry.

---

## Sensitivity-handling implementation

### Discovery rule

Sensitivity-tier status determined by frontmatter. Skill reads frontmatter of all `signals*.md` files before reading content. Sensitivity files excluded from default scope.

### Default scope

`signals.md` (+ `signals-history.md` if it exists). Sensitivity-tier files excluded unless explicitly named via `in <target>`.

### Required-explicit invocation

Operator must name sensitivity target explicitly to include sensitivity signals in the promotion batch. No inference, no defaulting.

### Trigger-surface table

| Trigger condition | Surface |
|---|---|
| `creed-promotion` — default scope, no sensitivity active | `[Read]` batch from `signals.md` [+ `signals-history.md` if exists], #creed-adjacent entries only |
| `creed-promotion` — sensitivity files active, no explicit `in <target>` | `[Read]` batch from general scope only; sensitivity excluded; footer fires |
| `creed-promotion in <target>` — named target resolves to active sensitivity file | `[Read]` batch from named sensitivity file, #creed-adjacent entries only |
| `creed-promotion over <time-range>` | `[Read]` time-range-filtered batch from scope |
| Per-candidate surfaced | `[Confirm]` "Approve this promotion, edit, or reject?" |
| Named target doesn't exist or isn't active | `[Error]` "No active file found for `<target>`. Active targets: [list]." |
| No #creed-adjacent markers found in scope | `[Read]` "No creed-adjacent signals found in this scope." |
| Sensitivity-tier file exists, active, excluded from invocation | `[Read]` footer: "Sensitivity-tier files exist and were excluded. Specify `in <target>` to include — e.g., `creed-promotion in <tier-name>` for any active sensitivity tier." |
