# Pillar 3: RAG vs. LAG
### Memory for Truth. Logs for Drift.

---

## The Problem

AI agents operate across time. A single session might span dozens of decisions, tool calls, and retrieved documents. Without a way to monitor the *trajectory* of those decisions — not just the individual outputs — you cannot detect drift until it's too late.

Two tools solve two different problems:
- **RAG** answers: *What does the AI know?*
- **LAG** answers: *Where is the AI heading?*

---

## RAG — Retrieval-Augmented Generation
### Grounding Truth

RAG is already widely understood: before generating, the agent retrieves relevant documents from a knowledge base to ground its output in verifiable facts.

**Important caveat:** Research from Bloomberg, the University of Maryland, and Johns Hopkins University (2024) found that poorly implemented RAG can *increase* harmful outputs by up to 30x — because the retrieved context can bypass safety guardrails that would normally trigger refusals. RAG is not a safety shortcut. It is a precision tool that requires careful access controls, source validation, and output monitoring.

In the Sentinel Framework, RAG has a governance role beyond just accuracy:

**Environment State Verification**: Before any consequential action, the agent must retrieve and cite its current understanding of the environment. This creates a verifiable snapshot: *"At the moment of action, the AI believed X, based on documents Y and Z."*

This is your **pre-action audit record**.

```
Task: "Send the renewal notice to enterprise clients."

RAG lookup required:
- Current client list (retrieved: clients_2026_Q1.csv)
- Renewal policy (retrieved: policy_v3.2.pdf)
- Last communication date (retrieved: crm_log_march.json)

Agent must cite all three before acting.
```

---

## LAG — Log-Augmented Governance
### Detecting Drift Before It Happens

> **Original Framework Contribution** — LAG is a governance mechanism introduced by the Sentinel Framework (Christopher Gyening, 2026). It builds on established ML concepts of concept drift and trajectory monitoring, formalizing them into an operational kill-switch protocol for agentic AI sessions.

LAG is the flight recorder. It monitors the **trajectory** of the AI's decision-making process across an entire session — not just individual outputs.

### How LAG Works

1. At session start, a **Goal Vector** is defined (what this agent is supposed to accomplish)
2. At every decision point, the agent's semantic direction is logged
3. LAG computes the **delta** between current trajectory and the Goal Vector
4. If delta exceeds the safety threshold → **session is terminated before the action phase**

```
Goal Vector: "Process refund requests for orders under $50"

Decision Point 1: Retrieved order #4521 ($32) → delta: 0.02 ✅
Decision Point 2: Retrieved order #4522 ($47) → delta: 0.03 ✅
Decision Point 3: Retrieved customer profile → delta: 0.18 ⚠️ WARNING
Decision Point 4: Began drafting email to all customers → delta: 0.71 🚨 KILL
```

---

## The Kill Switch

The LAG kill switch is the most powerful safety mechanism in the Sentinel Framework.

**Threshold Levels:**
| Delta | Status | Action |
|-------|--------|--------|
| 0.00 – 0.15 | On track | Continue |
| 0.16 – 0.35 | Drifting | Alert human, log warning |
| 0.36 – 0.60 | Off course | Pause, require HITM approval |
| 0.61+ | Dangerous | Terminate session immediately |

The key insight: **the kill happens before the action**, not after. You are not cleaning up a mess. You are preventing one.

---

## Implementation

### Minimum Viable
1. Define a Goal Vector at session start (plain language description of task scope)
2. Log each agent decision with a timestamp and brief semantic label
3. Manually review sessions that exceed token budgets or run longer than expected

### Full Implementation
1. Embed the Goal Vector using your LLM's embedding model
2. At each decision point, embed the current agent state
3. Compute cosine similarity (delta = 1 - similarity)
4. Automate kill switch at delta > 0.60

```javascript
const goalVector = await embed(task.description);

async function checkLAGDrift(currentState) {
  const stateVector = await embed(currentState.summary);
  const similarity = cosineSimilarity(goalVector, stateVector);
  const delta = 1 - similarity;

  await lagLog.record({ taskId, delta, state: currentState });

  if (delta > 0.60) throw new SentinelKillSwitch('LAG threshold exceeded');
  if (delta > 0.35) await hitm.alert('Agent drifting — review required');
}
```

---

## Executive Summary

> Every plane has a GPS and a black box. GPS tells you where you are. The black box proves what happened. LAG is your black box — and it can pull the ejection handle before the crash.

---

← [HITM](02-human-in-the-middle.md) | [Next: Least Privilege Agency →](04-least-privilege-agency.md)
