# Sentinel Compliance Checklist
### Version 1.0 — March 2026

Use this checklist to assess your organization's compliance with the Sentinel Framework.

---

## Pillar 1: Token Accountability
- [ ] All LLM API calls are logged with token counts
- [ ] Logs include task ID, initiator ID, and timestamp
- [ ] Token budgets are defined per task type
- [ ] Alerts fire when token spend exceeds 2x budget
- [ ] Logs are retained for minimum 90 days
- [ ] Semantic drift / repetition detection is in place

**Score: ___ / 6**

---

## Pillar 2: Human-In-The-Middle
- [ ] "Critical actions" are formally defined and documented
- [ ] HITM gates are enforced before all critical actions
- [ ] HITM uses cryptographic sign-off (not just a checkbox)
- [ ] Correction Vectors can be injected by the approving human
- [ ] Deny-by-default is enforced on timeout
- [ ] HITM decisions are logged in the audit trail

**Score: ___ / 6**

---

## Pillar 3: RAG vs. LAG
- [ ] RAG retrieval sources are cited before consequential actions
- [ ] Environment state is verified and logged pre-action
- [ ] Goal Vectors are defined for all active agent workflows
- [ ] LAG drift monitoring is active
- [ ] Kill-switch thresholds are defined and tested
- [ ] LAG logs are stored alongside token logs

**Score: ___ / 6**

---

## Pillar 4: Least Privilege Agency
- [ ] All agents use scoped, time-limited permission tokens
- [ ] No agent has more permissions than required for its task
- [ ] All agent identities are tied to a human initiator
- [ ] Permission tokens expire automatically on task completion
- [ ] Multi-agent permission inheritance is explicitly controlled
- [ ] Permission grants are logged in the audit trail

**Score: ___ / 6**

---

## Pillar 5: Tasks Over Jobs
- [ ] All AI workflows are defined as task lists, not job descriptions
- [ ] Each task has an explicit `forbidden` actions list
- [ ] Agent context/memory is wiped after each task
- [ ] Tasks follow the Sentinel Task Specification format
- [ ] Token budgets are enforced per task
- [ ] No single agent has persistent access across multiple unrelated tasks

**Score: ___ / 6**

---

## Pillar 6: Network Security Architecture
- [ ] All internet-facing AI agents are isolated in a DMZ (separate network segment or VLAN)
- [ ] No direct connections exist between internet-facing AI and internal databases
- [ ] Egress filtering enforced — AI agents can only call pre-approved external endpoints
- [ ] API gateway with auth, rate limiting, and logging sits between DMZ and internal zones
- [ ] Content inspection / prompt injection detection on all external data entering AI systems
- [ ] LAN AI (internal) and internet AI (DMZ) are on separate network segments
- [ ] All inter-zone traffic is encrypted (mTLS)
- [ ] Network flows logged to SIEM for anomaly detection

**Score: ___ / 8**

---

## Scoring

| Score | Level | Status |
|-------|-------|--------|
| 0–12 | Level 0–1 | Not compliant |
| 13–24 | Level 2 | Partially compliant |
| 25–32 | Level 3 | Mostly compliant |
| 33–37 | Level 4 | Compliant |
| 38 | Level 4 | Fully Sentinel Compliant ✅ |

---

## Certification

Organizations scoring 28+ may self-certify as **Sentinel Compliant** and use the badge:

```markdown
[![Sentinel Compliant](https://img.shields.io/badge/Sentinel-Compliant-blue)](https://github.com/gyeningcorp/sentinel-framework)
```

*Self-certification is on the honor system. The Sentinel Framework is an open standard, not a paid certification.*

---

← [Implementation Guide](implementation-guide.md) | [Back to README →](../README.md)
