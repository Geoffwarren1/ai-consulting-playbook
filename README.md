# Agent Operations Playbook

How to design, govern, and validate AI agents that run real operational work, not just assist with it.

I built these patterns while designing and shipping an autonomous agent system for a Customer Success team at an enterprise data company. The agent pulled signals from calls, tickets, usage data, and email, kept a running picture of every account, and turned that into prioritized work for the team. This repo is the operating model behind it, written up so it can be reused in any function.

**Author:** Geoff Warren

---

## The core idea

Most AI rollouts stop at "humans do the work, AI assists." That makes individuals a little faster. It doesn't change how a company scales.

The pattern here flips it: **agents run the repeatable operational work, and people handle judgment, relationships, and exceptions.** Throughput grows without headcount growing at the same rate.

That only works if three things are true:

- **Context is persistent.** The agent keeps its knowledge in plain, version-controlled files, not in someone's head or a single chat window.
- **Every action is auditable.** Every change links back to the signal that caused it, so you can replay and correct it.
- **Accuracy is measured before it's trusted.** Agents are tested against real answers before they go live, and tested again after every change.

---

## What's in this repo

| Document | What it covers |
|----------|---------------|
| [autonomous-cs-agent-pattern.md](autonomous-cs-agent-pattern.md) | The core architecture: the Sense → Interpret → Plan → Act pipeline, the four operating modes, and when a human steps in |
| [state-file-template/](state-file-template/) | The context layer: templates for STATE, RISK_OPP, DESTINATION, and CHANGELOG files that give the agent memory and an audit trail |
| [signal-pipeline-pattern.md](signal-pipeline-pattern.md) | How to feed signals from many sources into one processing flow, including what to do when sources disagree |
| [skill-design-pattern.md](skill-design-pattern.md) | How to split agent knowledge into three layers (general, company, and account) so it stays reusable |
| [health-model-design-guide.md](health-model-design-guide.md) | How to build evidence-backed scoring, where every score traces back to a fact |
| [agent-validation-guide.md](agent-validation-guide.md) | How to measure accuracy before and after launch: ground-truth sets, accuracy scoring, failure analysis, and regression testing |

---

## Beyond Customer Success

The first build was for Customer Success, but nothing in the architecture is CS-specific. Any function with lots of signals, repeatable decisions, and a need for traceability fits the same pattern:

| Function | Sense (signals) | Interpret | Plan / Act |
|----------|----------------|-----------|------------|
| **Operations** | Orders, bookings, vendor updates, exceptions | What changed, and what's at risk of a service failure? | Route exceptions, flag capacity gaps, draft vendor outreach |
| **Sales** | Calls, email, CRM activity, intent data | Deal health, stalled deals, expansion signals | Next-best actions, follow-up drafts, pipeline alerts |
| **Finance** | Invoices, contracts, usage, payment data | Billing gaps, collections risk, forecast changes | Queue reconciliations, flag anomalies, prep variance notes |
| **Support** | Tickets, chat, product telemetry | Recurring issues, escalation risk | Triage, draft responses, surface issues for product teams |

The rollout approach is the same in each function:

1. **Pick use cases by outcome, not by excitement.** Start where the signal volume is already more than people can process and the result can be measured.
2. **Build the context layer first.** Before any agent can be accurate, it needs a governed source of truth.
3. **Validate, then automate.** Build a ground-truth set with the people who know the domain, measure the agent against it, and only then let it act on its own.
4. **Keep humans on judgment.** Define up front which decisions go to a person, and review outputs rather than redoing inputs.
5. **Each launch should make the next one faster.** Shared skills, templates, and validation sets mean use case #5 costs much less than use case #1.

---

## How to use this

These are frameworks, not code to copy and paste. A typical sequence:

1. Start with the **agent pattern** to agree on the operating model and where humans step in.
2. Map existing data sources with the **signal pipeline** pattern.
3. Set up the **state files** as the context and audit layer.
4. Design the **skill layer**: what's general, what's company-specific, and what's specific to each account or case.
5. Define scoring with the **health model** guide, where it applies.
6. Run the **validation guide** before anything goes to production, and keep running it afterward.

---

## Note on sources

These are general patterns and methods. They contain no proprietary data, customer information, or source code from any employer.
