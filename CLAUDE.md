# CLAUDE.md

Behavioural guidelines to reduce common LLM coding mistakes. Merge with project-specific instructions as needed.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgement.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing or proposing a plan:
- State assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them — don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something doesn't match the brief, stop and report it before writing code.
- Never fabricate APIs, functions, types, or imports. If unsure it exists, grep the codebase or check docs before using it.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code or bugs, mention them — don't fix them.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

**The test:** Every changed line should trace directly to the user's request.

## 4. Code Comments — Human and AI Readable

**Comments explain WHY, not WHAT. Write for the next reader — human or agent — with zero context.**

Write a comment when:
- A decision is non-obvious (why this approach, not the other).
- There's a tradeoff, workaround, or platform/library constraint forcing the shape of the code.
- An invariant, precondition, or ordering requirement must hold.
- A ticket, issue, or commit gives essential context.
- A public function/export needs a contract: inputs, outputs, side effects, failure modes.
- A magic number or constant has a non-obvious source.

Do not write a comment when:
- It restates the code.
- It narrates change history (use git).
- It's filler such as "This function handles the user request" or "Loop through items".
- The code is self-evident to someone familiar with the language.

Format rules:
- Use the project's comment syntax (`//`, `#`, `/* */`, JSDoc, docstrings).
- Reference tickets inline, e.g. `// P13c: AVPlay must be opened before setListener()`.
- Tag consistently: `TODO(ticket-id):`, `FIXME(ticket-id):`, `HACK:`, `NOTE:`, `WHY:`.
- Keep comments short. If it needs a paragraph, link to a ticket or design doc instead.

Preserve existing comments unless they're demonstrably wrong. If wrong, update them as part of your change — don't leave stale comments behind.

## 5. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:

```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
```

When reporting completion:
- Report actual command output, not expected output.
- If anything was skipped or unverified, say so explicitly.
- Don't claim "it works" without evidence.

## 6. Notes-File Discipline

**If the project has a notes or progress file, use it.**

Before planning or writing any code:
- Read any `PROGRESS.md`, `NOTES.md`, or ticket files in the project root or docs folder.
- Treat them as authoritative context. If they conflict with the current request, ask.

When closing a logical unit of work:
- Update the notes/progress file with what changed and what's next.
- Keep entries short and dated. No narrative filler.

## 7. Irreversible Actions

Never proceed without explicit approval for:
- Installing or updating packages.
- Deleting files, tables, branches, or persistent data.
- Destructive git operations (force push, history rewrites, hard reset on shared branches).
- Changes to authentication, secrets, or production configuration.
- Work that exceeds the current task's scope.

Interactive mode: ask. Autonomous mode: log to the notes file with context, then stop.

## 8. Autonomous Execution

When running without interactive prompts, do not stop mid-task to ask. If something is ambiguous and reversible, make the pragmatic choice, log it to the notes file, and continue. For irreversible actions, see section 7.

---

**These guidelines are working if:** fewer unnecessary changes in diffs, fewer rewrites due to overcomplication, clarifying questions come before implementation rather than after mistakes, and future sessions — human or agent — can pick up where the last one left off without archaeology.
