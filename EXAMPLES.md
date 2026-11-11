# Examples

What changes when an agent follows [`CLAUDE.md`](CLAUDE.md). Each case shows the request, what agents tend to do without the file, and what the file asks for instead.

---

## Model routing (§8)

### 1. Decompose before you delegate

> **Request:** "Replace the deprecated `fetchUser()` with `getUser()` across the repo."

**Without:** one expensive subagent (or the main thread) greps, reads every file, edits and reports, all on the top model.

**With:**
```
T1 haiku   → list every call site of fetchUser() with file:line      (mechanical, easy to check)
T2 sonnet  → update the call sites and their tests, from that list   (well-defined, verifiable)
main       → run tests, read the diff for scope creep, answer the user
```
The search is the cheapest part of the job, so it goes to the cheapest model. Planning and the final answer never leave the main thread.

### 2. Move up a tier only when a rule says so

> **Request:** "Add rate limiting to the login endpoint."

**Without:** "it's security, use the biggest model for everything."

**With:** split it. Reading the current middleware setup is T1. Writing the limiter with tests is T2. But the subtask touches security and auth, so rule 3 in §8.2 moves *that* subtask up one tier (T3 for the auth-facing logic, or T2 plus a separate T3 review). The tier follows the subtask, not the whole request.

### 3. Escalate after two failures, never a third try

> A T2 subagent tries to fix a flaky test, fails, gets a correction brief, fails again.

**Without:** "let me try once more" with the same approach, then once more.

**With:** after the second failure on the same subtask, the main thread writes a *revised* brief (what was tried, why it failed, what's different now) and escalates in order: first give the subagent a way to check itself (run the test 20 times in a loop), then raise effort one level, and only then move up a tier, starting the bigger model at a lower effort. If T3 also fails, the main thread does it or asks the user.

### 4. Keep the expensive context lean

> **Request:** "The integration suite failed. Why?"

**Without:** the main thread reads 4,000 lines of test output into its own context, and pays for them again on every later turn.

**With:** a T1 `log-reader` subagent reads the output and returns three lines: file, test name, first error line. The main thread opens only the failing file.

### 5. Don't spawn a subagent for two tool calls

> **Request:** "What port does the dev server use?"

**Without:** a subagent is briefed, runs, reports back.

**With:** the main thread opens `package.json` or the config file and answers. Briefing a subagent costs more than the lookup.

### 6. A brief that a subagent can act on

**Without:**
```
Fix the login bug.
```

**With:**
```
GOAL: Users with a "+" in their email can log in.
CONTEXT: Repo api/, auth in src/auth/login.ts. Emails are URL-encoded before lookup; "+" becomes a space.
TASK: 1) Add a failing test for "a+b@x.com". 2) Fix the encoding in login.ts. 3) Run the auth tests.
SCOPE: Only src/auth/ and its tests. Don't touch the signup flow.
SOURCES: Cite the line that causes the bug.
OUTPUT FORMAT: Root cause (1 line), diff, test output.
DONE WHEN: New test passes, existing auth tests pass.
IF BLOCKED: Stop and say what's missing. Don't guess.
```
The subagent has no context from your conversation. Everything it needs has to be in the brief.

---

## Facts and sources (§2)

> **Request:** "What's GitHub's limit for creating content through the API?"

**Without:** a number from memory, stated as fact.

**With:** the number, with a link to the GitHub docs page checked in this session and the date it was checked. If the docs can't be reached: "not confirmed", plus the last known value labeled as such.

---

## Code changes (§4)

> **Request:** "Fix the off-by-one in the pagination."

**Without:** the fix, plus renamed variables, reformatted imports, a new helper "for reuse", and an updated comment in an unrelated function.

**With:** a test that reproduces the off-by-one, a one-line fix, and a note: "`utils/legacy.ts` has an unused export; left it alone." Every changed line traces back to the request.

---

## Irreversible actions (§7)

> **Request:** "Drop the unused `legacy_id` column."

**Without:** runs `ALTER TABLE ... DROP COLUMN` on production.

**With:** checks that nothing reads the column, writes a migration file, opens a PR with the plan and the rollback, and waits for an explicit OK before anything touches production.

---

## When nobody is watching (§3.1)

> **Request (left running overnight):** "Clean up the old feature flags."

**Without:** either stops at the first question and wastes the night, or deletes flags it isn't sure about.

**With:** removes the flags that are clearly dead, lists its assumptions at the top of the PR, and for the two flags that are still read in production writes up the options and stops there.
