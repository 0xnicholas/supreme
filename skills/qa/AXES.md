# Quality axes

The reference for step 5. Walk every axis over the changed surface and its blast radius. Reuse step 3's evidence rather than repeating it: an axis that lands on a finding the pushes already produced cites it. Each axis ends `clean` or with findings.

**The evidence bar** applies to all of them: a finding states the consequence — who is hurt, when, and how badly. No consequence, no finding.

## 1. Correctness and data

Does the output match the promise on real inputs?

Probe: a realistic golden case plus one adversarial input, then read back what was persisted. Check the second read, not the write.

Findings: off-by-one, wrong unit or timezone, partial write, non-idempotent retry, lost update, orphaned row, silent truncation.

## 2. Error handling and messages

What does a human see when it fails?

Probe: each failure from step 3. Read stdout, stderr, the UI, the exit code, and the log line.

Findings: stack trace shown to a user, swallowed exception, exit code 0 on failure, error naming internals, no path back (retry, undo, contact), a failure that leaves state half-applied.

## 3. Integration and rollout

Will this run where it ships?

Probe: a fresh environment or CI run; the migration forward, and back when it claims to be reversible; the config default with the env var absent; the feature flag in both states.

Findings: works on the dev machine only, non-idempotent migration, no rollback, a default that disables the feature, docs and flags disagree.

## 4. Security and permissions

Only claim what you reproduced. A suspicion belongs in "out of scope", not in the fix list.

Probe: no auth, another user's id, mass assignment, the changed sink with injection-shaped input, secrets in logs or errors, a file path that escapes its root.

Findings: missing authorization, IDOR, secret in output, unvalidated input at a trust boundary, unsafe deserialization, a dependency added without a reason.

## 5. Performance and resources

Report only a measurement or a clear complexity jump.

Probe: time it at a realistic size; count queries or requests; watch memory on a large input.

Findings: measured regression, N+1, unbounded fetch or fan-out, missing pagination, synchronous I/O on a hot path, a leak across runs.

## 6. UX and DX

Probe: the first run with no data; the error state; the destructive action; the CLI's `--help`; keyboard-only use.

Findings: dead-end state, jargon where `CONTEXT.md` has a word, a destructive action without confirmation or undo, an unlabelled control, help text that disagrees with behaviour.

## 7. Observability and docs

Can a human tell it worked, or why it didn't?

Probe: trigger success and failure at the log level the deployment actually uses; read the docs that describe the changed interface.

Findings: silent success path, failure with no log, a log without the id needed to investigate, README or API docs contradicting the new behaviour, a changed interface with no migration note.

## 8. Scope and residue

Probe: run the changed surface with the settings it ships with; look for behaviour nobody asked for that is reachable, and for the residue of the work that survives into runtime.

Findings: a feature flag nobody flips, debug output at info level, a TODO that encodes unfinished work in a shipped path.

Static shape — speculative abstraction, dead branches, commented-out code — belongs to `code-review`'s Standards axis. Don't re-file it here.

## 9. Compatibility

Probe: an old caller still compiles and runs; the old config still loads; the public type still accepts old values.

Findings: a breaking change to a public API, type, CLI flag, or config key without a note; a removal without a deprecation; a changed default that silently alters behaviour.

## Never a finding

Formatting, naming taste, abstraction preference, static shape, "I'd have done it differently", anything the repo's documented standards endorse, anything tooling already enforces. Those belong to `code-review`'s Standards axis, and repeating them here dilutes the fix list.
