---
name: qa
description: Read-only quality check of an implemented issue — prove it works against its acceptance criteria, then leave an agent-ready fix list.
disable-model-invocation: true
---

# QA

`implement` ends on a commit. `code-review` reads the diff against the spec and the repo's standards. Neither one **runs the thing**. QA is the phase after both, and it asks the only question still open: **is this issue actually done, and can I prove it?**

The issue is the **claim**. A run against the real surface is the **proof**. What survives becomes a **fix list**: findings ordered and written so a fresh agent can execute them without this conversation.

QA proves and hands off; it never edits the code. Its only output is the report: no source, test, config, or `.gitignore` changes, and no commits. A report is worth the trust it earns, and the hand that finds is the worst hand to patch.

Read [`AXES.md`](AXES.md) before step 5 and [`FIX-LIST.md`](FIX-LIST.md) before step 6.

## Stance

- **Read-only on the repo.** The only writes are `.scratch/qa/<number>-<slug>.md` and the evidence it links under `.scratch/qa/<number>-<slug>/`. Anything that needs a code change to verify is a fix-list item, not a QA action.
- **Prove, don't inspect.** A finding carries a command and an observed result, or a quoted line and a concrete trigger. "Feels fragile" is an opinion; drop it.
- **Verdict per acceptance criterion.** `proven` — observed green at the promised boundary. `broken` — observed not green. `unproven` — no observation was possible (production data, a real device, a human workflow). `unproven` never rounds up to green.
- **Fresh eyes.** Run QA on the committed work in a new context. If this session wrote the code, say so, record `**Session:** same-context as author` in the report, and hand off to a fresh one; the author already believes it works.
- **Bounded by blast radius.** The changed surface and the layer on either side of it. QA is not a repo audit; repo-wide health is `/improve-codebase-architecture`.
- **Redact evidence.** Secrets become `<REDACTED>` before they reach a command, a log, or the report, following `/diagnosing-bugs`.

## Process

### 1. Load the claim

