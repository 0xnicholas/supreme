# The fix list

The fix list is the artifact that outlives the session. Write it for a fresh agent with no access to this conversation: that agent starts at `QA-1` and finishes without asking a question.

## Rules for every item

- **Behaviour, not location.** The title names what changes, so it survives a rename.
- **Evidence is a snapshot.** A command and its output, or a quote plus the trigger that reaches it. Paths and line numbers are welcome here, because evidence records a moment. A command runs from the repo root against the linked fixtures, with no hidden state. Long logs, screenshots, and payloads live under `.scratch/qa/<number>-<slug>/` and are linked from the item.
- **Current → desired.** Both stated, including the edge and error cases the fix must cover.
- **Touch is durable.** Name the interface, type, function, or config shape. No file paths, no line numbers — the agent explores fresh.
- **Acceptance is observable.** A check that passes or fails on its own, independent of how the fix is written.
- **Test names the seam.** Say where the test goes and what it asserts red-first. For a binding gap, name the mutation that must turn the existing test red.
- **One cause, one item.** Symptoms of the same root cause merge.

## Severity

- **Blocker** — a promised behaviour is broken or unproven; data is wrong or lost; a security hole; a failure that looks like success. Don't ship past it.
- **Major** — a common path degraded, a critical behaviour with no binding test, a rollout hazard (migration, flag, config) that will bite.
- **Minor** — an edge case, a message, an observability or docs gap.
- **Nit** — a typo in user-facing copy. Everything else merely stylistic stays with `code-review`.

## Good and bad

Bad:

> **QA-3 — Improve error handling in the import flow.**

Good:

> **QA-3 — Empty CSV import returns a 500 HTML page** · blocker
>
> - **Evidence:** `curl -s -o /dev/null -w '%{http_code}' -X POST localhost:3000/api/import -F file=@empty.csv` → `500`; the body is the Express error page.
> - **Current:** a header-only CSV reaches the row parser, which throws on `rows[0]`, and the exception escapes the handler.
> - **Desired:** a header-only CSV returns `200` with `{"imported": 0}`; a malformed row returns `422` with `{"errors": [{"row": n, "field": "..."}]}`. Nothing persists on failure.
> - **Touch:** the import handler's request → result contract; the parser's empty-input behaviour.
> - **Acceptance:** the two curls above return `200 {"imported":0}` and `422`; the table is unchanged after the second.
> - **Test:** at the HTTP seam, assert both responses and the post-failure row count.
> - **Blocked by:** nothing.

Bad:

> **QA-5 — Tests are insufficient.**

Good:

> **QA-5 — Deleting the negative-total guard leaves the suite green** · major
>
> - **Evidence:** removed the `total < 0` throw in `refund()`, ran `pnpm test refund` → 14 passed; restored it → 14 passed.
> - **Current:** no test binds the guard.
> - **Desired:** `refund()` rejects a negative total before touching the ledger.
> - **Touch:** `refund()`'s error contract.
> - **Acceptance:** with the guard removed, the suite fails; with it restored, it passes.
> - **Test:** at the `refund` seam, assert the rejection and that no ledger entry is written.
> - **Blocked by:** nothing.

## The file

Write `.scratch/qa/<number>-<slug>.md`:

````markdown
# QA — <issue title>

**Issue:** <link | no issue — spec/branch + confirmed ACs> · **Range:** `<base>..<head | base..worktree>` · **Date:** <YYYY-MM-DD>
**Session:** <fresh | same-context as author — findings may share author bias>
**Verdict:** <ship | ship with follow-ups | don't ship> — <one line>
**Counts:** <n> blocker · <n> major · <n> minor

> **To the agent implementing this list:** work top to bottom — the order is topological, so a major may sit above a blocker that depends on it. For each item, write the failing test, make it pass, run the item's acceptance check, and keep the rest of the suite green. Work only the listed items; append discovered work here instead of fixing it in passing. When the list is complete, re-run the acceptance scorecard, report every row, and append the `## Re-run` block described below.

## Why this verdict

<The worst finding first, then what carried the verdict. A reader who stops here knows whether to release.>

## Acceptance scorecard

| # | What the issue promised | Verdict | Evidence |
| --- | --- | --- | --- |
| AC1 | <observable statement> | proven | `<command>` → `<observed>` |
| AC2 | <observable statement> | broken — QA-1 | `<command>` → `<observed>` |
| AC3 | <observable statement> | unproven — no reachable seam; QA-4 builds the probe | `<why>` |

## Fix list

### QA-1 — <behaviour> · <severity>

- **Evidence:**
- **Current:**
- **Desired:**
- **Touch:**
- **Acceptance:**
- **Test:**
- **Blocked by:**

## Verified clean

- <axis or behaviour> — <what was probed, why it holds>

## Out of scope

- <pre-existing issues, adjacent work, suspicions not reproduced>
````

## Re-runs

After a fix pass, append — never rewrite the first pass:

```markdown
## Re-run — <YYYY-MM-DD>

**Head:** `<head>` · **Verdict:** <updated> · **Flipped:** <AC2 → proven via QA-1>

<What changed since the first pass, and what did not.>
```
