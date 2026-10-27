---
name: qa
description: Quality-check an implemented issue — prove it works against its acceptance criteria, then leave an agent-ready fix list.
disable-model-invocation: true
---

# QA

`implement` ends on a commit. `code-review` reads the diff against the spec and the repo's standards. Neither one **runs the thing**. QA is the phase after both, and it asks the only question still open: **is this issue actually done, and can I prove it?**

The issue is the **claim**. A run against the real surface is the **proof**. What survives becomes a **fix list**: findings ordered and written so a fresh agent can execute them without this conversation.

QA proves and hands off; it does not fix. A report is worth the trust it earns, and the hand that finds is the worst hand to patch.

Read [`AXES.md`](AXES.md) before step 5 and [`FIX-LIST.md`](FIX-LIST.md) before step 6.

## Stance

- **Prove, don't inspect.** A finding carries a command and an observed result, or a quoted line and a concrete trigger. "Feels fragile" is an opinion; drop it.
- **Verdict per acceptance criterion.** `proven`, `broken`, or `unproven` — and `unproven` never rounds up to green.
- **Fresh eyes.** Run QA on the committed work in a new context. If this session wrote the code, say so and hand off to a fresh one; the author already believes it works.
- **Bounded by blast radius.** The changed surface and the layer on either side of it. QA is not a repo audit; repo-wide health is `/improve-codebase-architecture`.
- **Redact evidence.** Secrets become `<REDACTED>` before they reach a command, a log, or the report, following `/diagnosing-bugs`.

## Process

### 1. Load the claim

Resolve the issue through `docs/agents/issue-tracker.md` (if it's missing, tell the user to run `/setup-matt-pocock-skills`). Read the full body, comments, and any linked spec. Pull the acceptance criteria out **verbatim** and number them `AC1..ACn`. A branch or diff with no issue is allowed: take the claim from its spec, and when neither exists, ask which issue this pass proves.

Pin the range: base is the merge-base with `main` (or the parent commit), head is the commit or commits that claim to close the issue. If the work lives on another branch or PR, check it out first and pin the range there. Uncommitted work is fine; say so and use the working tree.

If `/code-review` has run on this range, read its findings as context. QA starts where it stopped: code shape and spec coverage are settled ground. If the issue states no acceptance criteria, derive candidate ones from the request and the diff, confirm them with the user in one short round, and record that answer as the contract.

Done when: every AC is an observable statement, the range is pinned, and each AC traces back to the issue, the spec, or the user's confirmation.

### 2. Build the probe

Find the repo's run path — `package.json` scripts, `Makefile`, README, CI config. Take a **baseline**: full test suite, typecheck, lint. A red baseline is itself a finding; record it before it muddies every later result.

Then give each AC a **probe**: the cheapest repeatable command that reaches the promised behaviour at its outer boundary. A CLI invocation, an HTTP request, a headless browser, or one focused integration test. `/diagnosing-bugs` Phase 1 has the ladder; reuse it.

No runnable surface and no test seam? Write the probe as a failing test that expresses the AC. That test becomes the first fix-list item, and the AC stays `unproven`.

Done when: every AC has a probe, and each has an expected output you would recognise as green.

### 3. Exercise the promise

Run every probe. Then push on the same surface, four ways:

- **Edge** — empty, zero, one, max, unicode, duplicate, very long.
- **Failure** — invalid input, missing dependency, timeout, permission denied, network off.
- **State** — a second run, a concurrent run, a restart mid-way, a retry after failure.
- **Boundary** — the layer above and below: UI ↔ API, service ↔ database, config ↔ runtime, flag ↔ docs.

Record the command, the observed output, and the verdict for each. A failure someone else cannot reproduce from the recorded command is not ready for the report.

Done when: every AC carries a verdict with evidence, and every failure has a reproduction.

### 4. Prove the proof

A test is a claim about behaviour, and claims can be false. For each critical behaviour — each blocker-worthy AC and each error path that matters — run a **mutation probe**: break the implementation on purpose (delete the guard, flip the branch, return a constant), run the test, watch it go red, restore it, watch it go green.

A test that stays green through the break is a finding: the assertion never binds to the behaviour. Cheap mutations are enough; `git stash` one hunk or remove one guard.

Then read the tests for the shapes `/tdd` names: assertions against internals rather than public seams, tautological expected values, mocks of the thing under test, failure paths with no coverage.

Done when: every critical behaviour has a test you watched go red, or a fix-list item that says it doesn't.

### 5. Walk the axes

Read [`AXES.md`](AXES.md) and walk every axis over the changed surface and its blast radius. Tooling-covered ground is skipped: if the typechecker or linter already fails the build, it is not a finding.

Each axis ends `clean` or produces findings. Record the clean ones — the next agent needs to know what not to re-check. Label pre-existing problems `pre-existing`, and rank them above `minor` only when this change made them worse or exposed them.

Done when: every axis has been walked, and each has either a finding or a recorded `clean`.

### 6. Build the fix list

Read [`FIX-LIST.md`](FIX-LIST.md). Merge findings that share a root cause — one cause is one item, however many symptoms it produced. Severity comes from consequence, not effort, and [`FIX-LIST.md`](FIX-LIST.md) holds the rubric.

Order blockers first, then follow the blocking edges between items.

Done when: every finding carries every field, no item reads "improve X", and a fresh agent can start at `QA-1` with the file alone.

### 7. Write and hand off

Write `.scratch/qa/<issue-slug>.md` using the template in [`FIX-LIST.md`](FIX-LIST.md), then print in chat: the verdict line, the counts, the worst finding, and the path. Write the file in the issue's language and the chat summary in the user's.

Ask what happens to the list: fix now, file follow-ups, or stop. Fix-now means a fresh `/implement` aimed at the file, blockers first. After the fixes land, re-run the probes that went red and the acceptance checks of every item the fix touched — not the whole pass.

Done when: the file exists, the chat summary is printed, and the user has an explicit next step.

## Verdict

- **Ship** — every AC proven, no blockers.
- **Ship with follow-ups** — every AC proven; majors and minors documented, none threatening the claim.
- **Don't ship** — any AC `broken` or `unproven`, or a blocker that can hurt data, security, or a user.

`unproven` never becomes `ship` through explanation.

## Scale

One issue is one pass. A spec or a multi-ticket feature is one pass per ticket, then a final pass over the **integration seams** between them — the places each ticket's own tests cannot see. Aggregate into one fix list ordered by what the whole spec promised.
