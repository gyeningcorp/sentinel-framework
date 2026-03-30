# Pillar 2: Human-In-The-Middle (HITM)
### Active Intervention, Not Passive Approval

---

## The Problem

"Human-in-the-loop" is a passive concept. It means a human reviews what the AI did — after the fact, or at the very end. That's not governance. That's a signature on a document you didn't write.

We need **Human-In-The-Middle (HITM)**: humans who intercept, correct, and steer the AI during the inference process — before high-stakes actions are taken.

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
Traditional HITL:
[AI generates] → [AI acts] → [Human reviews]
                               ↑ Too late.

HITM:
[AI generates] → [Human intercepts] → [Human corrects] → [AI acts]
                        ↑ Right here. This is the moment.
```

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

← [Token Accountability](01-token-accountability.md) | [Next: RAG vs. LAG →](03-rag-lag.md)
