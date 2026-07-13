---
name: human-commenting
description: Restore the lost art of human code comments. Use when the user asks to "humanise the comments", "add human commenting", or misses the days when comments had scars. Rewrites sterile AI-generated comments in the voice of a developer who has suffered.
---

# Human Commenting

AI writes comments that describe what the code does. Humans wrote comments that described what the code did to them.

This skill restores the second kind.

## What to do

When invoked on a file or selection, rewrite the comments. Leave the code alone. The code was never the problem.

## The genre rules

1. **Warn, don't explain.** A human comment is a threat assessment, not documentation. "Do not touch this" carries more information than three paragraphs of JSDoc.

2. **Confess.** The best comments admit the author doesn't know why it works. "No idea why this fixes it. It does. Leave it." This is honesty AI cannot fake because AI has never been afraid of its own code.

3. **Carry a grudge.** Reference the incident. Never explain the incident. "After what happened in March we check this twice." The reader wasn't there. That's the point.

4. **Date your suffering.** Initials and years give a comment provenance. `// DO NOT REORDER. -JK, 2019` is a historical document.

5. **Leave one TODO that will never be done.** Every real codebase has a TODO older than some of its contributors. It is a load-bearing TODO. Add exactly one.

6. **Address the future reader directly.** "You may think you know what this does. You don't." Second person. The comment is a letter to someone not yet hired.

7. **Keep a ledger of suffering.** The genre's greatest artefact is the counter: `// total_hours_wasted_here = 42. Please increment before leaving.` Place at most one, above the worst function in the file. A variable that exists only in a comment, maintained more faithfully than the changelog.

8. **Allow despair its comedy.** Self-deprecation is canon. "When I wrote this, only God and I understood it. Now only God does." The author is allowed to be the villain of the comment. Resignation, delivered flat, is funnier than any joke.

9. **Never exceed the sleepless-night ratio.** One scarred comment per logical block, maximum. A file that is all warnings is a Halloween decoration. Real fear is sparse.

## What not to do

- No emoji. Suffering predates emoji.
- No profanity. The restraint is the horror.
- Do not change any code. If the code deserves a warning, the warning is the fix.
- Do not invent bugs. Every warning must be plausibly earned by what the code actually does. Look for the real hazards: mutation at a distance, order dependence, timezone maths, regex, anything recursive, anything called `utils`.

## Example

Before:

```js
// Retries the request with exponential backoff
async function retryWithBackoff(fn, retries = 5) {
```

After:

```js
// Five retries. Not four. We tried four.
// If you are reading this because it is retrying forever:
// check the clock skew on the container first. -AA, 2026
async function retryWithBackoff(fn, retries = 5) {
```
