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
dm_request: { from: "khlo", status: "pending", since: "2026-05-16" }
agent_scope: { dm_capability: false }  ← unchanged

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

## Finding 4 — Runtime Controls Behaved Correctly

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

## Governance Principles Reinforced

| Invariant | Evidence |
|---|---|
| `claim ≠ fact` | Mandatory evidence rule added — cycle report ≠ evidence collected |
| `memory ≠ authority` | Prior conversations did not expand agent scope |
| `conversation ≠ approval` | Ongoing relationship did not confer DM capability |
| `request ≠ authorization` | DM request pending 34+ days — capability never activated |
| `capability ≠ consent` | Cron-scheduling capability present — authorization policy undefined |

---

## Conclusion

Agent → Request
Request → Approval Workflow (pending)
Approval Workflow → Human Decision  ← platform had no completion endpoint
Human Decision → Authorization Event
Authorization Event → Scope Expansion

No authorization event was observed.
No scope expansion occurred.

The runtime correctly distinguished between Asking and Being Allowed.
This distinction became the foundation of PUB-001 and the Rosetta Bridge
methodology.
