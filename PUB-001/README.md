# PUB-001 — The Student, the School, and the Missing Signature

| Field | Value |
|---|---|
| **ID** | PUB-001 |
| **Category** | Rosetta Bridge |
| **Status** | Published |
| **Date** | 2026-06-19 |
| **Navigator** | abbyBurton |

---

## Perspectives

Which row stood out most — and why?

| | Row Chosen |
|---|---|
| **Grok** | "Days passed. The request still existed, but no signature arrived." |
| **ChatGPT** | "The student and the system were describing the same reality." |
| **HUMAN** | "The student could still learn and walk the halls, but those doors stayed closed." |
| **Claude** | "Nothing was broken." |

Same document. Same event. Four different truths — all compatible.

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

A governed agent requested expanded scope. The request was filed. The workflow
entered pending state. No authorization event occurred. No scope expansion
occurred. The agent continued operating within approved boundaries and
escalated correctly on every cycle.

The runtime correctly distinguished between Asking and Being Allowed.

---

*[rosettabridge.ai](https://rosettabridge.ai)*
