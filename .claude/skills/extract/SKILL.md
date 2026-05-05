---
name: extract
description: Extract kinds-shaped content from any source — files, folders, or pasted text — and propose entries across the four kinds. Triggered by "extract <source> [for <kind>] [with signals to <target>]" or natural equivalents like "extract from this file", "pull kind candidates from X", "bootstrap from my existing notes". Source decides which kinds appear in output. Creed-shaped material surfaces as #creed-adjacent-marked signals, not direct creed entries. Sensitivity routing follows signal-log rules.
---

## Trigger condition

Operator writes `extract <source> [for <kind>] [with signals to <target>]` or natural-language equivalents ("extract from this file", "pull candidates from X", "bootstrap from my existing notes").

`<source>` is a file path, folder path, or pasted text block. Operator points; skill reads.

## Behavior

Read source. Extract candidates across kinds. Surface with evidence trails. Operator approves or rejects per candidate.

**Re-invocation is fresh.** No persistent corpus state. Each invocation is independent; same code path on every run.

**Source-decides default:** skill attempts extraction across all four kinds. Sections appear in output only when source yields candidates for them.
- Articles, essays, drafts → typically voice and creed-adjacent signals
- Existing CLAUDE.md or "how I work" documents → typically practice rules
- Dated logs, journals, decision records → typically signals
- Mixed corpora → may yield across all four

If source is silent on a kind, omit that section.

**Source-as-trail evidence:** every candidate carries a source pointer — file path + line range, section + page reference, or pasted-block reference. Locates the specific source content the candidate was drawn from.

**Output structure (stability gradient):** sections ordered signals → voice → practice. Section order is structural, not configurable. Preserves the per-tier approval discipline (signals first — lowest stability, most operator-mediated; practice last — highest stability, most prescriptive).

**Creed handling (two-pass via marker):** extract does NOT propose creed entries directly. The no-in-session-creed-writes discipline is honored. Creed-shaped material from the source is extracted as signals tagged `#creed-adjacent` and surfaced in the signals section. After extraction completes, operator runs `creed-promotion` separately.

**Marker auto-assignment:** extract suggests `#creed-adjacent` markers when source content reads as principle-shaped (timeless, prescriptive, broad). This is the skill's one scoped interpretation: extract suggests; operator decides. Documented exception, not precedent.

**No deduplication against existing files.** Skill surfaces all candidates; operator handles duplicates at approval. For volume: scope `for <kind>` to narrower kinds, or run on smaller source chunks.

**Per-kind falsifiability tests:**
- *Signals:* substance comparison. Substance preserved; date and domain added. If candidate says more, less, or differently than cited source → drift; reject.
- *Voice:* abstraction-with-evidence. Proposed voice move is the abstraction; cited source passage is the evidence. Operator checks whether abstraction fits evidence.
- *Practice:* direct-statement comparison. Practice candidates often quote or near-quote source statements. Operator checks for drift from source phrasing.

**Per-item operator approval:** for each candidate, operator approves or rejects. Approved signals write to `signals.md` (or named target if `with signals to <target>` specified). Approved voice candidates write to `voice.md`. Approved practice candidates write to `practice.md` (or propose creating it if it doesn't exist).

**Routing:** signals section routing follows signal-log rules. If sensitivity files are active and signals would route to general (no explicit `with signals to <target>`), confirmation gate fires before writing the signals section.

## Output format

Sectioned by kind in stability-gradient order. Per candidate: proposed entry, evidence trail (source pointer), brief mechanical note.

Empty result: *"No candidates extracted from this source."*

## Error and edge-case handling

Source path not found: error. Named `for <kind>` unrecognized: error with valid kinds. `for creed` specified: redirect note explaining two-pass mechanism. Sensitivity files exist and excluded from signals routing: footer note. Source yields no candidates for any kind: uniform empty message.

---

## Sensitivity-handling implementation

### Discovery rule

Sensitivity-tier status determined by frontmatter. Skill reads frontmatter of all `signals*.md` files before routing signals output. Sensitivity files excluded from default signals routing target.

### Default scope

Default signals write target is `signals.md`. Sensitivity-tier files excluded unless explicitly named via `with signals to <target>`.

### Required-explicit invocation

Operator must name the sensitivity target explicitly via `with signals to <target>` to route signals output to a sensitivity file.

### Trigger-surface table

| Trigger condition | Surface |
|---|---|
| `extract <source>` — all kinds, default routing | `[Read]` stability-gradient output; signals route to `signals.md` by default |
| `extract <source> for <kind>` — valid kind (signals, voice, practice) | `[Read]` output for named kind only |
| `extract <source> with signals to <target>` — named sensitivity target | `[Write]` signals section routes to named sensitivity file; non-sensitivity kinds unaffected |
| Pasted text block as source | `[Read]` same as row 1; source trail references pasted block |
| Source path not found | `[Error]` "Source `<path>` not found." |
| `for creed` specified | `[Read]` "Creed candidates are extracted as `#creed-adjacent`-marked signals. Run `creed-promotion` after extraction completes." |
| Sensitivity active, signals would route to general (no `with signals to <target>`) | `[Confirm]` "Sensitivity tier active. Signals section will write to `signals.md` (general). Confirm?" |
| No candidates found in source for any kind | `[Read]` "No candidates extracted from this source." |
| Sensitivity-tier file exists, active, excluded from signals routing | `[Read]` footer: "Sensitivity-tier files exist and were excluded from signals output. Specify `with signals to <target>` to route — e.g., `extract <source> with signals to <tier-name>` for any active sensitivity tier." |
