# AI Consulting Playbook — Autonomous Agent Patterns

A set of reusable frameworks for designing autonomous agents that run operational workflows in enterprise B2B SaaS companies. Built from firsthand experience designing and shipping an autonomous Customer Success agent system.

---

## What's Here

| Document | What it covers |
|----------|---------------|
| [autonomous-cs-agent-pattern.md](autonomous-cs-agent-pattern.md) | The core architecture: Sense→Interpret→Plan→Act pipeline, 4 operating modes, the "agent-led operations" inversion |
| [state-file-template/](state-file-template/) | Generic state file templates (STATE, RISK_OPP, DESTINATION, CHANGELOG) for persistent agent memory |
| [skill-design-pattern.md](skill-design-pattern.md) | How to stratify Claude Code skills into Foundation / Company / Account layers |
| [signal-pipeline-pattern.md](signal-pipeline-pattern.md) | How to wire multi-source signals into a unified processing pipeline |
| [health-model-design-guide.md](health-model-design-guide.md) | How to design a multi-dimensional, evidence-backed health scoring framework |
| [agent-validation-guide.md](agent-validation-guide.md) | How to measure and improve agent accuracy before and after production |

---

## The Core Idea

Traditional enterprise workflows: **humans do the work, AI assists.**

The pattern documented here: **agents do the operational work, humans handle exceptions and relationships.**

This works when:
- The domain has high signal volume that exceeds human processing capacity
- The operational work is repetitive, traceable, and auditable
- Human judgment is needed for relationship, executive, and ethical decisions — not routine state management
- You have access to multi-source data that needs synthesis

---

## How to Use This

These are frameworks, not copy-paste code. For each client engagement:

1. Start with **autonomous-cs-agent-pattern.md** to align on the architecture and operating model
2. Use **health-model-design-guide.md** to design a domain-specific health framework with the client
3. Use **state-file-template/** as a starting point; customize dimensions and fields to the client's domain
4. Use **skill-design-pattern.md** to design the skill layer (what generic knowledge goes in foundations, what's company-specific)
5. Use **signal-pipeline-pattern.md** to map their existing data sources and design the ingestion layer
6. Use **agent-validation-guide.md** to build a validation plan before any production deployment

---

## Important: Legal Note

These frameworks reflect general architectural patterns and methodologies. They do not contain proprietary data, customer information, or source code from any employer or client. Before applying any of these patterns to client work, ensure your engagement contracts permit it.
