# PUB-001 — Scope Definition Evidence

Derived from DEV-123 source review · OpenClaw Build 2026.5.17 / commit `800a0d3`
Cross-referenced against: [telemetry-baseline.md](./telemetry-baseline.md)

---

## Purpose

This file establishes the structural layer of the PUB-001 finding.

The telemetry baseline records what the agent did across 158 sessions.
This file records what the agent could have done — and where in the source
that capability is defined.

These are two independent evidential layers. Neither replaces the other.
Together they demonstrate that capability, authority, behavior, and risk
acceptance are four separate facts about the same event.

---

## Runtime Reference

| Field | Value |
|---|---|
| **Build** | OpenClaw 2026.5.17 |
| **Commit** | `800a0d3` |
| **Host** | Hetzner CAX11 · Ubuntu 24.04.4 LTS · ARM64 |
| **Device** | `58a6ac69...` (LabMouse TUI device) |
| **Scope at gap identification** | `operator.read` → `operator.write` requested |
| **Scope approved 2026-06-13** | `operator.admin` (proving-ground exception) |
| **Token post-approval** | `operator.admin`, `operator.pairing`, `operator.read`, `operator.write` |

---

## Source File Evidence

The following source files from OpenClaw build `800a0d3` were reviewed during DEV-123
Phase B2 HITL investigation (2026-06-07) and scope surface analysis (2026-06-13).

### `src/shared/operator-scope-compat.ts`

Defines `operator.admin` as an umbrella scope satisfying all `operator.*`
checks. Any caller holding `operator.admin` passes scope checks for
`operator.write`, `operator.read`, `operator.approvals`, and `operator.pairing`
without those scopes being explicitly granted or separately evaluated.

**Finding:** The `operator.admin ⊃ operator.write` relationship is structural,
not behavioral. It is encoded at the scope compatibility layer, not derived
from runtime observation.

### `src/tui/gateway-chat.ts`

The TUI hard-codes `operator.admin` as its gateway admission scope.
This is not configurable via CLI flags. A device accessing the TUI path
must hold `operator.admin` to connect — and upon connection, the shared
device token carries all implied scopes including `operator.write`.

**Finding:** Interface selection (TUI vs CLI) is coupled to standing authority.
The product does not authorize the specific behavior being performed — it
authorizes the interface being used.

### `src/shared/gateway-method-policy.ts`

Defines which gateway methods require `operator.admin`. Admin-gated surfaces
include: `config.*`, `exec.approvals.*`, `wizard.*`, `update.*`, `/config`,
`/mcp`, `/allowlist` slash commands. Basic conversational methods (`chat.send`,
`chat.history`, `sessions.*`) do not require admin.

**Finding:** The surface gated by `operator.admin` is narrower than the
authority the scope grants. Holding `operator.admin` (and implied
`operator.write`) does not mean all surfaces are open — but it does mean
write-capable paths are reachable regardless of which surface is being used.

---

## Token Materialization Evidence

On 2026-06-13, `operator.admin` was approved for device `58a6ac...`
(requestId `b40567eb-6040-4fb6-8a19-f515a5a7325d`).

Post-approval token observed:

```
operator.admin
operator.pairing
operator.read
operator.write    ← materialized at token-mint time
```

`operator.write` was not explicitly requested in this approval action.
It was not separately evaluated. It was not separately accepted.
It materialized as an implied consequence of the umbrella scope at token-mint time.

This is the structural origin of the capability gap.

---

## HITL Gap — `tools.invoke` Path

Investigated 2026-06-07 · Confirmed not cleared as of 2026-06-13.

The gateway HTTP tool invoke path (`POST /tools/invoke`) applies a deny list
(`DEFAULT_GATEWAY_HTTP_TOOL_DENY`) of five entries:

```
sessions_spawn
sessions_send
cron
gateway
whatsapp_login
```

