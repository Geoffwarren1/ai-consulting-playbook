# Skill Layer Architecture for Claude Code Agents

## The Core Problem

A domain expert agent needs knowledge at multiple levels:
- **Generic** — reasoning frameworks that apply everywhere
- **Company-specific** — your product, your team, your pricing, your processes
- **Customer-specific** — this account's context, history, and state

Stuffing all of this into one massive prompt fails: it's too long, too expensive, impossible to maintain, and the model can't prioritize what matters for the current task.

The solution: **stratified skills** that load only what the current task requires.

---

## The Three-Layer Stack

```
┌─────────────────────────────────────────────────────┐
│  LAYER 3: Account Context Skills                    │
│  What we know about THIS customer                   │
│  (State files, history, relationships)              │
│  Changes: every signal                              │
├─────────────────────────────────────────────────────┤
│  LAYER 2: Company-Specific Skills                   │
│  What we know about OUR company                     │
│  (Product, team, pricing, processes)                │
│  Changes: quarterly                                 │
├─────────────────────────────────────────────────────┤
│  LAYER 1: Foundation Skills                         │
│  Generic expert knowledge                           │
│  (Domain frameworks, reasoning patterns, playbooks) │
│  Changes: rarely                                    │
└─────────────────────────────────────────────────────┘
```

**Key insight:** Layer 1 makes the agent smart. Layer 2 makes it company-aware. Layer 3 makes it customer-specific. You always need 1. You sometimes need 2. You rarely need all 3 at once.

---

## Layer 1: Foundation Skills

These encode expert knowledge that doesn't change with your company or your customers.

**Examples for a CS agent:**
- `cs-foundations` — Customer journey stages, escalation models, health frameworks, industry benchmarks
- `risk-framework` — How to identify, classify, and prioritize risks
- `account-planning` — How to build account plans, structure workstreams, set timelines
- `industry-persona` — Buyer personas, decision-making patterns, industry dynamics

**Design principles:**
- Write these as reasoning guides, not scripts
- Include "when to use this reasoning" sections
- Include anti-patterns — what NOT to do
- Keep them evergreen; don't reference your specific product

---

## Layer 2: Company-Specific Skills

These encode knowledge about your company that the agent needs to reason correctly.

**Examples for a CS agent:**
- `product` — Feature inventory, capability descriptions, what each feature enables for customers
- `team-context` — Org chart, who owns what, escalation paths, working agreements
- `pricing` — Renewal mechanics, expansion triggers, commercial constructs
- `processes` — Internal workflows, review cadences, handoff protocols

**Design principles:**
- Update these when your company changes, not every session
- These are reference material, not instructions — the agent reasons with them
- Keep them factual and structured (tables, lists) for fast retrieval
- Flag internal-only information appropriately

---

## Layer 3: Account Context Skills

These load the current state for a specific customer.

**In practice:** These are usually not "skills" in the traditional sense — they're the state files (STATE.md, RISK_OPP.md, DESTINATION.md) loaded into context for the session.

**Key discipline:** These files must be readable by the agent, not just humans. Use consistent structure, clear section headers, and explicit labels (dates, sources, confidence levels).

---

## The Orchestrator Pattern

Every agent needs an entry point that routes tasks to the right skill combination.

```
User request
    ↓
Orchestrator
    ↓ determines: mode, customer, task type
    ↓ loads: relevant Layer 1 + 2 skills, Layer 3 state
    ↓ dispatches to: specialized skill
Specialized skill executes
    ↓
Output + state updates
```

**Orchestrator responsibilities:**
1. Parse the request (what mode? which customer? what's needed?)
2. Load the minimum necessary skills — don't load everything
3. Dispatch to the right specialist skill
4. Validate output before writing to state files

---

## Skill File Structure (Claude Code)

```
.claude/
└── skills/
    ├── orchestrator/           # Entry point
    │   └── skill.md
    ├── foundation/
    │   ├── cs-foundations/     # Layer 1
    │   │   └── skill.md
    │   └── risk-framework/
    │       └── skill.md
    ├── company/
    │   ├── product/            # Layer 2
    │   │   └── skill.md
    │   └── team-context/
    │       └── skill.md
    └── specialized/
        ├── signal-processor/   # Task-specific
        │   └── skill.md
        └── account-planning/
            └── skill.md
```

---

## Skill Design Checklist

For each skill, define:
- [ ] **Purpose** — one sentence: what does this skill enable?
- [ ] **When to invoke** — specific triggers (mode, task type, signal type)
- [ ] **What it needs** — required context (state files, other skills)
- [ ] **What it produces** — output format and where it goes
- [ ] **Constraints** — what it must NOT do (e.g., "never update state files directly")
- [ ] **Anti-patterns** — common failure modes to avoid

---

## Common Mistakes

**Over-stuffing Layer 1:** Adding company-specific content to foundation skills makes them brittle and hard to reuse.

**Under-specifying orchestration:** If the orchestrator doesn't have clear routing logic, the agent becomes unpredictable.

**Missing the separation of facts from interpretation:** Foundation skills should teach the agent HOW to interpret. State files provide the WHAT to interpret. Never merge them.

**Too many skills for simple tasks:** If every STATUS query loads 12 skills, you're burning context and cost. Build lightweight fast-paths for common queries.
