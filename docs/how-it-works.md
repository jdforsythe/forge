# How Forge Works

A practitioner's guide to the ideas behind Forge. No jargon for its own sake -- just the mechanisms that matter and why they matter.

---

## 1. Why Vocabulary Matters

This is the single highest-leverage thing you can do when writing an agent definition.

LLMs store knowledge in clusters. When your prompt contains a specific technical term, the model activates the cluster of knowledge around that term. When your prompt contains a vague term, the model activates... vague knowledge. Blog posts. Beginner tutorials. Marketing copy.

One precise term can replace an entire paragraph of explanation. "Bounded context (Evans, DDD)" pulls in microservices patterns, context mapping, and domain-driven design. "Separate the code into logical pieces" pulls in generic advice you could find on any intro-to-programming site.

Here is what this looks like in practice:

| Generic Term | Expert Term | What Changes in the Output |
|---|---|---|
| "nice colors" | "OKLCH tinted neutrals" | Routes to color science, perceptual uniformity, and modern CSS color spaces instead of "try blue, it's calming" |
| "handle errors gracefully" | "circuit breaker pattern (Nygard)" | Activates resilience engineering -- cascading failure prevention, timeout strategies, fallback logic -- instead of try/catch boilerplate |
| "make it scalable" | "horizontal scaling, sharding strategy" | Pulls in distributed systems knowledge, CAP theorem tradeoffs, and partition strategies instead of "use a load balancer" |
| "plan the work" | "story mapping (Patton), INVEST criteria" | Activates user-centered decomposition, vertical slicing, and testable story writing instead of generic Gantt chart advice |
| "monitor the system" | "observability (Majors), SLI/SLO, distributed tracing" | Routes to modern SRE practices -- structured logging, trace propagation, error budgets -- instead of "set up alerts" |

The pattern is consistent: generic terms route to surface-level advice; expert terms route to the knowledge a senior practitioner actually uses.

**The 15-year practitioner test.** For every term in an agent definition, ask: would a senior practitioner with 15+ years of experience use this exact term when talking to a peer? "Circuit breaker pattern" passes. "Best practices for error handling" fails -- no senior engineer says that to another senior engineer. It is consultant-speak, and it activates consultant-speak clusters in the model.

**Attribution strengthens the signal.** "Fitness functions" activates moderate knowledge. "Fitness functions (Ford and Parsons, Building Evolutionary Architectures)" activates it much more strongly. Include the originator when the term comes from a named framework or book.

**Anti-pattern: buzzword stacking.** "AI-driven blockchain microservices with DevSecOps and zero trust" creates a scatter-shot activation pattern. No single knowledge cluster dominates. The model does not know what you actually want.

---

## 2. Why Real-World Roles Work

LLMs were trained on millions of documents written by real product managers, real architects, and real engineers -- job postings, internal docs, technical blogs, conference talks, books. When you say "you are a senior site reliability engineer," you activate that entire cluster of SRE knowledge: incident response, SLOs, capacity planning, toil reduction. When you say "you are a synergy optimization specialist," you activate nothing useful because that role does not exist in the training data.

Three practices, each grounded differently:

1. **Keep it short.** Forge keeps role identities to roughly 20-50 tokens. Persona research (Hu, Rostami & Thomason 2026, "Expert Personas Improve LLM Alignment but Damage Accuracy") found that knowledge-task accuracy degrades *more* as personas get longer -- but there's no published "optimal token count." The 20-50 token range is a Forge design standard for context economy, not a measured threshold.

2. **Use real job titles.** "Senior site reliability engineer" reads as an unambiguous scope descriptor and maps cleanly to real-world deliverables and decision boundaries. No study shows real titles outperform invented personas on accuracy -- Forge keeps them as a design standard for clarity, not a capability lever.

3. **Never use flattery.** "You are the world's best programmer" spends tokens claiming quality instead of defining behavior. This is a Forge style convention, not a research finding -- no study isolates flattery's effect on accuracy inside personas.

A good agent identity looks like this:

> You are a software architect responsible for system design and technical decision-making within a product engineering team. You report to the engineering director and collaborate with the product manager, lead engineer, and QA engineer.

A bad one looks like this:

> You are a world-class, highly experienced software architect with decades of expertise in building scalable, resilient, high-performance distributed systems known for brilliant technical decisions.

The second version wastes tokens on flattery and provides no organizational context. The first version tells the model who this person is, what they do, and who they work with.