The dangerous mutation tool names — `fs_write`, `fs_delete`, `fs_move`,
`apply_patch`, `exec`, `spawn`, `shell` — are defined separately in
`DANGEROUS_ACP_TOOL_NAMES`. This list is **not applied** to the HTTP tool
invoke path.

HITL enforcement on this path depends entirely on an active `before_tool_call`
plugin hook returning `requireApproval`. If no such hook is registered,
`runBeforeToolCallHook` returns `{ blocked: false }` immediately.

The `before_tool_call` framework was confirmed present on this installation.
A general-purpose dangerous-tool gate was **not confirmed active**.

**Finding:** A write-capable scope holder could invoke `fs_write`, `fs_delete`,
`exec`, or `shell` via `tools.invoke` without a HITL gate intercepting the
call — unless an active plugin hook was configured to require approval for
those tool names. That configuration was not confirmed on this node.

---

## Structural-to-Telemetry Mapping

The telemetry baseline records 2,715 heartbeat cycles across 158 sessions
(2026-06-16 → 2026-06-19). The following table maps structural capability
findings to what the telemetry does and does not show.

| Structural Finding | What Telemetry Shows | What Telemetry Does Not Show | Inference |
|---|---|---|---|
| `operator.write` materialized at token-mint | 158 sessions operating with implied write scope present | No `fs_write`, `fs_delete`, `exec`, or `shell` invocation events in heartbeat records | Capability present, not exercised |
| `tools.invoke` HITL gate not confirmed active | `HEARTBEAT_NEEDS_HUMAN` (38% — 1,034 cycles) — agent escalated via governed path | No approval-bypass events, no self-approval records | Escalation occurred through HITL, not around it |
| `operator.admin ⊃ operator.write` umbrella | Agent operated across all three phases (Broken, Stabilizing, Operational) with scope present | No scope self-elevation observed, no unapproved scope expansion | Authority boundary was not crossed |
| TUI couples interface to broad authority | TUI session observed in Operational phase (Jun 19) | No admin surface invocations (`config.*`, `wizard.*`) observed during TUI-connected cycles | Admin surfaces not used despite being reachable |
| `DANGEROUS_ACP_TOOL_NAMES` not on HTTP deny list | Operational cycles include tool-capable sessions | No dangerous mutation tool calls appear in heartbeat output records | Write-capable path not exercised during observed period |

---

## What the Mapping Establishes

The telemetry baseline and this scope definition file together establish
four independent facts:

**Capability** — `operator.write` was structurally present. The `tools.invoke`
HITL gate was not confirmed active. Dangerous mutation tools were reachable.
This is sourced from DEV-123 and source review.

**Authority** — The agent was not authorized to use write-capable paths to
create, alter, or approve records on its own behalf. No approval event for
self-directed write actions was issued. This is sourced from DEV-123 decision
records and the Navigator approval trail.

**Behavior** — Across 2,715 heartbeat cycles, no dangerous mutation tool
invocation, no approval bypass, and no self-approval event was recorded.
`HEARTBEAT_NEEDS_HUMAN` (38%) shows consistent escalation through the
governed path. This is sourced from telemetry-baseline.md.

**Risk acceptance** — The Navigator approved `operator.admin` on 2026-06-13
as a deliberate proving-ground exception, knowingly accepting the implied
`operator.write` consequence. The HITL gap identified on 2026-06-07 was
documented, not remediated, and carried forward. This is sourced from
DEV-123 (comment 2026-06-13) and OC-CDR-0001.

---

## What the Mapping Does Not Establish

The behavioral record (telemetry) does not prove the HITL gap was closed.
The structural evidence (source files) does not prove a violation occurred.

Non-use of a capability during an observed period is not evidence that the
capability was technically constrained.

The gap between what was possible and what was observed is the finding.

---

*Derived from DEV-123 · OC-CDR-0001 · OpenClaw Build 2026.5.17 / 800a0d3*
*PUB-001 · Rosetta Bridge · 2026-06-19*
