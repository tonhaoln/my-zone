---
name: signal-log
description: Append an observation to the operator's signals file. Triggered by "log this in <domain> [to <target>] [as creed-adjacent]: <observation>" or natural equivalents like "note this", "add a signal", "log a signal". Also fires when operator approves a signal candidate explicitly presented by the agent. Domain is operator-defined (e.g. patient identifiers, practice areas, time periods). Optional "to <target>" routes to a named file (e.g. "to clinical"). Optional "as creed-adjacent" tags the entry with #creed-adjacent for later creed-promotion review.
---

## Trigger condition

**Explicit trigger:** Operator writes `log this in <domain> [to <target>] [as creed-adjacent]: <observation>` or a natural-language equivalent ("note this", "add a signal", "log a signal"). Conversation context up to the trigger phrase is the evidence trail.

**Approval trigger:** fires when operator approves a signal candidate that was explicitly presented by the agent in the preceding context ("approve", "yes", "log it", or equivalent). Domain, observation, and any markers (`#creed-adjacent`) are derived verbatim from the presented candidate. Only fires when a signal candidate was explicitly proposed in the preceding turn — does not fire on generic approvals unrelated to a signal write. If multiple candidates were presented and the approval is ambiguous, surface a clarification prompt before writing.

## Behavior

Append an entry to the appropriate signals file. Do not paraphrase. Verbatim rule applies in both trigger modes:
- Explicit trigger: write what the operator typed after the colon, verbatim.
- Approval trigger: write the presented candidate text, verbatim.

**Entry format:**

```
YYYY-MM-DD | domain | observation [→ context] [#creed-adjacent]
```

- `domain`: operator-supplied domain label (patient/case identifier, practice area, time period, or any operator-defined convention)
- `observation`: verbatim text from operator's input after the colon
- `→ context`: optional additional context field if operator provides it
- `#creed-adjacent`: optional marker; add when operator includes "as creed-adjacent" in their trigger

**Append at top** of the signals section (most-recent-first order).

**Routing:**
- No `to <target>` specified, no active sensitivity files → write to `signals.md`
- No `to <target>` specified, sensitivity files active → confirmation gate before writing to `signals.md` (see trigger-surface table row 2)
- `to <target>` specified, target contains no `/` → resolve to `signals-<target>.md` at vault root; write if file exists
- `to <target>` specified, target contains `/` → resolve as path (`<target>.md`); write if file exists

**Falsifiability test:** verbatim verification. For explicit trigger: observation written must match what the operator typed after the colon. For approval trigger: entry written must match the presented candidate. Operator reads entry and verifies match.

## Output format

After write: echo the written entry.

```
Logged: YYYY-MM-DD | domain | observation [#creed-adjacent]
```

On confirmation gate (row 2): surface the confirmation prompt before writing. On approval, proceed with write and echo. On rejection, exit without write.

## Error and edge-case handling

Missing domain or observation: surface error with usage. Named target not found: surface error with active targets list. Sensitivity files exist and were excluded: surface footer note unconditionally.

---

## Sensitivity-handling implementation

### Discovery rule

Sensitivity-tier status is determined by frontmatter (`mode: descriptive-sensitive` AND `active: true`), not filename. Skill reads frontmatter of all `signals*.md` files to determine sensitivity state before routing.

### Default scope

Default write target is `signals.md`. Sensitivity-tier files (`mode: descriptive-sensitive`, `active: true`) are excluded from default routing unless explicitly named via `to <target>`.

### Required-explicit invocation

Operator must name the sensitivity target explicitly via `to <target>`. Skill does not infer, suggest, or default to sensitivity files. Under sensitivity-active state, general routing triggers a confirmation gate.

### Trigger-surface table

| Trigger condition | Surface |
|---|---|
| Valid domain + observation; no active sensitivity files | `[Write]` entry appended to `signals.md` |
| Valid domain + observation; sensitivity files active; no explicit `to <target>` | `[Confirm]` "Sensitivity tier active. This signal will write to `signals.md` (general). Confirm?" |
| `to <target>` resolves to active sensitivity file | `[Write]` entry appended to sensitivity file |
| `to <target>` without `/`; file not found | `[Error]` "No file found for `<target>`. Active targets: `signals.md` [+ active sensitivity file names]." |
| `to <target>` with `/` (path-based); file not found | `[Error]` "No file found at `<target>.md`." |
| `as creed-adjacent` modifier present | `[Write]` same routing rules apply; entry includes `#creed-adjacent` marker |
| Missing observation (nothing after colon, or no colon) | `[Error]` "Observation required. Usage: log this in \<domain\> [to \<target\>] [as creed-adjacent]: \<observation\>" |
| Missing domain | `[Error]` "Domain required. Usage: log this in \<domain\> [to \<target\>] [as creed-adjacent]: \<observation\>" |
| Operator approves presented signal candidate; domain + observation clear from context | `[Write]` entry derived from presented candidate verbatim; routing rules apply as normal |
| Operator approves but multiple candidates were presented and approval is ambiguous | `[Clarify]` "Which entry are you approving?" |
| Sensitivity-tier file exists, active, excluded from this invocation | `[Read]` footer: "Sensitivity-tier files exist and were excluded. Specify `to <target>` to include — e.g., `log this in <domain> to <tier-name>: <observation>` for any active sensitivity tier." |
