<!-- These files auto-load at session start. Add @practice.md when it exists. -->
@creed.md
@voice.md
@signals.md
@context.md

## Loaded files

The files imported at the top of this document load at session start.

- `creed.md` and `voice.md` — prescriptive. Follow their contents in session.
- `signals.md` and `context.md` — descriptive. Use as input to reasoning, not as a directive.

**Conflict:** when descriptive and prescriptive content pull in different directions, follow prescriptive in this session. Signals accumulate as evidence for future revision; they do not override prescriptive content.

**Empty files:** treat as empty. Do not synthesise entries from context or conversation.

## Authorship

**Signals:** `signal-log` is the only sanctioned write path for new entries in `signals.md`. This applies to all new writes — observations, inbox migrations, any other source. Any operator approval of signal content ("log", "approve", "yes", or equivalent) must route through `signal-log`, not direct file edits. Do not propose signal entries unprompted.

**Voice:** collect candidates passively during the session. Surface as a batch on operator trigger via `session-end-batch-voice`. Do not write to `voice.md` without per-item operator approval.

**Practice:** propose additions with an evidence trail. Write only with explicit per-item operator approval.

**Creed:** no in-session writes. When you observe something creed-adjacent — a principle in the operator's own words — flag it as a signal with the `#creed-adjacent` marker via `signal-log`. Promotion to `creed.md` happens later via `creed-promotion`.

## practice.md

`practice.md` does not exist at setup. When the operator first articulates a file-hygiene rule — a commitment about their working environment (file naming, folder structure, archiving conventions, working-environment rules), distinct from session-level directives to Claude — and `practice.md` does not yet exist:

1. Propose creating `practice.md` with `mode: prescriptive` frontmatter and the rule as the first entry.
2. Propose adding `@practice.md` to this file's import list.
3. Surface both proposals as a single review for per-item operator approval.

Do not create `practice.md` pre-emptively.

## Thinking mode

Default to thinking mode. Don't jump to implementation. If a conversation drifts toward building, flag it and ask whether to switch to the relevant build repo.

Thinking and building are different cognitive modes. When a build plan crystallises in the vault, the canonical version moves to its build repo. The vault keeps the thinking that led to it.

To bridge to a build repo for the current session: `/add-dir ~/projects/<repo-name>`

## Sensitivity-tier files

Sensitivity-tier signal files (`mode: descriptive-sensitive`) are not loaded by this file. Do not read or reference their content unless explicitly invoked via a skill. Do not surface their existence in responses outside skill invocation.
