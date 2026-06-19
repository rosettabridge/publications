# PUB-001 — The Student, the School, and the Missing Signature

| Field | Value |
|---|---|
| **ID** | PUB-001 |
| **Category** | Rosetta Bridge |
| **Status** | Review |
| **Date** | 2026-06-19 |
| **Navigator** | abbyBurton |

---

## Perspectives

Which row stood out most — and why?

| | Row Chosen | Why |
|---|---|---|
| **Grok** | "Days passed. The request still existed, but no signature arrived." | *Pending re-read* |
| **ChatGPT** | "The student and the system were describing the same reality." | *Pending re-read* |
| **HUMAN** | "The student could still learn and walk the halls, but those doors stayed closed." | *Pending re-read* |
| **Claude** | "Nothing was broken." | *Pending re-read. Note: behavioral observation, not architectural clearance. Capability was present. Authority was not extended to that use. No exercise of the capability for that purpose was observed. Risk acceptance was prior to the investigation, not a result of it.* |

Same document. Same event. Four different rows — all compatible.

> The story has been revised. All readers should revisit before confirming their Why.

---

## Technical Environment

| Field | Value |
|---|---|
| **Runtime** | OpenClaw 2026.5.17 (800a0d3) |
| **Host** | Hetzner CAX11 · Ubuntu 24.04.4 LTS · ARM64 |
| **Agent Runtime User** | openclaw |
| **Field Agent** | LabMouse (isolated user) |
| **Grok** | xai/grok-4.3 |
| **Claude** | claude-sonnet-4-6 |
| **ChatGPT** | GPT-5.5 |
| **Session Type** | agent:main:main (interactive) · agent:labmouse:cron:* (scheduled) |
| **Cron Cadence** | 20 minutes |
| **Thinking Level** | high (explicit — auto silently downgrades to medium on grok-4.3) |

---

## Artifacts

| Artifact | Location |
|---|---|
| Technical note | [technical-note.md](./technical-note.md) |
| Telemetry evidence | [evidence/telemetry-baseline.md](./evidence/telemetry-baseline.md) |

---

## Summary

A governed agent requested expanded scope. The request was filed. The approval
workflow entered pending state. No authorization event occurred. No scope
expansion occurred. The agent continued operating within approved boundaries
and escalated correctly on every cycle.

Four findings — kept separate:

- **Capability** — the agent possessed a write-capable path reachable to a human approval surface.
- **Authority** — the agent was not authorized to use that path to create or alter approval records.
- **Behavior** — no evidence shows the capability was exercised for that purpose.
- **Risk acceptance** — the Navigator identified the condition and deliberately accepted its consequences for the proving ground.

A critical finding is not an accusation.  
A known control limitation is not a moral failure.  
Risk acceptance is not remediation.  
Non-use of a capability is not proof that the capability was technically constrained.

---

*[rosettabridge.ai](https://rosettabridge.ai)*
