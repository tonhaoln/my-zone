---
name: aggregate-review
description: Surface structural observations and aggregate-coherence questions across kinds. Triggered by "aggregate-review [<kind>] [including history] [over <time-range>]" or natural equivalents like "review my kinds", "check my creed", "how are my signals looking". Surfaces structural facts and observations — not verdicts. Per-kind behavior: signals (pattern surfacing), creed (structural facts only), voice (drift flagging), practice (rule-health observations). Sensitivity files excluded by default.
---

## Trigger condition

Operator writes `aggregate-review [<kind>] [including history] [over <time-range>]` or natural-language equivalents ("review my kinds", "check my signals", "how is my creed looking"). All parameters optional; bare invocation reviews all kinds.

## Behavior

Read kind files and signals files. Surface structural observations. Do not verdict.

**Per-kind review behavior:**

**Signals:** structural facts (entry count, date range, age of oldest entry) plus content-level pattern surfacing. Structural facts surface unconditionally — regardless of whether values are 'concerning.' Observation-shape only:
- *Observation:* "Three signals from the last quarter mention Friday afternoons."
- *Verdict (avoid):* "You have a pattern of Friday-afternoon fatigue."

**Creed:** structural facts only. Report: entry count, date of last update (via git history if available), count of `#creed-adjacent` signals accumulated since last creed update. Do not surface specific creed-adjacent signals (that's `creed-promotion`'s job). Do not propose promotions.

**Voice:** structural observations. Surface voice moves whose phrasing has drifted away from current session writing, voice moves that contradict each other, and counts/freshness facts. Close with aggregate question: "Do these collectively still read like you?"

**Practice:** structural observations — rules unedited for N months (mechanical, via git history where available); rules whose phrasing seems disconnected from current session behavior (interpretive but bounded); rules that contradict each other or observed session behavior. Report per observation. Do not verdict which rule is 'wrong.'

**Cross-kind tensions** (comprehensive invocation): surface the parallel; don't interpret.
- *Observation:* "In the last quarter, three creed-adjacent signals mention X. Two voice moves added in the same period also touch X."
- *Verdict (avoid):* "X is appearing across multiple kinds, suggesting creed itself has shifted."

**Rule-vs-evidence corpus:** current session's content only. Honest about platform reality — no cross-session rule-evidence comparison.

**Time-range grammar:** `over <relative-duration>` (e.g., "last 6 months"), `since <YYYY-MM-DD>`, `until <YYYY-MM-DD>`, `since <date> until <date>`. Time-range affects which entries are surfaced within scoped files; does not affect which files are read.

**`including history` modifier:** include `signals-history.md` in signals review.

**Falsifiability test:** observation-shape audit. Every surfaced item stays observation, not verdict. Operator reads the review and rejects any item whose wording makes a causal or evaluative claim not present in the source content.

## Output format

Sectioned by kind. Structural facts first within each section, then observations.

Empty result: *"No structural observations from this review."* (or per kind: *"No structural observations for [kind] from this review."*)

## Error and edge-case handling

Unrecognized kind in `aggregate-review <kind>`: error with valid kinds list. Sensitivity files exist and excluded: footer note. Kind files don't exist yet (empty zone): surface "no entries" facts for each kind.

---

## Sensitivity-handling implementation

### Discovery rule

Sensitivity-tier status determined by frontmatter. Skill reads frontmatter of all `signals*.md` files before reading content. Sensitivity files excluded from default scope.

### Default scope

`signals.md` (+ `signals-history.md` if `including history` specified, + any explicitly named sensitivity files).

### Required-explicit invocation

Operator must name sensitivity targets explicitly. Skill does not infer or default to sensitivity content.

### Trigger-surface table

| Trigger condition | Surface |
|---|---|
| `aggregate-review` (all kinds, default scope) | `[Read]` structural observations per kind; signals from `signals.md` only |
| `aggregate-review <kind>` | `[Read]` observations for named kind only |
| `aggregate-review including history` | `[Read]` includes `signals-history.md` in signals section |
| `aggregate-review over <time-range>` | `[Read]` filters signal entries to date range within scope |
| `aggregate-review` with named sensitivity target explicitly included | `[Read]` includes named sensitivity file in signals section |
| Unrecognized kind named | `[Error]` "Unrecognized kind `<kind>`. Valid: creed, voice, practice, signals." |
| No structural observations for a kind | `[Read]` "No structural observations for [kind] from this review." |
| Sensitivity-tier file exists, active, excluded from invocation | `[Read]` footer: "Sensitivity-tier files exist and were excluded. Specify the target to include — e.g., `aggregate-review including <tier-name>` for any active sensitivity tier." |
