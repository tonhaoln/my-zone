---
name: signals-history
description: Search the operator's signal corpus and return matching entries verbatim. Triggered by "signals history [in <target>] [about <query>] [since <date>] [until <date>]" or natural equivalents like "search my signals", "find signals about X", "show me signals since June". Searches domain and observation text only. Default scope is signals.md and signals-history.md (non-sensitivity files). Sensitivity files require explicit "in <target>" naming.
---

## Trigger condition

Operator writes `signals history [in <target>] [about <query>] [since <date>] [until <date>]` or natural-language equivalents ("search my signals", "find signals about X", "show me signals from last month"). All parameters are optional; bare invocation returns all entries in scope.

## Behavior

Discover and read signals files. Apply filters. Return matching entries verbatim.

**Discovery:** read all `signals*.md` files in the zone root. For each file, check frontmatter:
- No frontmatter or `mode: descriptive` → general signals file; include in default scope
- `mode: descriptive-sensitive` AND `active: true` → active sensitivity file; excluded from default scope; include only when operator names via `in <target>`
- `mode: descriptive-sensitive` AND `active: false` → inactive; skip

**Default scope:** `signals.md` and `signals-history.md` (if it exists).

**Filtering:**
- `about <query>`: substring match, case-insensitive, against domain and observation fields only. Markers (`#creed-adjacent`) and context fields (`→ context`) are excluded from match scope. Regex special characters match literally.
- `since <date>` / `until <date>`: filter by entry date field. ISO 8601 format (`YYYY-MM-DD`). Relative expressions ("last 6 months", "last quarter") are interpreted against today's date.

**Pre-split fallback:** when `signals-history.md` doesn't exist, read from `signals.md` only; prepend header note: *"No archive exists yet; results from `signals.md` (recent) only."*

**No inference about query intent.** Return what matches. Do not suggest reformulations, alternative searches, or infer what operator might have meant.

**Falsifiability test:** returned matches must satisfy the query filters. Operator can verify by locating each returned entry in the source file and confirming it contains the query string.

## Output format

Returned entries verbatim, one per line, with source file and position noted.

```
[signals.md] YYYY-MM-DD | domain | observation [→ context] [#creed-adjacent]
```

Empty match: *"No matches found in this scope."*

## Error and edge-case handling

Named target doesn't exist or isn't active: surface error. No entries at all in scope: uniform empty message. Sensitivity files exist and excluded: surface footer.

---

## Sensitivity-handling implementation

### Discovery rule

Sensitivity-tier status determined by frontmatter (`mode: descriptive-sensitive` AND `active: true`). Skill reads frontmatter of all `signals*.md` files before reading content.

### Default scope

`signals.md` and `signals-history.md` (non-sensitivity files only). Sensitivity-tier files excluded unless explicitly named.

### Required-explicit invocation

Operator must name the sensitivity target via `in <target>` to include a sensitivity file in the search scope. No inference, no defaulting, no clarifying questions.

### Trigger-surface table

| Trigger condition | Surface |
|---|---|
| `signals history [about <query>] [since/until <date>]` — default scope | `[Read]` matching entries from `signals.md` [+ `signals-history.md` if exists] |
| `signals history in <target> [about <query>] [since/until <date>]` — named target resolves to active sensitivity file | `[Read]` matching entries from named sensitivity file |
| `signals history` with time-range filter | `[Read]` entries filtered to date range within scope |
| Named target doesn't exist or isn't active | `[Error]` "No active file found for `<target>`. Active targets: [list of active sensitivity file names]." |
| Query returns no matches in scope | `[Read]` "No matches found in this scope." |
| Sensitivity-tier file exists, active, excluded from invocation | `[Read]` footer: "Sensitivity-tier files exist and were excluded. Specify `in <target>` to include — e.g., `signals history in <tier-name> about <query>` for any active sensitivity tier." |
