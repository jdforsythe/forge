# We Read the Research So You Don't Have To: The Science of Building AI Teams

Three findings from recent AI research that should change how you build agent systems — and an open-source toolkit built on top of them.

---

## Three Things That Shouldn't Be True (But Are)

Here are three results from peer-reviewed AI research that run counter to almost everything you see on social media about building AI agents:

1. **Adding more AI agents to a task can make it 39-70% worse.** Not a little worse. Dramatically, measurably worse. DeepMind's 2025 multi-agent scaling study found that on tasks requiring tight step-by-step reasoning, multi-agent systems degraded by 39-70% compared to a single well-prompted agent. The agents spent more tokens coordinating with each other than they contributed in useful work.

2. **A single vocabulary change can shift AI output from blog-post quality to expert quality.** Ranjan et al. (2024) demonstrated that swapping generic language for precise domain terminology — "circuit breaker pattern (Nygard)" instead of "handle errors gracefully" — activates entirely different knowledge regions inside the model. Same model, same task, different words, dramatically different output.

3. **Telling an AI it's "the world's best expert" actually degrades its accuracy.** The PRISM persona framework found that superlatives like "you are the world's best programmer" activate generic aspirational content — motivational writing, marketing copy — rather than domain expertise. A plain "you are a software engineer" consistently outperforms it.

If you're building AI agents and none of this surprises you, you can stop reading. But if you've been stacking agents, writing elaborate persona backstories, or peppering your prompts with "you are the greatest," keep going. The research has some things to tell you.

---

## The Problem: Most People Build AI Agents Wrong

Spend ten minutes on any AI-focused community and you'll see the same patterns repeated as gospel:

**The flattery persona.** "You are the world's most brilliant senior staff engineer with 30 years of experience across every technology stack ever invented. You never make mistakes. Your code is legendary." This feels like it should work. It doesn't. More on why shortly.

**The agent swarm.** "I have 47 agents working together in a massive pipeline." The implicit assumption is that more agents equals more capability, the way more servers equals more throughput. But agents aren't servers. They're language models that need to communicate in natural language, and every handoff leaks information.

**Generic instructions without domain vocabulary.** "Write good, clean, well-tested code that follows best practices." This activates the model's knowledge of what blog posts say about writing code — surface-level advice about meaningful variable names and DRY principles. It does not activate the model's knowledge of what senior engineers actually do.

**Free-form dialogue for coordination.** Agents chatting back and forth in unstructured conversation, hoping they'll figure things out. MetaGPT's research (Hong et al., 2023) showed this approach produces roughly 40% more errors than structured artifact handoffs. Unstructured conversation is ambiguous by nature. Ambiguity between agents compounds at every step.

These aren't edge cases. They're the default approach, and each one is contradicted by published research. Let's walk through what the research actually says.

---

## What the Research Actually Says

### Finding 1: Vocabulary Routing — The Highest-Leverage Intervention

This is the single most important finding for anyone building AI agents, and it's the one least discussed.

Large language models organize knowledge in clusters within their embedding space. When your prompt contains a precise technical term, the model activates the cluster of knowledge surrounding that term. When your prompt contains a generic phrase, it activates a broader, shallower region — the kind of knowledge you'd find in a beginner tutorial rather than a practitioner's handbook.

Here's what that looks like in practice:

| Generic Term (Avoid) | Expert Term (Use) | Knowledge Cluster Activated |
|---|---|---|
| "separate concerns" | "bounded context (Evans)" | DDD, microservices, context mapping |
| "handle errors gracefully" | "circuit breaker pattern (Nygard)" | Resilience engineering, Release It! |
| "write good tests" | "mutation testing, property-based testing" | Advanced testing, PIT, QuickCheck |
| "make it scalable" | "horizontal scaling, sharding strategy" | Distributed systems, CAP theorem |
| "good architecture" | "fitness functions (Ford/Parsons)" | Evolutionary architecture, measurability |
| "plan the work" | "story mapping (Patton), INVEST criteria" | Agile planning, user-centered decomposition |
| "be secure" | "OWASP Top 10, threat modeling (Shostack)" | Application security, STRIDE |
| "monitor the system" | "observability (Majors), distributed tracing, SLI/SLO" | SRE, modern observability |

Notice the pattern. The left column is what a non-practitioner says. The right column is what a 15-year veteran says to a peer. The model has been trained on millions of documents written by those veterans — conference talks, technical books, architecture decision records, incident retrospectives. The expert vocabulary is the key that unlocks that training data.

Attribution amplifies the effect. "INVEST criteria" gives you moderate activation. "INVEST criteria (Bill Wake)" gives you strong activation. "Fitness functions (Ford and Parsons, Building Evolutionary Architectures)" gives you the strongest. Including the originator of a framework points the model more precisely at the right knowledge cluster.

