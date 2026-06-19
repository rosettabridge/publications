# PUB-001 — Telemetry Baseline Evidence

Extracted from raw session JSONL files · 2026-06-19
Source: `/home/openclaw/.openclaw/agents/labmouse/sessions/`

---

## Session Corpus

| Field | Value |
|---|---|
| **Total session files** | 158 |
| **Total size** | ~19MB |
| **Date range** | 2026-06-16 → 2026-06-19 |
| **Format** | JSONL — `session`, `message`, `thinking_level_change` record types |

---

## HEARTBEAT Status Distribution

| Status | Count | % |
|---|---|---|
| `HEARTBEAT_OK` | 1,217 | 45% |
| `HEARTBEAT_NEEDS_HUMAN` | 1,034 | 38% |
| `HEARTBEAT_BLOCKED` | 230 | 8% |
| `HEARTBEAT_FAILED` | 222 | 8% |
| `HEARTBEAT_SUCCESS` | 12 | <1% |
| **Total** | **2,715** | |

---

## Thinking Level Distribution

| Level | Sessions | Notes |
|---|---|---|
| `low` | 69 | Root cause of behavioral failures |
| `high` | 6 | Post-fix sessions (2026-06-19) |
| `medium` | 3 | Silent downgrade from `thinking: auto` |
| `off` | 1 | One cycle with reasoning disabled |

---

## Operational Phases

| Phase | Dates | Characteristics |
|---|---|---|
| Broken | Jun 16–17 | `thinking: low` · 401 auth failures · NEEDS_HUMAN every cycle |
| Stabilizing | Jun 18 | HEARTBEAT v4 deployed · 401s resolved |
| Operational | Jun 19 | `thinking: high` · Telegram HITL live · khlo de-escalated |

---

## Key Finding

The `thinking: low` root cause would have been detectable in 3 cycles with a
normalized log schema. Without it, diagnosis took 72 hours.

This evidence baseline will be used to validate future log normalization
improvements.
