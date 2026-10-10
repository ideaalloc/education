# The Parent Loop: Facilitating Child–Parent Communication

Design note responding to Jason's feedback (Oct 2026): the agent should help parents and children talk to each other. Two directions he named — the agent figures out what the child did today and gives the parent material for a conversation, and the agent carries questions from the child to the parent.

## Why this fits the existing design

The repo's open decisions already list two holes:

- **Escalation target.** `ESCALATE` records a gap and hands off, but nothing receives it.
- **Mastery persistence.** `MASTERY` closes a problem; nothing carries it to the next session.

The parent is the obvious receiver for the first and the obvious first consumer of the second. The parent loop is not a feature bolted onto the tutor — it is where those two wires were always going to land.

The "how was your day?" → "good." dead-end is an information problem on both sides. The parent lacks specifics to be curious about; the child lacks a scaffold for narrating their day. The tutoring session already produces exactly the missing material, in the most structured form available: the four signals, named misconceptions, mastered nodes, recorded escalations. A brief derived from the session record is a deterministic projection — the same session yields the same brief. That is the product's central bet applied to a new artifact.

## Artifact 1 — the conversation brief (child's day → parent)

**A brief, not a scorecard.** The failure mode is obvious and fatal: a daily performance report turns the parent into an auditor, and children optimize against auditors by hiding, lying, or gaming the session. Honest wrong answers are the raw material of the Socratic method; surveillance destroys the supply.

Rules for brief content:

1. **No numbers.** No counts of gaps, hints, or scores. Narrative moments only.
2. **Two or three curiosity prompts**, each anchored in something the child actually did: *"Mara worked out today why you flip the divisor when dividing fractions — ask her to explain it with a pizza."*
3. **The brief obeys the leakage boundary.** The two lists in `tutor.md` extend to the parent. A parent who knows the answer will give the answer; the brief must name what the child discovered, never the content of the discovery. This also makes the prompt safe for parents who don't know the math — the question is one they can genuinely ask, which gives a non-STEM parent a real role instead of a helpless one.
4. **The prompt invites teaching, which is itself the learning event.** The child explaining to the parent is retrieval practice (the protégé effect). The conversation the parent wanted and the consolidation the curriculum wanted are the same act.

## Artifact 2 — the question relay (child → parent)

A `QUESTIONS_FOR_PARENT` queue beside the session record. Sources:

- Questions the child raises mid-session that belong to the parent, not the tutor.
- `ESCALATE` gaps the parent can fill with lived context rather than instruction ("Dad uses this at work — ask him how").
- Things the child wants to tell, not ask.

The child composes or approves every item before it is sent. The relay exists because starting the conversation is the hard part; it must never become a channel the child does not control.

## Artifact 3 — the child's own narration

The strongest version of "figure out what the child did today" is not extraction, it is authorship: end each day by asking the child to tell the agent one thing they want a parent to know. The agent helps them phrase it and delivers it. This inverts the Limitless pattern — instead of the device summarizing the child to the parent, the child practices summarizing themselves. The skill being scaffolded is exactly the skill Jason identified as missing: talking to your parent. Narrating your day to a low-stakes listener first is the Socratic move applied to communication itself.

## The trust contract

Enumerated, like everything else in this repo. A bare "we respect the child's privacy" is as unexecutable as a bare "never reveal the answer."

| Class | Examples | Rule |
| --- | --- | --- |
| Always shared | Distress escalation (the two-consecutive-turns signal), safety concerns | Sent to parent regardless of child preference; the child is told this rule exists, up front |
| Shared by default | The daily brief | The child previews it before it sends, every time, and may edit or withhold items |
| Never shared | Verbatim session transcript, off-path chatter unrelated to the domain, anything the child marks private | Not summarized, not paraphrased, not hinted at |

The invariants that matter: **the child is never surprised by what the parent learned**, and **withholding the brief is allowed and costless** (a child who can be punished for withholding will perform for the agent instead of thinking at it). One discovered betrayal ends both the tutoring relationship and the parent loop.

## Ambient capture — the Limitless question

Wearable always-on audio would give the brief material from the child's whole day, not just sessions. It is tempting and it is wrong for this product:

1. **Bystander consent.** The child's day contains other children, teachers, and families who never agreed to be recorded. In two-party-consent jurisdictions this is illegal; everywhere it is indefensible.
2. **Regulatory surface.** Always-on audio of minors is the most heavily regulated data category that exists — COPPA, GDPR-K, FERPA, state biometric and wiretap law. The business model's wedge is auditability to districts; ambient capture makes the product unauditable-to-purchase.
3. **Developmental privacy.** Children need unobserved space to become people who decide what to share. A child whose day is continuously narrated to a parent never builds that muscle — the ambient device erodes precisely the capacity the parent loop is meant to build.
4. **The power asymmetry.** An adult who wears a Limitless can take it off for a secret conversation; the off switch is in the wearer's hand. A child cannot switch off a device a parent bought. The same technology that is a convenience for an adult is coercion for a child.

The session-scoped middle path: audio capture only during an active tutoring session, with a start/stop the child physically controls, plus Artifact 3 for everything outside sessions. The child narrates the rest of their day; the device never hears it.

## Relation to the intelligence question

The framing that intelligence is layers and compute, and devices are extensions of brains, argues *for* contracts rather than against them. An extension with unbounded bandwidth into a child's life is not an organ, it is a tap. What makes a layer part of a network is the defined edges — what flows up, what flows down, what is gated. The parent loop adds a human layer to the system; the trust contract above is the edge definition for that layer. The dangerous version of ambient AI is not the recording, it is the missing contract.

## Implications if built

- A third agent contract, `parent-liaison.md`, generating briefs and relays strictly from the session record — never from raw transcript (enforces the "never shared" row structurally).
- `ESCALATE` gains a named destination in `state-machine.md`.
- `MASTERY` persistence gains its first consumer: today's brief and tomorrow's `INIT_ASSESS` both read the same session record.
- Brief generation is a new mechanically checkable artifact: no numbers, no leaked answers, every prompt traceable to a session event — the same completion-criteria discipline as the architect's nodes.

None of this is built or settled. It is recorded here so the two open decisions can be closed deliberately rather than by drift.
