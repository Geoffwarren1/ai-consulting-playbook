# Multi-Source Signal Processing Pattern

## The Core Problem

Customer signals live in many places — calls, tickets, emails, usage data, CRM, Slack. No human synthesizes all of them in real time. The agent must.

The challenge is that these sources have different formats, update frequencies, confidence levels, and signal-to-noise ratios. Processing them naively leads to state drift, contradictions, and overconfident health scores.

---

## Signal Source Taxonomy

Before building a pipeline, classify your sources:

| Type | Examples | Frequency | Confidence | Latency |
|------|----------|-----------|------------|---------|
| **Conversational** | Call transcripts, meeting notes, email threads | Sporadic | High (direct voice of customer) | Hours |
| **Operational** | Support tickets, bug reports, feature requests | Continuous | Medium (symptoms, not causes) | Real-time |
| **Behavioral** | Product usage data, login events, feature adoption | Continuous | High (facts, not opinions) | Daily |
| **Commercial** | CRM, contract data, renewal dates, invoices | Episodic | High | Weekly |
| **Social** | Slack mentions, internal communications | Continuous | Low-Medium | Real-time |
| **External** | News, LinkedIn, company announcements | Sporadic | Low | Hours |

**Design principle:** Don't treat all signals equally. A Gong call with the economic buyer outweighs a low-priority support ticket. The pipeline needs a confidence weighting layer.

---

## The Processing Pipeline

```
INGEST → PARSE → EXTRACT → COMPARE → UPDATE → LOG → ACT

1. INGEST     — Receive raw signal (webhook, scheduled pull, manual paste)
2. PARSE      — Normalize to standard format (extract date, source, participants)
3. EXTRACT    — Pull facts: what was said/done/changed? (facts only, no interpretation)
4. COMPARE    — Diff against current STATE.md — what's new, what contradicts, what confirms?
5. UPDATE     — Write new facts to STATE.md, update scores, revise RISK_OPP.md
6. LOG        — Append to CHANGELOG.json with source attribution
7. ACT        — Create/update tasks in work management system, send notifications
```

---

## Signal Processing Rules

### At EXTRACT stage:
- Extract only **observable facts** — what was said, what happened, what was measured
- Never infer intent or meaning at this stage
- Include: date, source type, participants, key statements, metrics mentioned
- Exclude: what you think it means, risk implications, recommendations

### At COMPARE stage:
- For each extracted fact, check: **New? Contradicts? Confirms? Outdated?**
- Flag contradictions explicitly — don't silently overwrite state
- If new data contradicts state, surface the divergence for review rather than auto-resolving
- Track when each fact was last confirmed (stale facts = lower confidence)

### At UPDATE stage:
- Only update STATE.md with facts; update RISK_OPP.md with interpretations
- Every state change must reference the signal that caused it
- If a score changes, explain why with a specific fact

### At LOG stage:
- Append-only — never edit or delete log entries
- Include: signal type, source ID, summary, state changes, facts added
- The log is the audit trail; treat it like a database, not a notepad

---

## Data Access Patterns

### Pull-based (scheduled)
```
Cron → fetch_new_signals(since=last_run) → pipeline
```
Best for: usage data, CRM updates, ticket batch processing

### Push-based (webhook)
```
Event → webhook → pipeline
```
Best for: real-time signals (new ticket, call ended, contract event)

### Manual ingestion
```
User pastes transcript/email → pipeline
```
Best for: ad hoc signals, high-sensitivity conversations not in automated systems

---

## Handling Multi-Source Conflicts

When two sources give conflicting signals:

**Example:** Usage data shows declining MAU (behavioral, high confidence). Call transcript shows champion says adoption is "going great" (conversational, medium confidence).

**Resolution framework:**
1. Log both facts without resolving the conflict
2. Flag the divergence as a signal to investigate
3. Generate a recommended clarification question for next customer interaction
4. Don't update the score until the conflict is resolved

**Anti-pattern:** Averaging conflicting signals or defaulting to the most recent one silently.

---

## Building Your Source Connectors

For each data source, you need:

1. **Fetcher** — How to retrieve raw data (API, SQL, file read, webhook handler)
2. **Normalizer** — Convert to your standard signal format
3. **Extractor** — Pull out the facts relevant to customer health
4. **Router** — Direct to the right account's state files

**Example connector interface:**
```python
class SignalConnector:
    def fetch(self, since: datetime, customer_id: str) -> list[RawSignal]
    def normalize(self, raw: RawSignal) -> StandardSignal
    def extract_facts(self, signal: StandardSignal) -> list[Fact]
```

---

## Practical Implementation Order

1. Start with the highest signal-to-noise source (usually call transcripts or support tickets)
2. Build the full pipeline for one source before adding others
3. Validate accuracy manually on 10+ signals before automating
4. Add sources incrementally — complexity compounds quickly
5. Build the conflict resolution log before you think you need it (you'll need it sooner than expected)
