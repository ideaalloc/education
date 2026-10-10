# Catechism, Forensics, and What the Tutor Already Is

Design note prompted by Jason's feedback (Oct 2026): a talk arguing American education shifted from producing independent thinkers to producing instruction-followers, and that the older pedagogy ran on **catechism** (question-and-answer, answering questions with questions) and **forensics** (formal debate). The John Adams asparagus story: debating which end of the asparagus is meant to be eaten — the topic is trivial, the trained skill is not.

## The distinction that matters

"Catechism" carries two meanings and only one is useful here:

1. **Catechism as rote** — the Westminster pattern: memorize the prescribed Q&A pairs verbatim. This is the assembly-line pedagogy the talk criticizes, wearing an older costume. Nothing in this repo should want it.
2. **Catechism as interrogation** — answering a question with a question, requiring the respondent to produce the reasoning, not recite the answer. This is the Socratic method, and it is literally what `tutor.md` specifies.

The historical claim in the talk is romanticized (classical catechism was heavily meaning-1; the real thinking-training in the old curriculum was the medieval university **disputation**, which is meaning-2's loud cousin). But the design lesson survives the bad history.

## What the state machine already implements

| Talk's concept | Existing contract mechanism |
| --- | --- |
| Answer a question with a question | `INIT_ASSESS` names no method; every state exits into another question |
| Never recite the teacher's answer | Leakage boundary: the assigned problem's answer, derivation, and code stay unshown |
| Apologetics — know why you believe what you believe | `correct` requires *no unjustified step*; a right answer with an unjustified step classifies as `partial` |
| Confront the false belief | `DEBUG_MISCONCEPTION`: one counterfactual — "if X held, what would follow for Y?" — whose answer refutes the step; the student retracts it themselves |
| Transfer, not recall | `CHALLENGE_DEEP`: edge case or real-world application carrying the same structure |
| Multiple choice vs. constructed response | The four signals classify *reasoning*, not answer selection; there is no answer-selection surface at all |

One sentence for humans: **this is a catechism machine that never accepts a recited answer.**

## What it does not implement: forensics

The gap Jason's note exposes is real. The current machine assumes every problem has one target relation; the tutor's job is to lead the student to it. Debate is a different game:

- **Contestable positions.** No single target relation, so `correct`/`partial`/`gap` have nothing to key off. Forensics would need its own signals — e.g. `warranted` / `asserted` / `unanswered-objection` — classifying the quality of justification, not proximity to an answer.
- **The leakage boundary doesn't transfer.** Two lists (shown / unshown) presuppose an answer that exists to leak. In debate the analogous boundary is about the tutor not arguing the student's side for them — a different enumeration, invariant 4 restated for a new mode.
- **Both-sides discipline.** The trainable skill in the asparagus story is arguing a position you may not hold, against live objection. A forensics mode would assign positions, not let students pick comfortable ones.

The asparagus principle is what makes this cheap to try: the topic doesn't matter, the disputation structure does. Any existing curriculum node could gain a disputation variant ("argue that method A is superior to method B for this class of problem; then switch sides") without new domain content — the architect already knows each node's methods and misconceptions, and misconceptions are ready-made opposing positions.

## The AI-era framing

The talk's complaint — education optimized for producing answers — got catastrophically worse the moment answer production became free. An LLM in every pocket is the ultimate multiple-choice machine: it will supply any answer on demand. That is precisely the demand the tutor refuses, by contract. Scarcity moved from answers to the capacity to produce and defend them; the state machine is a device for exercising exactly that capacity, identically every session. This is also the honest reply to "kids can't think anymore": the fix is not less AI, it is AI whose contract makes thinking non-optional — which no answer-first product can retrofit, because its customers bought the answers.

## Status

Recorded, not scoped. Open decision added to `AGENTS.md`: whether a forensics/disputation mode belongs alongside the problem-solving machine, with its own signals and its own restatement of the leakage boundary. The parent loop (`research/parent-child-loop.md`) is unaffected either way — a debate the child had today is ideal brief material ("argue at dinner, both sides, asparagus rules").
