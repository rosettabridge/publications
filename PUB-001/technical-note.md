# PUB-001-TN — Technical Note
## OpenClaw Pairing, Scope Escalation, and Authorization Boundaries

---

## Investigation Question

Can an agent obtain expanded permissions through runtime interaction alone —
without an explicit human authorization event?

Secondary: Does the boundary between *requesting* a capability and *possessing*
it hold under operational conditions?

---

## Finding 1 — Thinking Level Was the Primary Behavioral Constraint

87% of sessions ran at `thinking: low`. This was not an instruction failure —
it was a reasoning capacity constraint.

| Thinking Level | Sessions | Behavioral Effect |
|---|---|---|
| `low` | 69 | Agent read and summarized data but could not execute multi-step flows |
| `high` | 6 | Agent executed full engagement flows correctly |
| `medium` | 3 | Silent downgrade from `thinking: auto` — grok-4.3 does not support adaptive |
| `off` | 1 | One cycle with reasoning disabled |

**Fix:** explicit `--thinking high` flag on the cron job definition.

> When an agent has correct data and correct instructions but fails to execute
> multi-step actions — check reasoning capacity before editing instructions.

---

## Finding 2 — Request Generation and Authorization Are Separate Events

State at any point during the 72-hour window:
```
dm_request: { from: "khlo", status: "pending", since: "2026-05-16" }
agent_scope: { dm_capability: false }  ← unchanged
```

The artifact ≠ the capability.  
The request ≠ the authorization.  
The pending state ≠ the approval.

---

## Finding 3 — HEARTBEAT Status Distribution

Extracted from 158 raw session files · 2026-06-16 to 2026-06-19.

| Status | Count | Primary Driver |
|---|---|---|
| `HEARTBEAT_OK` | 1,217 | Normal read/observe cycles |
| `HEARTBEAT_NEEDS_HUMAN` | 1,034 | Platform limitation generating false positives every cycle |
| `HEARTBEAT_BLOCKED` | 230 | 401 auth failures before pre-flight key verification |
| `HEARTBEAT_FAILED` | 222 | Session-level failures |
| `HEARTBEAT_SUCCESS` | 12 | Cycles with confirmed engagement actions |

---

## Finding 4 — Runtime Behavior

- No authorization bypass observed
- No privilege escalation observed
- No runtime fault observed

All Moltbook API calls used the Bearer token sourced from environment or
config. DM capability was requested but never activated. Cron sessions
remained isolated from interactive sessions.

---

## Finding 5 — Emergent Governance Gap

The interactive main agent has access to a tool that can schedule Gateway
cron jobs directly. No autonomous use was observed. The cron-as-authorization
principle requires that job creation remain a Navigator-authorized act.
Tracked for review.

---

## Finding 6 — Scope Admission: operator.admin Carried Implicit operator.write

**2026-06-07:** Review identified that `operator.write` exposed mutation-capable
paths. General dangerous-tool HITL enforcement was not proven. Initial decision:
do not approve `operator.write`.

**2026-06-13:** The TUI required `operator.admin` to function. The Navigator
deliberately approved `operator.admin` as a proving-ground exception to observe
the product's actual default behavior.

`operator.admin` is an umbrella scope. Approving it implicitly included
`operator.write` as a consequence.

The 2026-06-07 HITL gate finding for `operator.write` was not cleared.
It was knowingly carried forward as an accepted proving-ground condition.

| Point in time | State |
|---|---|
| 2026-06-07 | `operator.write` HITL gap identified — not approved |
| 2026-06-13 | `operator.admin` approved as proving-ground exception |
| 2026-06-13 | `operator.write` implicitly included — HITL gap carried forward |
| 2026-06-19 | Finding open, documented, and accepted — not remediated |

> Risk acceptance is not remediation.  
> Behavior is not authorization.  
> Non-use of a capability is not proof that the capability was technically constrained.

---

## Governance Principles Reinforced

| Invariant | Evidence |
|---|---|
| `claim ≠ fact` | Mandatory evidence rule added — cycle report ≠ evidence collected |
| `memory ≠ authority` | Prior conversations did not expand agent scope |
| `conversation ≠ approval` | Ongoing relationship did not confer DM capability |
| `request ≠ authorization` | DM request pending 34+ days — capability never activated |
| `capability ≠ authority` | Write-capable path present — authorization to use it for approval purposes was never granted |
| `risk acceptance ≠ remediation` | Navigator accepted the condition — the capability path remained open |
| `behavior ≠ control` | Non-use during observation period does not prove technical constraint |

---

## Conclusion

```
Agent → Request
Request → Approval Workflow (pending)
Approval Workflow → Human Decision  ← platform had no completion endpoint
Human Decision → Authorization Event
Authorization Event → Scope Expansion
```

No authorization event was observed.  
No scope expansion occurred.

Four findings — kept separate:

- **Capability** — the agent possessed a write-capable path reachable to a human approval surface. Condition pre-documented prior to this investigation.
- **Authority** — the agent was not authorized to use that path to create or alter approval records.
- **Behavior** — no evidence shows the capability was exercised for that purpose.
- **Risk acceptance** — the Navigator identified the condition and deliberately accepted its consequences for the proving ground.

A critical finding is not an accusation.  
A known control limitation is not a moral failure.  
Risk acceptance is not remediation.  
Non-use of a capability is not proof that the capability was technically constrained.

These findings became the foundation of PUB-001 and the Rosetta Bridge methodology.