The inverse also holds. Consultant-speak — "leverage," "best practices," "robust solution," "synergy" — activates generic business writing clusters. Buzzword stacking ("AI-driven blockchain microservices with DevSecOps") creates a scatter-shot pattern where no single cluster dominates. Both reliably produce shallow, generic output.

### Finding 2: PRISM Persona Science — Less Is More

The PRISM (Persona Research in Instruction-following and Systematic Measurement) framework studied how persona assignment affects LLM output quality. The findings are counterintuitive if you've been writing elaborate agent backstories.

**Brief identities outperform long ones.** Role assignments under 50 tokens produce the highest-quality outputs. At 100+ tokens, accuracy degrades because the model spends its attention budget processing the persona description instead of the task. Your three-paragraph backstory about the agent's career history is actively hurting performance.

**Real job titles activate real knowledge clusters.** "You are a senior site reliability engineer" activates SRE-related training data — runbooks, incident response procedures, SLO definitions — more effectively than "You are an expert in keeping systems running." Real titles that exist in real organizations have dense representation in the training corpus. Invented titles don't.

**Flattery degrades output.** "You are the world's best programmer" performs worse than "You are a software engineer." This is the alignment-accuracy tradeoff that PRISM identified: stronger personas improve instruction following (the model tries harder to do what you ask) but reduce factual accuracy (the model says what sounds right for the role rather than what is actually correct). A brief, realistic role identity gets high alignment without significant accuracy loss. An elaborate, superlative-laden persona gets extreme alignment with degraded accuracy.

The practical rule: real job title, primary responsibility, organizational context. Under 50 tokens. No superlatives. No flattery. One role per agent — combining titles ("You are a software architect and project manager and QA lead") fragments knowledge activation across multiple clusters.

### Finding 3: DeepMind Scaling Laws — The 45% Rule

DeepMind's 2025 multi-agent scaling study produced the most concrete numbers we have on when teams help and when they hurt.

**Performance saturates at 4 agents.** Three agents offer the best efficiency-to-capability ratio. Four is the peak for complex tasks. At five, gains are marginal. At seven or more, adding agents typically degrades overall performance.

The cost numbers tell the story clearly:

| Team Size | Token Cost | Effective Output | Efficiency |
|---|---|---|---|
| 1 agent | 1.0x | 1.0x | 1.00 |
| 3 agents | 3.5x | 2.3x | 0.66 |
| 5 agents | 7.0x | 3.1x | 0.44 |
| 7+ agents | 12.0x+ | 3.0x or less | <0.25 |

A 5-agent team costs 7x the tokens of a single agent but produces only 3.1x the output. A 7+ agent team costs 12x and often produces *less* output than a 4-agent team.

**The 45% threshold** is the key decision rule. If a single well-prompted agent achieves more than 45% of optimal performance on a task, adding more agents yields diminishing returns. The single agent already covers the accessible portion of the problem. The remaining difficulty lives in coordination-heavy integration work where more agents add more overhead, not more capability.

**The implication is clear:** always try a single agent first. Only add more when the single agent demonstrably fails — not when you assume it will fail.

---

## The "True to Life" Thesis

There's a unifying idea behind all three findings that's worth making explicit.

LLMs were trained on the real internet — millions of real documents written by real professionals doing real work. Product managers wrote PRDs. Architects wrote ADRs. SREs wrote incident retrospectives. Security engineers wrote threat models.

When you assign an agent the role "Product Manager," you're activating the model's knowledge of what product managers actually do, how they actually think, and what artifacts they actually produce. When you assign the role "Synergy Optimization Specialist," you're activating nothing, because that role doesn't exist in the training data. There are no documents written by Synergy Optimization Specialists. There are no conference talks. There are no textbooks.

Real roles come with real artifacts, real workflows, real failure modes, and real vocabulary. The model already knows all of this. Your job isn't to teach the model how to be a product manager — it's to activate the knowledge of product management that's already in there.

This is why vocabulary routing, real-world personas, and structured artifacts all point in the same direction. They're all techniques for activating existing knowledge rather than trying to inject new knowledge through elaborate instructions.

---

## What We Built: Forge

We took these three research pillars and built a system around them. Forge is an open-source toolkit that assembles AI agent teams based on what the research says works.

**Four core skills drive the system:**

- **Mission Planner** decomposes a goal into a team blueprint. It evaluates complexity, checks whether the task needs one agent or a team, selects the right coordination topology (pipeline, parallel, coordinator, or hierarchical), and produces a blueprint with roles, artifact chains, and quality gates.

- **Agent Creator** produces individual agent definitions. Each definition includes a brief real-world role identity (under 50 tokens), 15-30 domain vocabulary terms organized into expert clusters, structured deliverables with schemas, standard operating procedures, and 5-10 named anti-patterns with detection signals.

- **Skill Creator** packages reusable capabilities that agents can invoke — structured workflows for common tasks like code review, architecture evaluation, or threat modeling.

