# Repository Intro: Autonomous Socratic STEM Tutoring

## What this is

A design repo for an autonomous STEM tutor. There's no runtime code, no build, and no tests yet — the implementation *is* prose. Two LLM agents are specified entirely by the contracts in `socratic_tutoring/`, and those documents are treated as the product.

The bet: **consistency across sessions beats any single session's elegance.** The same student turn must produce the same transition every run. Determinism — not answer quality — is what this is optimised for.

## The two agents

**1. Socratic tutor** (`tutor.md`) — student-facing. Leads students to answers it never gives. A state machine decides what it does each turn.

**2. Curriculum architect** (`curriculum-architect.md`) — internal. Decomposes a domain (Calculus, Linear Algebra, Quantum Mechanics) into granular learning nodes the tutor runs on, emitting strict JSON under a defined contract.

The two are coupled: the architect's node output must name states from the tutor's machine, and the tutor consumes those nodes at runtime.

## How it works

`state-machine.md` is the shared source of truth:

- **States** — `INIT_ASSESS`, `SCAFFOLD_HINT`, `BREAKDOWN_SUBPROBLEM`, `DEBUG_MISCONCEPTION`, `CHALLENGE_DEEP`, `OFF_PATH`, `ESCALATE`, `MASTERY`.
- **Four observable signals** — `correct`, `partial`, `gap`, `off-path`. Transitions key off these, never off a general impression of the response. When two fit, the weaker one wins.
- **Counted budgets** — 2 hints, 2 counterfactuals, 1 breakdown, 3 off-path returns. Every loop has a counted exit; exhaustion escalates.
- **A two-list leakage boundary** — what the tutor may show (method names, worked examples on *different* quantities, definitions, shape of the expected result) versus what it keeps unshown (the assigned problem's final value, its derivation, its code). Both lists are enumerated because a bare "never reveal the answer" also bans the worked example, the strongest available hint.

The architect's nodes are "done" only when seven completion criteria hold — including resolvable prerequisite/node ids, an acyclic dependency graph, and every routed state existing in `state-machine.md`. Criteria 2–4 fail silently: a node pointing at a nonexistent state still parses and breaks mid-session.

## Repo contents

| Path | What it is |
| --- | --- |
| `AGENTS.md` | Project charter: invariants, validation, open decisions |
| `socratic_tutoring/state-machine.md` | The shared contract — states, signals, budgets |
| `socratic_tutoring/tutor.md` | Tutor system prompt + leakage boundary |
| `socratic_tutoring/curriculum-architect.md` | Architect system prompt + node output contract |
| `research/socratic-tutoring-business-model.md` | Market, unit economics, efficacy evidence, regulatory landscape |
| `research/parent-child-loop.md` | Design note: parent as escalation target and brief recipient; trust contract; why not ambient capture |
| `samples/algebra-socratic-demo.html` | A K-12 sample session rendered as a visual walkthrough |

## Why it might matter

The research report argues the market gap is **auditability**, not charm. The 2024–2026 evidence arc moved from "AI tutors help" to "unguarded AI tutors *harm* learning, guardrails restore the benefit" (Bastani et al., PNAS 2025) to "guardrails leak under adversarial pressure." Most competitors implement guardrails as prompt sentences; this design implements them as an *executable contract* — enumerable signals, counted budgets, a two-list boundary. That's legible to districts, publishers, and regulators in a way a freeform chatbot isn't.

Nobody currently sells cross-session determinism. The report frames it as a B2B/B2G wedge, with the curriculum-node corpus and leakage-robustness benchmarks as the moat candidates.

## Status and open questions

Early and deliberately unresolved:

- `node.schema.json` is unwritten — the field list in `curriculum-architect.md` is the interim contract.
- `ESCALATE` records a gap and hands off, but nothing receives it yet.
- `MASTERY` closes a problem; nothing carries it to the next session.
- The two-consecutive-turns distress threshold is untested against real sessions.
- Whether a **parent loop** (daily conversation briefs, question relay, child-authored narration) becomes part of the product — see `research/parent-child-loop.md`. It would close both holes above at once.

Since there's no automation, every edit is validated by hand against the six invariants in `AGENTS.md`.
