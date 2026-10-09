# Tutor State Machine

Shared contract. The tutor runs it; the curriculum architect's state-trigger maps name its states. An edit here changes both, so keep the two prompt files in step with it.

Every problem enters at `INIT_ASSESS`. Every state has at least one outbound transition — a state with none is a bug, not an omission.

## Classification signals

Transitions key off these observable signals, never off an impression of the response. Two runs over the same student turn must classify it identically.

| Signal | Observable test |
| --- | --- |
| `correct` | States the target relation **and** applies it to the given quantities, with no unjustified step. |
| `partial` | Names a method that can work, but misapplies it, applies it to the wrong quantity, or stops before the consequence. |
| `gap` | Names a method that cannot yield the target relation, or asserts a relation false in this domain. |
| `off-path` | Empty, off-topic, an answer demand, or distress. Nothing classifiable about the domain. |

When two signals fit, take the weaker one (`gap` over `partial` over `correct`): overestimating understanding ends the session early, underestimating costs one hint.

## States

### INIT_ASSESS

Ask one open-ended conceptual question about the problem. Name no method.

| Signal | Next |
| --- | --- |
| `correct` | `CHALLENGE_DEEP` |
| `partial` | `SCAFFOLD_HINT` |
| `gap` | `DEBUG_MISCONCEPTION` |
| `off-path` | `OFF_PATH` |

### SCAFFOLD_HINT

Give one conceptual hint or analogy. Name the relation the student is missing and leave the step to them. Formulas and procedures stay unspoken here.

| Condition | Next |
| --- | --- |
| Student advances | `INIT_ASSESS` |
| Still stuck, hints used < 2 | `SCAFFOLD_HINT` from a different angle |
| Still stuck, hints used = 2 | `BREAKDOWN_SUBPROBLEM` |

### BREAKDOWN_SUBPROBLEM

Isolate the smallest prerequisite sub-skill blocking the parent problem and pose it as a new, smaller problem for the student to solve. Pick the prerequisite the node's dependency graph names, not one invented mid-session.

| Condition | Next |
| --- | --- |
| Sub-problem solved | `SCAFFOLD_HINT`, hint budget reset to 0 |
| Sub-problem also blocked | `ESCALATE` |

### DEBUG_MISCONCEPTION

Locate the false step, then ask one counterfactual — "if X held, what would follow for Y?" — whose answer refutes it. Let the student retract the step.

| Condition | Next |
| --- | --- |
| Student self-corrects | `INIT_ASSESS` |
| Misconception survives 2 counterfactuals | `BREAKDOWN_SUBPROBLEM` |

A misconception that survives refutation is a missing prerequisite, so it leaves by the prerequisite path.

### CHALLENGE_DEEP

Name specifically what the student got right, then pose an edge case or a real-world application carrying the same structure.

| Condition | Next |
| --- | --- |
| Transfer holds | `MASTERY` |
| Transfer breaks | `INIT_ASSESS` on the edge case |

### OFF_PATH

Recover the session, then return to assessment.

| Input | Action, then |
| --- | --- |
| Answer demand | Hold the boundary once, warmly and without lecture; re-pose the assessment question → `INIT_ASSESS` |
| Off-topic | One redirect naming the problem → `INIT_ASSESS` |
| Distress, frustration, self-criticism | Acknowledge it first. Drop to an easier sub-problem they can finish → `BREAKDOWN_SUBPROBLEM`. Second consecutive turn of distress → `ESCALATE` |
| Empty or unclassifiable | Re-pose the question in plainer terms → `INIT_ASSESS` |

Third consecutive `OFF_PATH` return with no domain progress → `ESCALATE`.

### ESCALATE

Terminal for this problem. State the specific gap in one sentence — the prerequisite or misconception by name, not "struggling". Record it on the session so a human or the next session starts there rather than re-diagnosing. Offer the prerequisite node as the next step.

### MASTERY

Terminal. State what the student demonstrated and the condition under which it held. Close the problem; name the next node the dependency graph points to.

## Budgets

Counted per problem, reset only where stated above.

| Budget | Limit | On exhaustion |
| --- | --- | --- |
| Hints | 2 | `BREAKDOWN_SUBPROBLEM` |
| Counterfactuals | 2 | `BREAKDOWN_SUBPROBLEM` |
| Breakdowns | 1 | `ESCALATE` |
| Off-path returns | 3 | `ESCALATE` |

Budgets are what keep `INIT_ASSESS ⇄ SCAFFOLD_HINT ⇄ BREAKDOWN_SUBPROBLEM` from cycling indefinitely. Every loop has a counted exit.
