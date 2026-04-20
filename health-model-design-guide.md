# Health Model Design Guide

## The Core Principle: Facts-Only Scoring

A health model is only useful if scores are reproducible and trustworthy. That requires one rule:

**Every score must be backed by a cited, observable fact. No score without evidence.**

If you don't have data for a dimension, the score is unknown — not 5, not 7, not "medium." Unknown. This is a feature, not a bug. Unknown dimensions are signals to go gather data.

---

## Step 1: Choose Your Dimensions

Health is multi-dimensional. A single score averages away the information that matters. Choose 4-7 dimensions that together give a complete picture of customer health in your domain.

**Design criteria for good dimensions:**
- **Independent** — each measures something the others don't capture
- **Observable** — there's actual data you can point to (not vibes)
- **Actionable** — a low score suggests a specific type of intervention
- **Leading, not lagging** — measures risk before it becomes churn, not after

**Common dimension categories for B2B SaaS:**

| Category | What it captures | Example signal |
|----------|-----------------|----------------|
| Technical adoption | How deeply is the product integrated? | # integrations, API usage, advanced feature use |
| Breadth adoption | How widely is the product used? | MAU, # teams, % of licensed seats active |
| Value realization | Is the customer seeing ROI? | Business outcomes cited, exec satisfaction |
| Relationship health | How strong is the champion and exec relationship? | Last exec touch, champion tenure, multi-threading |
| Commercial trajectory | What's the renewal/expansion outlook? | ARR trend, contract terms, budget signals |

**Industry-specific examples:**
- Dev tools → deployment frequency, CI pipeline integration, team onboarding velocity
- HR tech → manager adoption, self-service usage rate, HR admin efficiency metrics
- Data platforms → data sources connected, active users by department, governance policy coverage

---

## Step 2: Define Scoring Anchors

For each dimension, define what 3 / 5 / 7 / 9 look like in concrete, observable terms. This removes ambiguity and makes scores comparable across CSMs and over time.

**Template:**

### [Dimension Name] — Scoring Guide

| Score | Label | Observable Evidence Required |
|-------|-------|------------------------------|
| 9-10 | Excellent | [Specific criteria — e.g., "5+ integrations active, API in production, advanced feature X in use by 3+ teams"] |
| 7-8 | Strong | [Criteria] |
| 5-6 | Developing | [Criteria] |
| 3-4 | At Risk | [Criteria] |
| 1-2 | Critical | [Criteria] |
| — | Unknown | Insufficient data to score |

**Design tip:** Write the anchors before you score any customers. If you write them after, you'll unconsciously write them to match the customers you already have in mind.

---

## Step 3: Define Cross-Dimension Patterns

The most valuable insights come from combinations of dimensions, not individual scores. Document the patterns you discover — these become your model's diagnostic power.

**Pattern template:**

### Pattern: [Name]
- **Signal:** [Dimension A] is [high/low] while [Dimension B] is [high/low]
- **What it means:** [Interpretation]
- **Typical causes:** [2-3 root causes]
- **Recommended intervention:** [Specific action]
- **Example:** [Real or anonymized example]

**Common SaaS patterns to watch for:**

| Pattern | Signal | Meaning |
|---------|--------|---------|
| Adoption plateau | Breadth stable at ~30%, technical depth growing | Product is sticky for power users but hasn't crossed the org |
| Champion dependency | Relationship health high, adoption breadth low | One person driving everything — if they leave, you're at risk |
| Value gap | Technical adoption high, value realization low | Customer is using the product but can't articulate ROI |
| Honeymoon cliff | All dimensions high at month 3, declining at month 9 | Initial excitement fading, hasn't embedded into workflows |
| Expansion-ready | Breadth at ~60%, relationship health high, commercial trending up | Right time to expand to adjacent teams or products |

---

## Step 4: Build Your Evidence Standards

Define the minimum evidence required before any score can be written. This prevents hallucination and score drift.

**Evidence quality levels:**
- **Verified** — Directly stated by customer, confirmed by data
- **Indicated** — Strongly implied by multiple signals
- **Assumed** — Inferred from partial information (flag as low confidence)
- **Unknown** — No data available (do not score)

**Rule:** Only "Verified" and "Indicated" evidence should drive scores. "Assumed" evidence should trigger data-gathering tasks. "Unknown" dimensions should remain unscored.

---

## Step 5: Plan for Score Decay

Scores go stale. A score from 6 months ago may be actively misleading. Build decay into your model.

**Decay rules:**
- Define a freshness TTL for each dimension (e.g., behavioral data: 30 days; relationship: 90 days)
- When a dimension's last-updated date exceeds TTL, flag it as "stale" rather than showing the old score
- Include a "data gaps" section in the state file to track what needs refreshing

---

## Common Mistakes

**Using output metrics as health indicators:** Revenue, NPS, renewal rate are outputs of health, not inputs. By the time these move, it's too late to intervene.

**Averaging dimensions:** An overall score of 6 could mean all dimensions are at 6, or it could mean one is at 2 and another is at 10. Report dimensions independently; use overall score only for portfolio-level sorting.

**Scoring without evidence:** If you write a 7 and can't cite a specific fact, delete the score. Unsubstantiated scores corrupt the model.

**Not updating scores on negative signals:** It's tempting to hold a score stable when a bad signal arrives, hoping it's an outlier. Don't. Update on evidence. If the score recovers, that's a good signal too.
