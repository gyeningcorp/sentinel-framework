# Sentinel Implementation Guide
### From Zero to Compliant in 3 Phases

---

## Phase 1 — Foundation (Weeks 1–4)
*Goal: Know what you have. Stop the worst risks.*

### Week 1–2: AI Audit
- [ ] List every AI tool your organization uses (ChatGPT, Copilot, custom agents, etc.)
- [ ] For each: what data can it access? What actions can it take?
- [ ] Classify each as **Job** (broad access) or **Task** (scoped)
- [ ] Flag anything with access to financial systems, customer data, or external comms

### Week 3–4: Quick Wins
- [ ] Remove unused permissions from all AI tools
- [ ] Add a human approval step before any AI sends an external communication
- [ ] Start logging all AI interactions (even basic logs are better than none)
- [ ] Define your first 5 tasks using the [Task Specification Format](05-tasks-over-jobs.md)

**Exit Criteria**: You can answer "what does each of our AI agents have access to?"

---

## Phase 2 — Governance Layer (Months 2–3)
*Goal: Build the accountability infrastructure.*

### Token Accountability (Pillar 1)
- [ ] Instrument all LLM API calls with input/output token logging
- [ ] Tag logs with task ID and initiator
- [ ] Set token budgets per task type
- [ ] Create dashboard showing token spend by agent, task, and time

### HITM Gates (Pillar 2)
- [ ] Define your "critical action" list (what always requires human approval)
- [ ] Build or integrate a HITM notification system (Slack, email, mobile push)
- [ ] Implement 15-minute timeout with deny-by-default
- [ ] Test with 3 real workflows

### Least Privilege (Pillar 4)
- [ ] Integrate with your IAM provider (Okta, Azure AD, Google Workspace)
- [ ] Create scoped permission sets for each task type
- [ ] Implement automatic token expiration
- [ ] Audit all multi-agent permission inheritance chains

**Exit Criteria**: Every agent action is logged. High-risk actions require human sign-off.

---

## Phase 3 — Sentinel Standard (Months 4–6)
*Goal: Full compliance. Predictive governance.*

### RAG/LAG (Pillar 3)
- [ ] Implement RAG with source citation for all consequential decisions
- [ ] Define Goal Vectors for all active agent workflows
- [ ] Deploy LAG drift monitoring
- [ ] Set kill-switch thresholds and test with simulated drift scenarios

### Tasks Over Jobs (Pillar 5)
- [ ] Complete task decomposition for all remaining "job" workflows
- [ ] Implement stateless agent pattern (context wipe after task)
- [ ] Validate all tasks against the Task Specification schema

### Certification
- [ ] Run the [Sentinel Compliance Checklist](compliance-checklist.md)
- [ ] Document your implementation for internal audit
- [ ] Add the Sentinel Compliant badge to your AI governance documentation

**Exit Criteria**: All five pillars implemented. You can answer any question about any AI decision made in the last 90 days.

---

## Maturity Model

| Level | Name | Description |
|-------|------|-------------|
| 0 | Unmanaged | AI tools deployed with no governance |
| 1 | Aware | AI inventory exists; basic logging in place |
| 2 | Controlled | Token budgets, HITM gates, scoped permissions |
| 3 | Governed | Full RAG/LAG, task decomposition, stateless agents |
| 4 | Sentinel Compliant | All five pillars implemented and auditable |

---

## Common Mistakes

### "We have human review at the end"
That's not HITM. That's a post-mortem. Put the human in the middle.

### "Our AI only reads data, it doesn't act"
Read access to sensitive data is still a risk. A read agent can exfiltrate, summarize for an adversary, or become the reconnaissance phase of a larger attack.

### "We trust our LLM provider's safety filters"
Provider safety filters are not governance. They are a floor, not a ceiling. Sentinel is your ceiling.

### "Task decomposition takes too long"
The first time, yes. After that, you're adding tasks to a library. And you'll spend far less time on incidents.

---

← [Tasks Over Jobs](05-tasks-over-jobs.md) | [Compliance Checklist →](compliance-checklist.md)
