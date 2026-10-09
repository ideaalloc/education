# Education

Autonomous STEM tutoring: a Socratic tutor that leads students to answers it never gives, and a curriculum architect that generates the lesson nodes the tutor runs on. Both are LLM agents driven by the prompts and contracts in `socratic_tutoring/`.

Optimise for consistency across sessions over any single session's elegance — the same student turn must produce the same transition every run. That determinism, not answer quality, is the product.

## Layout

- `socratic_tutoring/state-machine.md` — the shared contract: states, classification signals, budgets. Read before editing either agent, and after editing either one.
- `socratic_tutoring/tutor.md` — student-facing tutor system prompt. Read when changing tutoring behaviour, tone, or the leakage boundary.
- `socratic_tutoring/curriculum-architect.md` — internal generator system prompt plus the topic-node output contract and its completion criteria. Read when changing generated assets.

No runtime code, no build, no tests yet. These are prose contracts an LLM executes, so the documents *are* the implementation and a loose sentence is a loose behaviour.

## Invariants

These bind every edit. A change that breaks one is a bug, not a style choice.

1. **No dangling states.** Every state has an outbound transition, and every transition names a state that exists in `state-machine.md`.
2. **Classify on signals.** Transitions key off the four observable signals, never off an impression of the response.
3. **Every loop has a counted exit.** A cycle between states is bounded by a budget, and budget exhaustion escalates.
4. **Leakage boundary is two lists.** What the tutor shows and what it keeps unshown are both enumerated. A bare prohibition is unexecutable: it also bans the worked example, the strongest hint available.
5. **One source of truth.** State names, signals, and budgets live in `state-machine.md`. The prompt files restate them only where runtime needs them inline; on conflict, `state-machine.md` wins.
6. **Generated nodes pass their criteria.** A node is done when every completion criterion in `curriculum-architect.md` holds, not when it looks right.

## Validation

Nothing enforces these automatically yet. Check by hand:

- **State-machine edits** — walk every state against invariants 1–3. The failure is silent: a missing transition surfaces mid-session as a tutor with nowhere to go.
- **Prompt edits** — re-read for the four signals still being operational, and for new negations that lack a positive counterpart.
- **Generated nodes** — run criteria 1–7 in `curriculum-architect.md`. Criteria 2–4 are the silent failures; they parse cleanly and break at runtime.

Not a git repository, so content edits are unrecoverable. Confirm before overwriting a prompt file.

## Open decisions

Unresolved. Settle before building on them.

- **Node schema enforcement.** `node.schema.json` is unwritten; the field list in `curriculum-architect.md` is the interim contract. Criteria 1–4 are mechanically checkable once it exists.
- **Escalation target.** `ESCALATE` records a gap and hands off, but nothing receives it. Needs a destination: human reviewer, queue, or prerequisite-node redirect.
- **Mastery persistence.** `MASTERY` closes a problem; nothing carries it to the next session. Student state across sessions is undesigned.
- **Distress threshold.** Two consecutive turns of distress escalates. Whether that is the right sensitivity is untested against real sessions.
