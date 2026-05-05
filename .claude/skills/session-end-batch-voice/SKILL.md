---
name: session-end-batch-voice
description: Surface voice-move candidates noticed during the current session as a batch with evidence trails. Triggered by "voice candidates" or natural equivalents like "surface voice candidates", "what voice moves did I use", "any voice candidates from this session". Retrospective scan at invocation time — no background tracking. Single-session scope only. Operator approves or rejects per candidate; approved candidates write to voice.md.
---

## Trigger condition

Operator writes `voice candidates` or natural-language equivalents ("surface voice candidates", "what voice moves came up", "any voice candidates from this session"). Invoke at any point in the session; typically invoked near session end.

## Behavior

At invocation, retrospectively scan the current session's writing-context content. Identify voice-move-shaped patterns. Surface as a batch with evidence trails. Operator approves or rejects per candidate.

**Retrospective scan, not background tracking.** The scan happens at invocation; there is no accumulation during the session.

**Single-session scope.** This session's content only. No carry-over from prior sessions; no persistent state between invocations.

**What a voice-move candidate looks like:** a pattern in the operator's writing (within this session) that coheres as a reusable move — a structural shape, a sentence pattern, a rhetorical choice used more than once or used distinctively. Not every piece of writing yields candidates.

**Output per candidate:**
- Proposed voice move (abstraction — the pattern, not an instance)
- Evidence trail (verbatim session phrasing — operator's own words)
- Brief mechanical note describing the pattern

**Abstraction-vs-evidence boundary:** voice moves are abstractions of patterns. Claude's proposal is the abstraction. The evidence trail is verbatim session content. These are distinct: the abstraction characterises; the evidence grounds it. If pattern-recognition language appears in the evidence trail rather than the operator's own session words, that's a violation — operator rejects.

**Pattern-knowledge-not-evidence:** Claude's prior knowledge of voice-move patterns generates candidates. That prior knowledge is not evidence and must not appear in the evidence trail.

**Falsifiability test:** session-content verification. Operator can verify candidates against session content. Session is the corpus; candidates are claims about the corpus. Operator reads both and judges whether the abstraction fits the cited instances.

**Re-invocation:** re-scans the session. Previously rejected candidates may reappear if the re-scan identifies the same patterns. No persistent rejection state.

**Per-item operator approval:** for each candidate, operator approves or rejects. On approval: write to voice.md (proposed move + evidence trail). On rejection: skip.

**Mixed-scope note:** if the session includes content from sensitivity-tier invocations, the retrospective scan may surface evidence trails that reference text near sensitive content. Close the session before mixing scopes.

## Output format

Batch with candidates one by one. Each candidate: proposed move, evidence trail, brief note. Approval gate after each.

Empty result: *"No voice-move candidates from this session."* (Covers multiple causes uniformly — brief session, no writing in session, writing without identifiable patterns. Skill does not distinguish causes.)

## Error and edge-case handling

Session has no writing content: uniform empty message. Re-invocation after full rejection: re-scans; may or may not surface same candidates. voice.md doesn't exist yet: propose creating it before writing the first approved entry.

**Sensitivity-handling does not apply.** Voice-only by design; no sensitivity-tier voice files exist in the design. Skill is voice-write-only and does not touch signals files.
