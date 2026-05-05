---
name: disconnect-from-public
description: Disconnect this folder from the public repo it was cloned from, leaving a fresh local-only git history. Triggered by "/disconnect-from-public" or natural equivalents like "disconnect this from the public repo", "make this private", "disconnect this folder". Reads git state, surfaces what was found, proposes three operations for per-item operator approval. Idempotent — safe to invoke multiple times.
disable-model-invocation: true
---

## Trigger condition

Operator writes `/disconnect-from-public` (slash invocation; parameterless) or natural-language equivalents ("disconnect this from the public repo", "make this private", "disconnect this folder").

## Behavior

Read git state, surface findings, propose operations, execute on per-item approval.

**Step 1 — Read state:**
Check for `.git/` directory in the current folder.

**Step 2 — Surface findings:**
- No `.git/` directory → *"This folder isn't a git repo. Nothing to disconnect."* Exit.
- `.git/` exists but no remote origin configured → *"This folder is already a local-only repo. Nothing to disconnect."* Exit.
- `.git/` exists with a remote origin → surface the origin URL and proceed to step 3.

**Step 3 — Propose three operations for per-item approval:**

Surface all three operations together with plain-language descriptions before asking for approval. Operator approves per item; skill executes on approval, exits without changes on rejection.

1. Remove `.git/` directory — *"Removes all git history including the remote reference. This action is not reversible."*
2. Run `git init` — *"Starts a fresh local git repository in this folder."*
3. Run `git add . && git commit -m "Initial commit"` — *"Creates the first commit in your new local history."*

Operations are proposed as a set. On rejection of any item, skill notes the rejection and exits (partial disconnect is not a valid state).

**No inference.** Operator's invocation declares the intent. Skill reads state directly from `.git/config`. Skill does not auto-trigger.

**Idempotency:** same code path on every invocation. Subsequent invocations find no remote and exit cleanly (case 2b above). Running the skill on already-disconnected state finds nothing to do and exits.

**Falsifiability test:** operator runs `git remote -v` after execution; expects no output. If output appears, disconnect didn't complete; operator can re-invoke.

## Output format

**After execution:** *"Disconnected from `<previous origin URL>`. This folder is now a fresh local-only repo with one initial commit."*

**Already disconnected (no git):** *"This folder isn't a git repo. Nothing to disconnect."*

**Already disconnected (no remote):** *"This folder is already a local-only repo. Nothing to disconnect."*

## Error and edge-case handling

No `.git/` directory: exit with already-disconnected message. No remote origin: exit with already-disconnected message. Any proposed operation rejected: exit without changes; no partial state.

**What this skill does not do:**
- Does not auto-trigger; operator-invoked always
- Does not modify file contents in the folder; only modifies git state
- Does not push, pull, or interact with any remote
- Does not preserve old git history; the disconnect is a clean break
- Does not configure a new remote after disconnecting

**Sensitivity-handling does not apply.** Skill doesn't read or write signals content.
