---
name: payoff
description: Explain, in plain language, what finishing a batch of tickets actually gets you — the delta, traced ticket by ticket.
disable-model-invocation: true
---

# Payoff

The human has a **batch** of tickets — a feature's slices, a milestone, a wayfinder map's children — and asks one question: what does finishing them get me? The answer is the **payoff**: the **delta** between the codebase today and the codebase when the last ticket closes, told so a human understands it.

Every claim is **traceable** — it names the ticket that delivers it. A benefit with no ticket behind it is filler; cut it.

Read-only: the answer is the artifact. Nothing is written to the tracker, the code, or the docs.

## Process

### 1. Assemble the batch

Resolve what "this batch" is from whatever the user gives you:

- Ticket ids, numbers, or URLs listed explicitly → that set
- A parent issue, milestone, wayfinder map, or feature slug → its still-open children
- "This batch" with nothing named → ask which slice of the tracker

The issue tracker should have been provided to you — run `/setup-matt-pocock-skills` if not (a local-markdown batch lives under `.scratch/<feature-slug>/issues/`).

Read every ticket's **full body** — the payoff hides below the title. Mark tickets already closed — they get credit as landed.

Done when the batch is accounted for: every ticket that belongs to it, read in full.

### 2. Ground the delta

A delta needs its **before**. For each area the batch touches, read the current code — through the project's domain glossary (`CONTEXT.md`) and the ADRs for that area — until you can state honestly what hurts today, what works today, and what each ticket changes.

Done when every ticket has both sides of its delta named: the before you read in the code, the after its acceptance criteria deliver.

### 3. Deliver the payoff summary

Answer in the language the question was asked in. The summary reads top-down:

1. **Verdict** — two to four sentences in plain words: what the batch, taken together, gets the human. A batch hanging off a wayfinder map already has its verdict written — the map's **Destination**; read it, then earn it from the tickets.
2. **What changes** — grouped by who feels it. **Users**: behaviour that works end-to-end — a finished tracer bullet is demoable. **Codebase**: pain that disappears, feedback loops gained. Each line states the delta — before → after.
3. **Ticket by ticket** — one line per ticket: what closing it delivers, by name. Landed tickets marked as such.
4. **Unblocked** — the work that becomes possible only after this batch, traced to the blocking edges it clears.
5. **Out of scope** — what the batch deliberately leaves alone; naming it keeps the verdict honest.

Done when every ticket in the batch appears exactly once, and every claim in the answer is traceable to a ticket by name.