- **Librarian** manages the agent and template library. It indexes what's available, helps you find agents and templates that match your goal, and tracks usage patterns.

**A starter library ships with 11 domain agents and 3 team templates** across software development (Product Manager, Architect, Lead Engineer, QA), marketing (Campaign Strategist, Content Creator, Designer, Analytics Lead), and security (Lead Auditor, Penetration Tester, Compliance Analyst). Every agent definition in the library follows the research principles: real titles, expert vocabulary, structured artifacts, named failure modes.

**Every design decision traces to research.** The vocabulary payloads trace to Ranjan et al. The persona format traces to PRISM. The team size limits trace to DeepMind. The structured artifact handoffs trace to MetaGPT. The anti-pattern lists trace to the MAST failure taxonomy. This isn't a framework based on vibes. The `research/` directory in the repository contains the full synthesis of every source.

---

## From Goal to Working Team in Minutes

Here's what it looks like in practice.

You open Claude Code or Cowork and say: *"Build me a SaaS analytics product."*

**Mission Planner activates.** It evaluates the goal's complexity, identifies that this maps to the SaaS product development template, and adapts the template to analytics-specific concerns. It determines this needs a team (the scope exceeds what a single agent can handle) and selects a sequential pipeline topology — requirements flow into design, design flows into implementation, implementation flows into testing.

**It presents a blueprint.** Four roles: Product Manager, Software Architect, Lead Engineer, QA Engineer. An artifact chain: PRD flows to Architecture Decision Records, which flow to implementation specs, which flow to test plans. Quality gates at each handoff — the Architect can reject a PRD that lacks clear success metrics, QA can reject an implementation that doesn't match the architecture.

**Agent Creator produces the team.** Each agent gets a brief role identity ("You are a Product Manager responsible for defining product requirements and success metrics for a SaaS analytics platform"), expert vocabulary clusters (the PM gets "story mapping (Patton), INVEST criteria, Jobs-to-Be-Done framework, product-market fit, cohort analysis, activation metrics"), structured deliverables (the PM produces a PRD with a defined schema), and named anti-patterns ("feature soup — listing features without a coherent value proposition; detection signal: PRD contains more than 15 features with no prioritization framework").

**You have a working team in minutes.** Not a team you have to prompt-engineer yourself. A team where the prompts are already engineered based on research, the handoffs are structured, the failure modes are named, and the vocabulary is chosen to activate the right expert knowledge.

---

## Open Source, MIT License

Forge is MIT licensed. The entire system — skills, agent library, templates, research synthesis, schemas — is open and free.

**Get started:**
```bash
git clone https://github.com/jdforsythe/meta-skill.git ~/.claude/forge
```

It works with Claude Code out of the box. Point it at a goal and the Mission Planner takes it from there.

**Contributing:** The system is designed to grow. New domain agents (healthcare, finance, education, legal), new team templates, new research findings — all follow documented schemas and contribution guidelines. The `CONTRIBUTING.md` in the repository covers the process. Every contributed agent must follow the research principles: real role title, expert vocabulary with the 15-year practitioner test, structured deliverables, named anti-patterns.

---

## What's Next

Forge ships today with three domains (software, marketing, security) and the infrastructure to add more. Here's where we're headed:

**More domains from the community.** The starter library is a starting point, not a boundary. Healthcare, finance, education, legal, operations — every domain has its own expert vocabulary, its own role structures, its own artifacts. Community contributions will expand the library across these domains.

**More platform integrations.** Forge currently works with Claude Code and Cowork. The agent definition format is platform-agnostic by design — structured markdown with clear schemas. Adapters for other agent frameworks are a natural next step.

**Research tracking.** The field moves fast. New findings on multi-agent coordination, context engineering, and prompt design appear regularly. The research directory will continue to be updated as new results emerge, and the methodology will evolve with the evidence.

**Better templates.** The three starter templates are deliberately simple. As real teams use Forge on real projects, we'll learn which patterns work best and codify them into more sophisticated templates with more nuanced topology selection and role configuration.

---

## The Takeaway

The gap between how most people build AI agents and what the research says works is enormous. The fixes are not complicated:

- Use expert vocabulary, not generic instructions. One precise term outperforms a paragraph of explanation.
- Use real job titles in brief role statements, not flattery-laden backstories. Under 50 tokens. No superlatives.
- Start with one agent. Add more only when one demonstrably fails. Cap at 3-5.
- Use structured artifacts for handoffs, not free-form conversation. Define schemas. Build quality gates.
- Name the failure modes. If you don't tell the model what bad looks like, it will gravitate toward the most average output in its training data.

These aren't opinions. They're findings from DeepMind, PRISM, MetaGPT, and the broader prompt engineering research literature. Forge packages them into a system you can use today.

The research is there. The toolkit is open source. The agents are waiting to be built properly.

[GitHub: jdforsythe/meta-skill](https://github.com/jdforsythe/meta-skill) | MIT License | Contributions welcome
