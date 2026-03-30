# Pillar 4: Least Privilege Agency
### AI Gets a Badge, Not a Master Key

---

## The Problem

When organizations deploy AI, they often give it broad access — "just in case it needs it." An AI agent with access to email, files, databases, and APIs is a single prompt injection away from becoming a catastrophic insider threat.

The solution is borrowed from security engineering: **Least Privilege**. Give every agent the minimum access needed to complete its specific task — nothing more, nothing less.

---

## Ephemeral Scoped Roles

Forget "User Accounts" for AI. Sentinel uses **Ephemeral Scoped Roles (ESRs)** — time-limited, task-specific permission certificates.

### ESR Properties
- **Scoped**: Only the exact verbs needed for this task (`read:email`, not `read:*`)
- **Ephemeral**: Expires when the task ends (or after a time limit)
- **Traceable**: Every ESR is tied to a human initiator and a task ID
- **Non-transferable**: The agent cannot grant its permissions to another agent

---

## The Verb Model

Permissions are defined as **verbs**, not roles.

```
❌ Traditional: Role = "Admin"
   → Can read, write, delete, send, create, modify everything

✅ Sentinel ESR: Verbs = ["read:email_inbox", "write:draft"]
   → Can only read the inbox and save drafts. Cannot send.
```

### Common Verb Pairs
| Read | Write | The Line |
|------|-------|----------|
| `read:email` | `send:email` | **Never give send without explicit HITM** |
| `read:database` | `write:database` | **Writes require task justification** |
| `read:files` | `delete:files` | **Delete requires human approval always** |
| `read:calendar` | `create:meeting` | **External-facing actions need HITM** |

---

## Identity Minting

Every agent spin-up generates a **unique, traceable Identity Token**:

```json
{
  "agentId": "agt_7f3k9m2x",
  "initiatedBy": "user_chris_gyening",
  "taskId": "task_summarize_inbox_2026_03_30",
  "permissions": ["read:email_inbox"],
  "issuedAt": "2026-03-30T14:00:00Z",
  "expiresAt": "2026-03-30T14:10:00Z",
  "environment": "production",
  "sentinelVersion": "1.0.0"
}
```

This token is:
- Signed by the initiating human's credentials
- Stored in the audit log before the agent is launched
- Revocable at any time by the human initiator or a senior role

---

## Multi-Agent Trust

When Agent A needs to spawn Agent B, the trust chain must be explicit:

```
Human (root trust)
  └── Agent A [ESR: read:crm, read:email]
        └── Agent B [ESR: read:crm ONLY]
              ↑ Agent B cannot inherit Agent A's email permissions
              ↑ Agent B cannot escalate its own permissions
              ↑ Each spawn requires a new human-signed ESR
```

**No agent can have more permissions than its parent.**  
**No agent can grant permissions it doesn't have.**

---

## Implementation

### Minimum Viable
1. Inventory every AI agent and what it currently has access to
2. Remove all access that isn't required for its current task
3. Implement time-based session tokens (auto-expire after task)

### Full Implementation
1. Build an ESR issuance service (integrate with your existing IAM: Okta, Azure AD, etc.)
2. Sign every agent session with the initiating human's identity
3. Implement permission inheritance rules for multi-agent chains
4. Audit every permission grant in your token accountability log (Pillar 1)

```javascript
async function mintAgentIdentity(task, initiator, permissions) {
  const esr = {
    agentId: `agt_${uuid()}`,
    initiatedBy: initiator.id,
    taskId: task.id,
    permissions: permissions, // explicit list only
    issuedAt: new Date().toISOString(),
    expiresAt: new Date(Date.now() + task.estimatedDurationMs).toISOString(),
  };

  // Verify no permission exceeds initiator's own access level
  for (const perm of permissions) {
    if (!initiator.permissions.includes(perm)) {
      throw new Error(`Cannot grant ${perm}: initiator lacks this permission`);
    }
  }

  await auditLog.write({ event: 'ESR_ISSUED', esr });
  return sign(esr, initiator.privateKey);
}
```

---

## Executive Summary

> Contractors get key cards — not the building access code. Your AI is a contractor. Define exactly which doors it can open, for how long, and make sure it can't copy the key.

---

← [RAG vs. LAG](03-rag-lag.md) | [Next: Tasks Over Jobs →](05-tasks-over-jobs.md)
