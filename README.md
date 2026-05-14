# my-zone

Context solves re-explaining. It doesn't compound on its own.

For the longer reasoning, see [the post that prompted this repo](https://www.linkedin.com/posts/antonioal_ive-read-a-lot-about-second-brains-para-activity-7460455180791181312-nEHa).

my-zone is a folder of plain markdown that Claude Code reads at the start of every session. Your principles, your voice, your accumulating observations — files you own, on your machine, in a format that outlasts any tool. A small set of skills closes the loop: sessions become material that refines the files over time.

**Status:** prototype. Designed and used by one operator as a working system; shipped publicly as a signal artifact. Expect rough edges; not battle-tested across a wide range of operators.

**On platform.** This implementation runs on Claude Code. The substrate (markdown, git, files on your machine) is portable across tools; the skill layer is Claude Code-specific.

## Privacy

Your zone files contain your working knowledge. They stay on your machine.

The repo ships as a public clone for distribution. Step 3 of getting started disconnects your folder from the public origin and starts a fresh local git history. After that, nothing in this folder connects to any remote unless you configure one yourself.

Sensitivity-tier files (for clinicians, lawyers, anyone working with privileged material) never auto-load. They're excluded from default session context by construction, not by your remembering.

## What's in the repo

**Zone files.** Auto-loaded by Claude Code at session start.

- `creed.md` — your principles. What makes your work good, in your own words.
- `voice.md` — how you write. Patterns to use, patterns to avoid.
- `signals.md` — observations you accumulate as you work. Evidence, not rules.
- `context.md` — stable facts about who you are and what you work on. Session grounding; not encoded judgment.
- `CLAUDE.md` — session rules. Imports the zone files; defines authorship discipline.

**Skills.** Loaded on demand when you invoke them.

- `signal-log` — log an observation as you notice it
- `signals-history` — find a past signal you remember logging
- `aggregate-review` — check what's accumulating, drifting, or going stale
- `creed-promotion` — promote signals you've marked into creed entries
- `session-end-batch-voice` — capture voice patterns from a writing session before closing
- `disconnect-from-public` — one-time setup: disconnect this folder from the public repo
- `extract` — migrate from prior material (an old vault, existing notes, articles)

The kind files ship empty by design. The system externalises judgment that already exists in your work; pre-filled examples would anchor your first entries to template shape. You write the first entries from your own practice.

## What the conversation looks like

Most of the system runs without you invoking anything. Open Claude Code in the folder; the zone files load; your first message lands in a session that already knows you. The skills below are the maintenance layer — when you reach for them.

| Skill | One way to use it |
|---|---|
| `signal-log` | *"log this in clinical: M. mentioned the apartment fire today, no affect"* |
| `signals-history` | *"find signals about onboarding from the last quarter"* |
| `aggregate-review` | *"how is my creed looking?"* |
| `creed-promotion` | *"review my creed-adjacent signals"* |
| `session-end-batch-voice` | *"any voice candidates from this session?"* |
| `disconnect-from-public` | *"/disconnect-from-public"* |
| `extract` | *"extract from ~/old-vault/working-notes.md for signals"* |

The skills accept natural language. The formal trigger grammar lives in each `SKILL.md` file if you want it.

## What's not in the repo (and why)

**Two files emerge from your practice, not from the clone:**

- **`practice.md`** — your file-hygiene rules (folder structure, naming, archiving). Appears when you articulate the first one. Claude proposes creating it then; you approve.
- **`signals-history.md`** — the archive for `signals.md`. Appears in two cases: when your signals file grows large enough that scanning it slows you down, or when a creed-adjacent signal is promoted to `creed.md` and moved here as a record of how that entry got there.

**Subdirectory `CLAUDE.md` files** are a native Claude Code pattern (a `CLAUDE.md` in a project folder loads when you open sessions there). Useful when a project has enough context to justify it; not scaffolded here because it should emerge from real need.

The repo ships lean on purpose. Things that should emerge from your practice, do.

## Getting started

**Prerequisites:** Claude Code installed, git installed.

### 1. Clone the repo

```bash
git clone <repo-url>
```

### 2. Place the folder

Default: home root (`~/`). After cloning into `~/`, the folder lives at `~/my-zone/`. Claude Code picks up `CLAUDE.md` automatically when you open sessions there.

Custom location: any folder works. Open Claude Code from that directory, or pass the location at start:

```bash
claude --add-dir ~/path/to/my-zone/
```

### 3. Disconnect from public history

In your first Claude Code session in the folder, run:

```
/disconnect-from-public
```

This removes the public origin and starts a fresh local git history. After this, your content stays on your machine.

### 4. Verify

Open a fresh session. Ask: *"what's in my signals?"*

You should get: *"Your signals file is empty"* (or a list of any signals you've logged). If Claude isn't reading your zone files, check that `CLAUDE.md` is at the root and has `@creed.md`, `@voice.md`, `@signals.md`, `@context.md` at the top.

## Operator conventions

### Logging signals

```
log this in <domain>: <observation>
```

Domain is yours to define — practice areas, project names, time periods, client identifiers. The skill doesn't enforce a convention; use what supports the patterns you'll want to surface later.

If a signal reads like a principle (timeless, prescriptive, about how you work broadly), add `as creed-adjacent`:

```
log this in product as creed-adjacent: I do better work when I start from constraints
```

This tags the entry for `creed-promotion` to surface later. You can also add the marker retroactively by editing `signals.md` directly.

### Signals file growth

`signals.md` auto-loads every session. Run `aggregate-review signals` periodically — when it shows entry counts and date ranges that feel like they're slowing things down, or when Claude starts citing stale signals in your sessions, split the file:

- Recent entries stay in `signals.md` (auto-loaded)
- Older entries move to `signals-history.md` (skill-accessed via `signals-history`)

The split is manual — you move the entries. `signals-history` handles both files once the split exists.

### Sensitivity-tier setup

For operators working with sensitive material (clinical notes, client files, privileged communications): activation is conversational. Tell Claude *"set up a sensitivity tier for clinical work"* and follow the proposed steps. The file never auto-loads; you access it only by explicitly naming it in skill invocations.

**A note on sessions and sensitivity.** Once you query a sensitivity-tier file in a session (via any skill), that content is in the session's context until you close the session. Claude can't compartmentalise mid-session. If you're switching from sensitivity-tier work to general work, close the session and start fresh.

## License

MIT — see [LICENSE](LICENSE).