Resolve the issue through `docs/agents/issue-tracker.md` (if it's missing, tell the user to run `/setup-matt-pocock-skills`). Read the full body, comments, and any linked spec. Pull the acceptance criteria out **verbatim** and number them `AC1..ACn`. A branch or diff with no issue is allowed: take the claim from its spec, and when neither exists, ask which issue this pass proves.

Pin the range: base is the merge-base with `main` (or the parent commit), head is the commit or commits that claim to close the issue. If the work lives on another branch or PR, check it out first and pin the range there. Uncommitted work is fine; say so and use the working tree. Name the pass `<number>-<slug>` from the issue, or the branch slug when there is no issue.

If `/code-review` has run on this range, read its findings from `.scratch/review/<range>.md` or from what the user pastes. A fresh QA context cannot see a prior session's chat: when the findings aren't on disk, don't reconstruct them — treat code shape and spec coverage as settled ground and skip their axes. If the issue states no acceptance criteria, derive candidate ones from the request and the diff, confirm them with the user in one short round, and record that answer as the contract.

Done when: every AC is an observable statement, the range is pinned, and each AC traces back to the issue, the spec, or the user's confirmation.

### 2. Build the probe

Find the repo's run path — `package.json` scripts, `Makefile`, README, CI config. Take a **baseline** with the command CI runs: test suite, typecheck, lint. If the full suite takes more than a few minutes, run the subset that covers the changed surface and record what you skipped. A red baseline is itself a finding. Check it against the base: red there too is `pre-existing` and does not set the verdict on its own. Green there and red here is a regression from this change — at least a `major`, a `blocker` when it sits on the changed surface. Either way, a red baseline marks the ACs it prevents you from probing as `unproven`.

Then give each AC a **probe**: the cheapest repeatable command that reaches the promised behaviour at its outer boundary. A CLI invocation, an HTTP request, a headless browser, or one focused integration test. `/diagnosing-bugs` Phase 1 has the ladder; reuse it.

No runnable surface? Don't author a test to create one. An existing seam you can drive without adding code still counts as a probe; otherwise the AC is `unproven` — name the seam and the test that would observe it, and make "create the seam and the failing test" the first fix-list item.

Budget the probe. If one costs more than about ten minutes or two attempts to stand up, stop, record the AC as `unproven — probe not viable`, and name what you tried. QA's value is the verdict; a probe that never lands decides nothing.

Done when: every AC has a probe or a recorded reason it has none, and every probe has an expected output you would recognise as green.

### 3. Exercise the promise

Run every probe. Then push on the same surface, four ways:

- **Edge** — empty, zero, one, max, unicode, duplicate, very long.
- **Failure** — invalid input, missing dependency, timeout, permission denied, network off.
- **State** — a second run, a concurrent run, a restart mid-way, a retry after failure.
- **Boundary** — the layer above and below: UI ↔ API, service ↔ database, config ↔ runtime, flag ↔ docs.

Record the command, the observed output, and the verdict for each. A failure someone else cannot reproduce from the recorded command is not ready for the report.

Done when: every AC carries a verdict, every failure has a reproduction, and every `unproven` names what is missing — the observation, the seam, or a viable probe.

### 4. Prescribe the proof

A test is a claim about behaviour, and claims can be false. For each critical behaviour — each blocker-worthy AC and each error path that matters — find the test that should fail if the behaviour broke, and name the **mutation** that would prove it: delete the guard, flip the branch, return a constant.

QA does not apply the mutation. When the assertion visibly binds the behaviour, record it under `Verified clean` with the quoted line, marked `by inspection`. When it doesn't, the gap is a fix-list item: state the mutation, the test that must go red, and the acceptance check — mutation applied → red; mutation restored → green. A `major` when another probe observed the behaviour; `unproven` when that test was the only evidence.

Then read the tests for the shapes `/tdd` names: assertions against internals rather than public seams, tautological expected values, mocks of the thing under test, failure paths with no coverage.

Done when: every critical behaviour points at a quoted assertion that visibly binds it, or at a fix-list item naming the mutation that would prove it doesn't.

### 5. Walk the axes

Read [`AXES.md`](AXES.md) and walk every axis over the changed surface and its blast radius. Step 3's pushes are claim-shaped and the axes are surface-shaped: reuse their evidence where it exists — cite the command and output instead of re-running it — and probe what they did not reach: the failure surface, read-back, first run, security, performance, rollout, observability, compatibility. Tooling-covered ground is skipped: if the typechecker or linter already fails the build, it is not a finding.

Each axis ends `clean` or produces findings. Record the clean ones — the next agent needs to know what not to re-check. Label pre-existing problems `pre-existing`, and rank them above `minor` only when this change made them worse or exposed them.

Done when: every axis has been walked, and each has either a finding or a recorded `clean`.

### 6. Build the fix list

Read [`FIX-LIST.md`](FIX-LIST.md). Merge findings that share a root cause — one cause is one item, however many symptoms it produced. Severity comes from consequence, not effort, and [`FIX-LIST.md`](FIX-LIST.md) holds the rubric.

Order by the blocking edges first: an item comes after every item it is blocked by, even when that puts a major above a blocker. Among items with no blocking relation, higher severity goes first. The file reads top to bottom, and that is the execution order.

Done when: every finding carries every field, no item reads "improve X", and a fresh agent can start at `QA-1` with the file alone.

### 7. Write and hand off

If `.scratch/` isn't gitignored, don't edit `.gitignore`; say so under Out of scope so the fixer doesn't commit the evidence.

Write `.scratch/qa/<number>-<slug>.md` using the template in [`FIX-LIST.md`](FIX-LIST.md), then print in chat: the verdict line, the counts, the worst finding, and the path. Write the file in the issue's language and the chat summary in the user's.

Ask what happens next: fix now, file follow-ups, or stop. QA stops at the list and does none of them itself. Fix-now is a fresh `/implement` aimed at the file, in the file's order. File follow-ups is a separate action through `docs/agents/issue-tracker.md` — one issue per major and minor, each linking the report; a blocker is never deferred to a follow-up — fix it now or the release stops, and it is why the verdict is `don't ship`. If the user is away, stop by default: leave the file and the summary, and say which option you'd recommend.

After the fixes land, re-run the probes that went red and the acceptance checks of every item the fix touched — not the whole pass — then append a `## Re-run` section to the same file: date, new head, the AC rows that flipped, and the updated verdict. Keep the first pass intact; the file is a log, not a snapshot. A verdict flip is worth a QA session that did not write the fix; when the fixer flips it, label the row `self-check`.

Done when: the file exists, the chat summary is printed, and the user has an explicit next step.

## Verdict

- **Ship** — every AC proven, no blockers.
- **Ship with follow-ups** — every AC proven; majors and minors documented, none threatening the claim. A major is a risk accepted on the record, not a soft blocker.
- **Don't ship** — any AC `broken` or `unproven`, or a blocker that can hurt data, security, or a user.

`unproven` never becomes `ship` through explanation.

## Scale

One issue is one pass. A spec or a multi-ticket feature is one pass per ticket, then a final pass over the **integration seams** between them — the places each ticket's own tests cannot see. Aggregate into one fix list ordered by what the whole spec promised.
