# Pillar 2: Human-In-The-Middle (HITM)
### Active Intervention, Not Passive Approval

> **Original Framework Contribution** — HITM is a governance model introduced by the Sentinel Framework (Christopher Gyening, 2026). It extends and supersedes existing HITL/HOTL models with active, mid-inference human steering.

---

## The Problem

The AI safety community has defined two oversight models:

- **HITL (Human-in-the-loop):** Human approves or rejects AI output at defined checkpoints.
- **HOTL (Human-on-the-loop):** Human monitors from above and intervenes by exception.

Both are insufficient for agentic systems. HITL creates bottlenecks. HOTL creates blind spots. Neither gives the human **steering authority** — the ability to redirect the AI mid-course with semantic guidance, not just a yes/no gate.

We need **Human-In-The-Middle (HITM)**: humans who intercept, correct, and steer the AI during the inference process — before high-stakes actions are taken. Not at the end. Not from above. *In the middle.*

---

## The Two Modes

### Mode 1: Gatekeeping
Critical actions require a cryptographic handshake from a verified human before they execute.

**Actions that require HITM Gatekeeping:**
- Any API call to a financial system
- Deleting or moving data
- Sending external communications (email, messages)
- Creating or modifying user accounts
- Accessing credentials or secrets

This is not a checkbox. It is a signed token — a proof that a specific human, at a specific time, authorized a specific action.

### Mode 2: Correction Vectors
The human doesn't just say "Yes" or "No." They provide a **Correction Vector** — a semantic nudge that steers the model's next output toward the intended goal.

Instead of:
> "Approved ✓"

The human says:
> "Approved — but focus on cost reduction, not feature expansion."

That correction is injected into the context window before the agent continues.

---

## Why "In-The-Middle" Matters

```
HITL (Human-in-the-loop):
[AI generates] → [AI acts] → [Human reviews]
                               ↑ Too late.

HOTL (Human-on-the-loop):
[AI generates] → [AI acts] → [AI acts] → [Human notices]
                                            ↑ Way too late.

HITM (Human-in-the-middle) — Sentinel Framework:
[AI generates] → [Human intercepts] → [Human corrects] → [AI acts]
                        ↑ Right here. This is the moment.
```

HITM is not just a tighter HITL. It introduces a third response option — **Approve with Correction Vector** — that makes the human an active co-pilot, not just a gatekeeper.

---

## Implementation

### The HITM Gate Protocol
1. Agent identifies a "critical action" by type (defined in your RBAC policy)
2. Agent pauses and emits a **pending_action** event
3. A human receives a structured notification: `WHAT`, `WHY`, `RISK LEVEL`
4. Human responds with: `APPROVE`, `DENY`, or `APPROVE WITH CORRECTION`
5. If approved with correction: correction text is prepended to agent context
6. Agent proceeds (or is terminated)

### Timeout Policy
- Default: 15-minute human response window
- If no response: action is **denied by default** (fail-safe)
- Emergency override: requires 2-factor auth from senior role

### Code Example
```javascript
async function requireHITM(action, context) {
  const gate = await hitm.createGate({
    actionType: action.type,        // 'send_email', 'delete_record', etc.
    actionPayload: action.payload,
    riskLevel: action.riskLevel,    // LOW / MEDIUM / HIGH / CRITICAL
    agentId: context.agentId,
    taskId: context.taskId,
    timeoutMs: 15 * 60 * 1000,     // 15 minutes
  });

  const decision = await gate.waitForHuman();

  if (decision.status === 'DENIED') throw new Error('HITM: Action denied');
  if (decision.correctionVector) {
    context.injectCorrection(decision.correctionVector);
  }

  return decision.status === 'APPROVED';
}
```

---

## Executive Summary

> Your CFO doesn't approve budgets after the money is spent. Don't approve AI actions after they've been taken. Put the human in the middle — not at the end.

---

---

## The Sentinel Engineer

HITM isn't just a technical protocol. It defines a **new professional role**.

Every organization deploying AI agents at scale will need a **Sentinel Engineer** — the human who sits in the middle.

| Responsibility | Description |
|----------------|-------------|
| Gate monitoring | Reviews pending HITM gates in real time |
| Correction authoring | Writes precise Correction Vectors to steer agent outputs |
| Drift response | Responds to LAG alerts before session termination |
| Compliance ownership | Owns the organization's Sentinel score and audit records |
| Incident response | Investigates and documents HITM denials and kill events |

This role is distinct from "AI safety engineer" (which is research-focused) and "AI ops" (which is infrastructure-focused). The Sentinel Engineer is **operational governance** — the person accountable for what the AI does, in real time, every session.

> *"Every pilot needs a co-pilot. Every AI agent needs a Sentinel Engineer."*

---

← [Token Accountability](01-token-accountability.md) | [Next: RAG vs. LAG →](03-rag-lag.md)