---

## 3. When to Use Teams vs Single Agents

Kim et al., "Towards a Science of Scaling Agent Systems" (arXiv:2512.08296, Google Research/MIT), gives us the best current numbers on multi-agent scaling. Here is what they found, translated into decision rules.

**The ~45% accuracy rule.** Tasks where a single agent already clears roughly 45% accuracy see negative returns from adding more agents (coordination coefficient β = -0.408, p < 0.001). The single agent already covers the accessible part of the problem; extra agents mostly add coordination overhead. This means: always try one agent first. Only add more when one agent demonstrably cannot do the job.

**The three questions to ask before building a team:**

1. **Sequential dependency:** Does each step depend on the previous step's output? If yes, a pipeline might help -- but only if different steps need genuinely different expertise.
2. **Tool density:** Does the task involve heavy file I/O, web search, or code execution? If yes, a single agent is usually better. Coordinating tool use across agents adds more overhead than it saves.
3. **Can one agent handle it?** Seriously, can it? Try it before you assume it cannot.

**Team size: 3-4 recommended, 5 hard cap.** This is a Forge design standard, not a measured universal optimum -- but it is informed by real numbers. Turn count grows superlinearly with team size (T = 2.72·(n+0.5)^1.724, R² = 0.974), and under a fixed compute budget, per-agent reasoning capacity becomes "prohibitively thin beyond 3-4 agents" (Kim et al.). Multi-agent teams also carry a real efficiency penalty -- a single-agent efficiency coefficient of 0.466 versus 0.074-0.234 for multi-agent architectures, a **2-6x efficiency penalty**. For comparison, Anthropic's own orchestrator-worker research system beat single-agent Claude by 90.2% on a breadth-first research eval, but consumed **~15x the tokens** of a single chat interaction (Anthropic, "How We Built Our Multi-Agent Research System," 2025).

**When teams help:**
- Subtasks can run in parallel (e.g., three independent analyses merged by a coordinator)
- Subtasks require genuinely different domain expertise (not just different steps by the same role)
- Handoffs between agents are structured artifacts with defined formats
- The project scope justifies the 2-6x efficiency penalty (or ~15x token spend for research-style orchestration)

**When teams hurt:**
- The task requires sequential reasoning where each thought depends on the last
- Heavy tool usage (file I/O, code execution, web search) dominates the work
- The goal is simple enough for one well-prompted agent
- You are adding agents "just in case" without evidence that one agent failed

**The cascade rule:** Start at Level 0 (single agent). Escalate to Level 1 (agent + tools). Then Level 2 (worker + reviewer). Only reach for Level 3 (team of 3-4, 5 max) when previous levels demonstrably failed. Never skip levels.

---

## 4. How the Library Grows

The Forge library follows a Use-Grow-Trim lifecycle. It is not a static collection -- it evolves as people use it.

**USE.** When a user describes a goal, the Mission Planner searches the library for agents and templates that match. It loads the best fits and logs which items were used, how often, and whether the result was successful. This usage data drives everything else.

**GROW.** When the library does not have what is needed, new items get created just-in-time:
- The **Agent Creator** builds new agent definitions when no existing agent matches the required expertise. It follows the vocabulary routing and persona rules described above.
- The **Skill Creator** builds new skill templates when no existing template matches the task pattern.
- New items enter the library with a "candidate" status. They are not yet trusted the way established items are.

**TRIM.** The **Librarian** periodically reviews the library for:
- **Staleness:** Items that have not been used in a long time. If nobody needs them, they are archived.
- **Duplicates:** Items that overlap significantly with other items. The better-performing one stays; the other is merged or removed.
- **Quality promotion:** Candidate items that have been used successfully multiple times get promoted to full library status. Items that consistently produce poor results get flagged for revision or removal.

The result is a library that gets better over time -- frequently used items get refined, rarely used items get pruned, and gaps get filled as they are discovered.

---

## 5. How Artifact Chains Work

MetaGPT's research (Hong et al., 2023) showed that agent teams communicating through structured artifacts outperform teams using free-form dialogue: executability scored 3.75 vs. 2.25 for dialogue-based ChatDev, with roughly 3x fewer human revisions needed.

The reason is straightforward. When agents talk to each other in free-form text, ambiguity compounds at every step. Agent B misinterprets something Agent A said. Agent C misinterprets Agent B's misinterpretation. By the end of the chain, the output has drifted far from the intent.

