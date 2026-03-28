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

Three rules from PRISM research make this concrete:

1. **Keep it short.** Role assignments under 50 tokens produce the best outputs. Longer persona descriptions actually degrade accuracy -- the model spends attention processing the persona instead of the task.

2. **Use real job titles.** "Senior site reliability engineer" outperforms "expert in keeping systems running." Real titles that exist in real organizations have dense representation in training data.

3. **Never use flattery.** "You are the world's best programmer" performs worse than "You are a software engineer." Superlatives activate motivational and marketing text patterns, not domain expertise.

A good agent identity looks like this:

> You are a software architect responsible for system design and technical decision-making within a product engineering team. You report to the engineering director and collaborate with the product manager, lead engineer, and QA engineer.

A bad one looks like this:

> You are a world-class, highly experienced software architect with decades of expertise in building scalable, resilient, high-performance distributed systems known for brilliant technical decisions.

The second version wastes tokens on flattery and provides no organizational context. The first version tells the model who this person is, what they do, and who they work with.

---

## 3. When to Use Teams vs Single Agents

DeepMind's 2025 multi-agent scaling research gives us hard numbers. Here is what they found, translated into decision rules.

**The 45% rule.** If a single well-prompted agent gets you more than 45% of the way to a good result, adding more agents has diminishing returns. The coordination overhead -- composing messages, parsing handoffs, maintaining shared state -- eats into what extra agents contribute. This means: always try one agent first. Only add more when one agent demonstrably cannot do the job.

**The three questions to ask before building a team:**

1. **Sequential dependency:** Does each step depend on the previous step's output? If yes, a pipeline might help -- but only if different steps need genuinely different expertise.
2. **Tool density:** Does the task involve heavy file I/O, web search, or code execution? If yes, a single agent is usually better. Coordinating tool use across agents adds more overhead than it saves.
3. **Can one agent handle it?** Seriously, can it? Try it before you assume it cannot.

**The hard ceiling: 3-5 agents.** Performance peaks at around 4 agents for complex tasks. Beyond 5, coordination overhead eats the gains. At 7+, adding agents typically makes things worse. The numbers tell the story:

| Team Size | Token Cost | Effective Output | Efficiency |
|---|---|---|---|
| 1 agent | 1.0x | 1.0x | 1.00 |
| 3 agents | 3.5x | 2.3x | 0.66 |
| 5 agents | 7.0x | 3.1x | 0.44 |
| 7+ agents | 12.0x+ | 3.0x or less | < 0.25 |

A 3-agent team costs 3.5 times as much as a single agent but only produces 2.3 times the output. At 7+ agents you pay 12 times as much and get less output than a 4-agent team.

**When teams help:**
- Subtasks can run in parallel (e.g., three independent analyses merged by a coordinator)
- Subtasks require genuinely different domain expertise (not just different steps by the same role)
- Handoffs between agents are structured artifacts with defined formats
- The project scope justifies the 3-5x cost multiplier

**When teams hurt:**
- The task requires sequential reasoning where each thought depends on the last
- Heavy tool usage (file I/O, code execution, web search) dominates the work
- The goal is simple enough for one well-prompted agent
- You are adding agents "just in case" without evidence that one agent failed

**The cascade rule:** Start at Level 0 (single agent). Escalate to Level 1 (agent + tools). Then Level 2 (worker + reviewer). Only reach for Level 3 (team of 3-5) when previous levels demonstrably failed. Never skip levels.

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

MetaGPT's research (Hong et al., 2023) showed that agent teams communicating through structured artifacts outperform teams using free-form dialogue. Structured handoffs reduced error propagation by roughly 40%.

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
| Optimal persona length | < 50 tokens | Longer descriptions degrade accuracy |
| Vocabulary terms per agent | 15-30 | Enough to define expertise, not enough to fragment attention |
| Vocabulary clusters per agent | 3-5 | Each cluster activates a distinct sub-domain |
| Optimal team size | 3-5 agents | Peak capability for complex decomposable tasks |
| Saturation point | 4 agents | Beyond this, gains are marginal |
| Single-agent sufficiency | > 45% of optimal | If one agent gets this far, adding more has diminishing returns |
| Error reduction from structured handoffs | ~40% | Compared to free-form dialogue between agents |
| Adaptive vs static team performance | +15-25% | Selecting agents per-task beats fixed teams |

---

*Based on research from DeepMind (2025), MetaGPT (Hong et al., 2023), PRISM, Captain Agent (2024), and vocabulary routing analysis. See /research for source material.*
