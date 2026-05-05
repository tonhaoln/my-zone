# Skills

This directory (`.claude/skills/`) contains the method's seed skills. Each subfolder is one skill — `.claude/skills/<name>/SKILL.md` is the path Claude Code reads for auto-discovery.

## When to build a skill

Build a skill when you have a repeating operation on your kind files that you want Claude to handle consistently. If you're doing the same thing manually across sessions, it's probably a skill.

## Decision tree — which seed skill to copy from

Match your skill's primary purpose to the reference shape:

| If your skill... | Copy from |
|---|---|
| Writes entries to a signals file | `signal-log` |
| Reads and returns signals entries verbatim | `signals-history` |
| Reads kind files and surfaces observations (not verdicts) | `aggregate-review` |
| Promotes or transforms content from one kind to another | `creed-promotion` |
| Surfaces session-bounded candidates in a batch | `session-end-batch-voice` |
| Does a one-time configuration change that's safe to re-invoke | `disconnect-from-public` |
| Extracts candidates from an external source | `extract` |

**Hybrid skills:** if your skill has more than one purpose, use the primary purpose's seed skill as the base. Adapt sections from other seed skills as needed.

**Non-method work skills** (e.g., skills for your own domain that don't touch kind files): no seed skill reference for these. Follow the behavioral-body floor (below) directly.

## Behavioral-body floor — every skill

Every SKILL.md covers these four points in whatever structure fits the skill:

1. **Trigger condition** — when does the skill apply? What does the operator say to invoke it?
2. **Behavior** — what does Claude do?
3. **Output format** — what does the operator see?
4. **Error and edge-case handling** — what happens when conditions aren't met?

These are content requirements, not section headers. Write whatever structure fits.

## Sensitivity-handling — signals-touching skills

Any skill that reads or writes signals content (your own or a seed skill's) must include a sensitivity-handling subsection. Four elements required:

1. **Discovery rule** — how does the skill detect sensitivity-tier status? (Answer: via frontmatter — `mode: descriptive-sensitive` AND `active: true`)
2. **Default scope** — which files are excluded by default?
3. **Required-explicit invocation** — how does the operator name a sensitivity file?
4. **Trigger-surface table** — two columns (Trigger condition / Surface); rows cover behaviorally distinct surfaces; Surface column uses `[Write]`, `[Read]`, `[Error]`, `[Confirm]` prefixes

The subsection is structural, not optional. Signals-touching skills without it are incomplete.

## Trigger-surface table format

```
| Trigger condition | Surface |
|---|---|
| <parameterized trigger description> | [Write/Read/Error/Confirm] <surface description> |
```

- Rows are **behaviorally distinct surfaces**, parameterized over inputs — not enumerated per case
- `[Confirm]` rows imply per-item operator approval downstream; don't enumerate the approval variants as separate rows
- Sensitivity-existence footer is always a row (unconditional when active sensitivity files are excluded)

## No skeleton template

Don't copy the SKILL.md file itself. Copy the *patterns* — read the relevant seed skill's structure and write your skill's body from scratch using the same patterns. Skeleton copies drift.

## Description field

The description field is how Claude Code discovers your skill. Write it to describe the skill's purpose and give illustrative trigger phrases. Aim for semantic richness, not exhaustive enumeration. Claude recognises intent from description; operators don't need to type exact phrases.

Character limit: 1024.