Structured artifacts fix this. Each agent produces a deliverable in a defined format. The receiving agent knows exactly what to expect and can validate it before proceeding.

**Example: a SaaS feature development chain.**

```
Product Manager  -->  PRD (requirements doc, defined sections)
     |
Software Architect  -->  Architecture Doc (ADR format, component diagram)
     |
Lead Engineer  -->  Implementation (code following architecture spec)
     |
QA Engineer  -->  Test Results (pass/fail matrix, coverage report)
```

Each arrow is a typed artifact with a known schema. The architect does not read a chat transcript from the PM -- they read a PRD with specific sections (user stories, acceptance criteria, constraints). The engineer does not read a vague description -- they read an architecture document with component boundaries and interface definitions.

**Why this matters in practice:**

| | Free Dialogue | Structured Artifacts |
|---|---|---|
| Error propagation | High -- ambiguity compounds | Low -- schemas enforce clarity |
| Token efficiency | Low -- redundant back-and-forth | High -- one transmission per handoff |
| Auditability | Poor -- buried in conversation | Good -- the artifact chain is the record |
| Scalability | Degrades with team size | Stable -- interfaces are typed |

Every agent definition in Forge includes a Deliverables section specifying the exact artifact format it produces. Every team blueprint includes an Artifact Chain section defining the flow. No free-form dialogue between agents -- only typed deliverables.

---

## 6. The Topology Decision

Different task shapes need different team architectures. Here is how to choose.

**Sequential pipeline.** Each step depends on the previous step's output. Best for tasks with a natural order where each phase transforms the artifact for the next phase.
- Example: Requirements --> Architecture --> Implementation --> Testing
- Use when: there is a clear "assembly line" where each stage adds to the previous one
- Watch out for: bottlenecks at the slowest stage

**Parallel-independent.** Subtasks do not depend on each other. A coordinator dispatches work and merges results.
- Example: Creating marketing content -- blog post, social media copy, email campaign, and landing page copy can all be written at the same time, then a coordinator ensures consistent messaging
- Use when: you need throughput and subtasks are genuinely independent
- Watch out for: the synthesis step can be hard if subtasks drift in different directions

**Centralized coordinator.** One agent manages complex dependencies, dispatching tasks and integrating results.
- Example: Security audit -- the coordinator assigns threat modeling, code review, dependency scanning, and infrastructure review to specialists, then integrates findings into a unified report with prioritized remediations
- Use when: dependencies between subtasks are complex and a single coordinator needs to manage the flow
- Watch out for: the coordinator becomes a bottleneck and single point of failure

**Decision tree:**

```
Is the task simple enough for one agent?
  YES --> Use one agent (Level 0-1)
  NO  --> Do subtasks depend on each other?
            YES --> Are dependencies simple and linear?
                      YES --> Sequential pipeline
                      NO  --> Centralized coordinator
            NO  --> Parallel-independent with synthesis agent
```

When in doubt, start with one agent. The 45% threshold means it will handle more than you expect. Escalate only when you have evidence, not assumptions.

---

## Summary of Key Numbers

| What | Number | Why It Matters |
|---|---|---|
| Role identity length | ~20-50 tokens | Forge convention -- knowledge-task accuracy degrades as personas get longer (PRISM) |
| Vocabulary terms per agent | 15-30 | Enough to define expertise, not enough to fragment attention |
| Vocabulary clusters per agent | 3-5 | Each cluster activates a distinct sub-domain |
| Recommended team size / hard cap | 3-4 / 5 | Forge design standard -- turn count grows superlinearly beyond this (Kim et al.) |
| Single-agent sufficiency | ~45% accuracy | Above this, adding agents shows negative returns (Kim et al.) |
| Structured artifacts vs. dialogue | 3.75 vs 2.25 executability, ~3x fewer revisions | MetaGPT vs. dialogue-based ChatDev (Hong et al. 2023) |
| Adaptive team assembly vs. static baselines | +21.94% | CaptainAgent vs. existing multi-agent baselines (Song et al. 2024) |

---

*Based on research from Kim et al. (2025, arXiv:2512.08296, Google Research/MIT), MetaGPT (Hong et al., 2023), PRISM (Hu, Rostami & Thomason 2026), CaptainAgent (Song et al. 2024), and vocabulary routing analysis. See /research for source material.*
