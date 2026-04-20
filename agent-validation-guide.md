# Agent Validation Guide

## Why This Matters

LLM agents make confident-sounding mistakes. The danger isn't that the agent says "I don't know" — it's that it invents a plausible-sounding answer that's wrong, and the human treats it as fact.

Before trusting an agent in production, you must validate its accuracy systematically. Vibes and a few spot checks aren't enough.

---

## The Validation Framework

### Phase 1: Ground Truth Collection

Before you can measure accuracy, you need a ground truth dataset — real examples where you know the correct answer.

**How to build it:**
1. Pick 5-10 representative accounts
2. For each account, gather all available signals (calls, tickets, usage data, emails)
3. Have a domain expert (not the agent) review all signals and produce:
   - A health score for each dimension with evidence citations
   - A list of active risks with severity and root cause
   - A prioritized action plan
4. This is your ground truth. Store it in a format the agent can't see.

**Time investment:** 2-4 hours per account. Not optional. Skip this and you're flying blind.

---

### Phase 2: Accuracy Testing

Run the agent against the same accounts. Compare its output to ground truth.

**What to measure:**

| Metric | What it captures | How to measure |
|--------|-----------------|----------------|
| Score accuracy | Are health scores within ±1 point? | Dimension-by-dimension comparison |
| Risk identification | Does the agent find the same risks? | F1 score: precision (did it find real risks?) + recall (did it miss any?) |
| Evidence grounding | Does the agent cite real evidence? | Manual audit: can each claim be traced to a source? |
| Hallucination rate | Does the agent invent facts? | Count claims that can't be verified against sources |
| Action relevance | Are recommended actions actually useful? | Expert rating of each recommendation: Critical / Useful / Noise / Harmful |

**Target thresholds before production:**
- Score accuracy: >80% within ±1 point
- Risk recall: >85% (missing risks is more dangerous than false positives)
- Hallucination rate: <5% of factual claims
- Action relevance: >70% rated Critical or Useful

---

### Phase 3: Failure Mode Analysis

When the agent gets something wrong, understand why — not just what.

**Common failure modes:**

| Failure Mode | Symptom | Root Cause |
|--------------|---------|------------|
| Evidence underweighting | Agent misses risk that was clearly signaled | Signal not reaching context, or low-confidence labeling |
| Stale state | Agent reports outdated information as current | State files not updated on schedule |
| Over-confidence | Agent assigns high scores without sufficient evidence | Missing evidence quality requirements in skill instructions |
| Pattern mismatch | Agent misidentifies a known pattern | Pattern library incomplete or poorly described |
| Hallucination | Agent invents customer details | Insufficient grounding instructions, or prompt allows it |
| Interpretation bleed | Facts contaminated with interpretation | State file design didn't enforce fact/interpretation separation |

For each failure mode found, update the relevant skill, not just the state file.

---

### Phase 4: Regression Testing

After any change to skills, state file structure, or prompts — rerun validation on your ground truth set.

**Minimum regression test:**
- 3 accounts from ground truth set
- All 5 output types (scores, risks, opportunities, actions, evidence citations)
- Compare to baseline results from last passing run

Build a simple test harness that outputs a comparison table. The human reviews the diff, not the full output.

---

## Practical Validation Cadence

| Stage | What to validate | Frequency |
|-------|-----------------|-----------|
| Pre-production | Full ground truth suite | Once, before launch |
| Skill changes | Affected accounts + 2 regression accounts | Every skill update |
| Data source changes | Full ground truth suite | Every new source added |
| Monthly | Sample 3 production accounts against CSM judgment | Monthly |
| Red account review | Compare agent risk assessment vs. expert assessment | Every review |

---

## The Feedback Loop

Validation is only valuable if failures improve the agent. Build a correction workflow:

1. CSM flags incorrect agent output (score, risk, recommendation)
2. Correction logged with: what was wrong, what the correct answer is, why the agent erred
3. Agent skill updated to prevent recurrence
4. Correction added to validation set as a regression test
5. Track correction rate over time — declining corrections = improving agent

**Target:** Correction rate should decline measurably over the first 3 months of production use.

---

## Simulation Testing

For testing the signal processing pipeline specifically:

1. Build a library of real signals (sanitized) with known correct outcomes
2. Feed each signal through the pipeline
3. Compare the state change the agent made against the expected state change
4. Track: false positives (state changed when it shouldn't), false negatives (state didn't change when it should)

**Minimum test library:** 50 signals across 5 accounts before production.

---

## What Good Validation Looks Like in Practice

A well-validated agent doesn't get every answer right — it gets the right answers right and fails safely on the wrong ones. Specifically:

- When it's confident, it's usually right
- When it's uncertain, it says so explicitly rather than guessing
- When it's wrong, it's wrong in recoverable ways (flagging a non-risk, not missing a critical risk)
- Failure modes are documented and declining over time
