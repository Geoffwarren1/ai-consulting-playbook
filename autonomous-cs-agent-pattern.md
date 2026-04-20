# The Autonomous CS Agent Design Pattern

## Core Insight: The Inversion

Traditional customer success: **humans lead, AI assists.**
Autonomous CS: **agents lead, humans assist.**

The agent is the default operator. The CSM is the exception handler — engaged when the agent flags a judgment call, an executive relationship moment, or a decision it can't make.

This is the Waymo model applied to enterprise SaaS: the car drives itself, the safety driver intervenes when needed. The goal is to reduce human intervention over time, not eliminate it — but every unnecessary human touch is a delay and a cost.

---

## The 4 Operating Modes

Every autonomous CS agent should support these modes:

### 1. STATUS
- **Trigger:** "What's going on with [customer]?"
- **Action:** Read current state files, synthesize GPS health, active risks, and priority workstreams
- **Output:** Narrative health summary with confidence levels
- **Rule:** Read-only. No data pulls, no file updates.

### 2. NEW SIGNAL
- **Trigger:** A new event arrives — call transcript, support ticket, email, Slack message, renewal notice
- **Action:** Parse signal → extract facts → compare against state → identify what changed → update STATE, RISK_OPP, CHANGELOG → update task tracker
- **Output:** Signal summary + state diff + updated tasks
- **Rule:** Never infer beyond the signal. Distinguish what the signal says from what it means.

### 3. PERIODIC REFRESH
- **Trigger:** Scheduled (weekly/monthly) or manual "do a full sync"
- **Action:** Pull fresh data from all sources → compare against current state → identify divergences → update all state files → re-score GPS → flag new risks
- **Output:** Full state refresh with changelog diff
- **Rule:** Flag divergences, don't paper over them. If state says one thing and data says another, surface it.

### 4. PORTFOLIO REFRESH
- **Trigger:** Weekly autonomous run across all customers
- **Action:** For each customer: pull signals since last refresh → process → update state → generate priority recommendations → create tasks for CSM
- **Output:** Portfolio heat map + prioritized action queue
- **Rule:** Agent completes the full cycle without human input. CSM reviews output, not inputs.

---

## The Sense → Interpret → Plan → Act Pipeline

```
SENSE                    INTERPRET              PLAN                   ACT
─────────────────────    ───────────────────    ──────────────────     ──────────────────
Calls / transcripts  →   What changed?      →   What should happen →   Create tasks
Support tickets      →   Why did it change?      by when?               Send updates
Usage data           →   What's at risk?         Who owns it?           Schedule meetings
Emails / Slack       →   What opportunity         What's the ask?        Update CRM
Renewal data         →   exists?                                         Generate plans
```

**Key discipline:** Keep SENSE (observable facts) strictly separated from INTERPRET (judgment). Facts belong in state files. Interpretations belong in risk/opportunity documents. Never mix them — it destroys traceability.

---

## Why This Architecture Works

1. **State is persistent and auditable.** Every change is logged with the signal that caused it. You can replay history.

2. **Interpretation is revisable.** When the agent is wrong, you update the interpretation layer without touching the facts layer.

3. **Operating modes are composable.** A NEW SIGNAL triggers a mini-INTERPRET cycle. A PORTFOLIO REFRESH chains SENSE → INTERPRET → PLAN → ACT across all accounts.

4. **Human escalation is explicit.** The agent doesn't silently make executive decisions. It flags them with a recommendation and waits.

---

## Implementation Checklist

- [ ] Define the health dimensions for your domain (see health-model-design-guide.md)
- [ ] Design state file structure (see state-file-template/)
- [ ] Map your data sources to SENSE layer
- [ ] Build skill layer for each domain knowledge area (see skill-design-pattern.md)
- [ ] Wire signal processor to state updater (see signal-pipeline-pattern.md)
- [ ] Define escalation triggers — what requires human judgment
- [ ] Build validation framework before production (see agent-validation-guide.md)
