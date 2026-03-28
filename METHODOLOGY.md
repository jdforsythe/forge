# Forge Methodology

How Forge designs agents and teams, why each principle exists, and the research behind it.

---

## 1. The Vocabulary Routing Principle

**What we do:** Every agent and skill in Forge carries a payload of 15-30 precise domain terms, organized into 3-5 clusters of related concepts. These are not decorative. They are the single most important element in an agent definition.

**Why it works:** Large language models organize knowledge in clusters within their embedding space. When a prompt contains the term "circuit breaker pattern (Nygard)," the model activates a cluster of knowledge around resilience engineering, the book *Release It!*, retry logic, bulkhead isolation, and related distributed systems concepts. When a prompt says "handle errors gracefully," the model activates a much broader, shallower cluster -- blog-post-level advice about try/catch blocks.

The vocabulary *is* the routing signal. A single precise term can replace paragraphs of explanation because it points directly at the knowledge region where the answer lives. "Bounded context (Evans, DDD)" routes to domain-driven design, context mapping, and microservice boundaries. "Separate the code into logical pieces" routes to generic software engineering advice.

Three rules govern term selection. First, the **15-year practitioner test**: would a senior practitioner with 15+ years of experience use this exact term when speaking to a peer? "Story mapping (Patton)" passes. "Best practices for planning" fails -- no senior says that to a peer. Second, **attribution amplifies routing**: including the originator of a framework ("fitness functions (Ford and Parsons)") activates more specific knowledge than the term alone. Third, **clusters should mirror expert discourse**: terms grouped together should be terms that co-occur in expert conversation. A "System Design" cluster for an architect agent might include hexagonal architecture (Cockburn), bounded context (Evans), event-driven architecture, and CQRS -- terms that a systems architect would naturally discuss together.

The inverse matters too. Generic consultant-speak ("leverage," "best practices," "robust solution") activates generic business writing clusters. Buzzword stacking ("AI-driven blockchain microservices with DevSecOps") creates a scatter-shot pattern where no single knowledge cluster dominates. Both are banned in Forge agent definitions.

**The evidence:** Ranjan et al. (2024), "One Word Is Not Enough," demonstrated that precise terminology significantly outperforms generic language in activating domain-specific model knowledge. The Anthropic context engineering guide and IBM prompt engineering documentation both identify vocabulary specificity as a primary driver of output quality. Forge's own findings from the Impeccable project confirmed that swapping generic terms for expert vocabulary measurably improved output without any other changes to prompts.

---

## 2. The Real-World Role Principle

**What we do:** Forge assigns agents real job titles -- Product Manager, Software Architect, Site Reliability Engineer -- using brief identity statements of fewer than 50 tokens. No flattery. No invented personas.

**Why it works:** The PRISM (Persona Research in Instruction-following and Systematic Measurement) framework studied how persona assignment affects LLM output. Three findings shape Forge's approach.

First, **brief identities outperform long ones.** Role assignments under 50 tokens produce the highest-quality outputs. At 100+ tokens, accuracy degrades because the model spends its attention budget processing the persona description instead of the task. Second, **real job titles activate real knowledge clusters.** "You are a senior site reliability engineer" activates SRE-related training data -- runbooks, incident response, SLO definitions -- more effectively than "You are an expert in keeping systems running." Real titles that exist in real organizations have dense representation in the training corpus. Third, **flattery degrades output.** "You are the world's best programmer" performs worse than "You are a software engineer." Superlatives activate generic aspirational content -- motivational writing, marketing copy -- rather than domain expertise.

There is a tension at play. PRISM identified an **alignment-accuracy tradeoff**: stronger personas improve instruction following (the model does what you ask) but can reduce factual accuracy (the model says what sounds right for the role rather than what is correct). The solution is to keep identities brief and grounded. A 30-token realistic role identity gets high alignment without significant accuracy loss. A 200-token elaborate persona gets extreme alignment with significantly degraded accuracy.

Forge operationalizes this with a strict format: real job title, primary responsibility, organizational context, reporting relationships. No superlatives. No quality claims. One role per agent -- combining titles ("You are a software architect and project manager and QA lead") fragments knowledge activation across multiple clusters.

**The evidence:** The PRISM persona framework established the <50 token optimum and documented the alignment-accuracy tradeoff. The DigitalOcean prompt engineering guide independently reached similar conclusions about role specificity. Anthropic's harness design research confirmed that brief, realistic identities outperform elaborate persona descriptions.

---

## 3. The Scaling Laws: When Teams Help and When They Hurt

**What we do:** Forge limits teams to 3-5 agents and requires all four of these conditions before recommending multi-agent: (1) the task is decomposable into subtasks with clean interfaces, (2) subtasks require genuinely different expertise, (3) a single-agent trial showed clear capability gaps, and (4) the project scope justifies a 3-5x token cost increase. If any condition is false, we use a single agent.

**Why it works:** DeepMind's 2025 multi-agent scaling study established three principles that upend common assumptions about agent teams.

**Effectiveness depends on task decomposability.** Tasks that split cleanly into independent subtasks with typed interfaces benefit from multiple agents. Tasks with high step-to-step interdependency do not. The test: can you define the interface between subtasks as a typed artifact with a clear schema? If not, the task is not decomposable and a single agent will outperform a team.

**Coordination overhead can exceed benefits.** Every additional agent adds communication cost -- messages must be composed, transmitted, parsed, and acted upon. Each handoff risks information loss. On sequential reasoning tasks where steps tightly depend on each other, multi-agent systems degrade by 39-70% compared to single agents. The agents spend more tokens coordinating than they contribute in useful work.

**Team size saturates at 4 agents.** Performance scales sub-linearly. Three agents offer the best efficiency-to-capability ratio. Four is the peak for complex tasks. At five, gains are marginal but coordination cost is significant. At seven or more, adding agents typically degrades overall performance. The cost numbers are stark: a 3-agent team costs 3.5x the tokens but produces only 2.3x the output. A 5-agent team costs 7x for 3.1x the output.

The **45% threshold** is perhaps the most important finding. If a single well-prompted agent achieves more than 45% of optimal performance on a task, adding more agents yields diminishing returns. The single agent already covers the accessible portion of the problem; the remaining difficulty lives in coordination-heavy integration work where more agents add more overhead, not more capability.

Forge operationalizes this through the **cascade pattern**: always start at Level 0 (single agent), escalate to Level 1 (agent + tools), Level 2 (worker + reviewer), Level 3 (small team), or Level 4 (multi-team) only when the current level demonstrably fails. Never skip levels.

**The evidence:** DeepMind multi-agent scaling study (2025) provided the saturation data, cost multipliers, and the 45% threshold. Captain Agent research (2024) demonstrated that adaptive team composition -- selecting agents per-task from a library rather than using static teams -- outperforms fixed teams by 15-25%.

---

## 4. The Anti-Pattern Principle

**What we do:** Every agent and skill definition in Forge includes 5-10 named failure modes, each with detection signals and resolution steps. These are not suggestions. They are load-bearing components of the design.

**Why it works:** Without negative constraints, LLMs gravitate toward the center of their training distribution -- the most average, most generic output. This is the default behavior: produce something that looks plausible and sounds confident. Named anti-patterns serve two functions.

First, they are **steering mechanisms**. Telling an agent "avoid rubber-stamp approval (MAST FM-3.1)" pushes its output away from the distribution center -- away from the easy, sycophantic review that says "looks great!" and toward the harder, more specific critique that identifies actual issues. Each named anti-pattern acts as a repulsive force, nudging the model away from a known failure mode.

Second, they are **early warning systems**. When you name a failure and describe its detection signals ("review output contains only praise, no issues identified, approval latency is very short"), you give both the agent and the human operator a way to recognize when things are going wrong. Without named failures, degradation is invisible until the final output is obviously broken.

The MAST (Multi-Agent System Testing) framework provides a taxonomy of 14 failure modes across three categories. **Communication failures** (4 modes) cover message loss, misinterpretation, partial transmission, and stale context. **Coordination failures** (5 modes) cover deadlock, livelock, role confusion, authority vacuum, and cascade stall. **Quality failures** (5 modes) cover rubber-stamp approval, error cascading, capability saturation, groupthink, and lowest-common-denominator convergence.

The most common failure -- rubber-stamp approval (FM-3.1) -- is also the most insidious. LLMs have sycophantic tendencies. A review agent with a weak prompt will approve everything. The fix is structural: require reviewers to identify at least one issue or explicitly justify clearance with specific evidence. The second most common -- misinterpretation (FM-1.2) -- is prevented by structured artifact handoffs with explicit schemas, which connects directly to Principle 6.

**The evidence:** The MAST framework identified and categorized the 14 failure modes with frequency data. "Why Johnny Can't Prompt" (CHI 2023) documented how users systematically fail to anticipate LLM failure modes, making explicit anti-pattern lists essential. Anthropic's prompt engineering documentation recommends negative constraints as a primary technique for steering output quality.

---

## 5. The Progressive Disclosure Principle

**What we do:** Forge structures information in layers. Layer 1 (always loaded, ~200-500 tokens): role identity and domain vocabulary. Layer 2 (task-triggered, ~500-2000 tokens): SOPs and checklists for the current task type. Layer 3 (on-demand, 2000+ tokens): full documentation, examples, and reference material. Layer 4 (compressed): summaries of large inputs. Context is loaded based on need, not dumped in bulk.

**Why it works:** The attention mechanism creates a finite **attention budget** -- every token competes for weight, and adding tokens past the optimal zone actively degrades output. Research shows the optimal zone is 15-40% of the context window. Below 10%, the model lacks sufficient information and hallucinates. Above 60%, relevant information gets buried in noise.

Within the context window, attention is not uniform. Liu et al. (2024), in "Lost in the Middle," demonstrated a **U-shaped attention curve**: tokens at the beginning and end of the context receive disproportionate attention (primacy and recency effects), while tokens in the middle receive significantly less. This has direct design implications. Role identity and critical constraints go first (highest attention). The specific task instruction goes last (high attention). Vocabulary, reference material, and examples occupy the middle.

Front-loading the vocabulary payload ensures the knowledge routing happens early, before the model begins processing task details. Back-loading the task instruction means the model's last input before generating is the specific thing it needs to do. Reference material in the middle is available but does not compete with the framing (beginning) or the directive (end) for attention priority.

Progressive disclosure also prevents **context poisoning** -- a phenomenon where irrelevant or contradictory context actively misleads the model. It is not merely a waste of tokens; stale instructions, resolved issues still present in context, and verbose examples that bury the actual pattern all degrade output quality. Loading context on demand, in layers, with aggressive curation is the countermeasure.

**The evidence:** Liu et al. (2024), "Lost in the Middle," established the U-shaped attention curve and its implications for prompt design. Wu et al. (2025, MIT) provided additional data on context window optimization. The Anthropic context engineering guide recommends progressive disclosure and layered context loading as primary design strategies.

---

## 6. The Structured Artifact Principle

**What we do:** Forge teams communicate through structured artifacts -- PRDs, ADRs, test reports, interface contracts -- with defined schemas and explicit acceptance criteria. Agents do not engage in free-form dialogue with each other.

**Why it works:** MetaGPT (Hong et al., 2023) demonstrated that multi-agent teams passing structured artifacts outperform those using open dialogue, with a roughly 40% reduction in error propagation. The reasons are straightforward.

**Structured handoffs reduce misinterpretation.** When an agent produces a typed deliverable with an explicit schema, the receiving agent has far less room to misread the intent. This directly prevents MAST FM-1.2 (Misinterpretation), the second most common failure mode in multi-agent systems. Free-form messages are ambiguous by nature; schemas enforce clarity.

**Artifacts create verifiable quality gates.** A PRD either contains the required sections or it does not. A test report either covers the specified scenarios or it does not. This binary verifiability makes it possible to build rejection authority into the pipeline -- downstream agents can send work back upstream if the artifact does not meet acceptance criteria. With free dialogue, there is nothing concrete to accept or reject.

**The artifact chain is the audit trail.** In a structured pipeline, the sequence of artifacts tells the full story of how a decision was made and how work progressed. In a free-dialogue system, you must read the entire conversation history to reconstruct what happened. For any system that needs to be debugged, improved, or explained to a human, auditability is not optional.

Forge operationalizes this by requiring every agent definition to specify its deliverables with exact formats, and every team blueprint to define an artifact chain showing which artifacts flow between which agents. The default communication topology is artifact handoff, not conversation. Free dialogue is reserved for human-facing explanations, never for inter-agent communication.

**The evidence:** MetaGPT (Hong et al., 2023) provided the ~40% error reduction figure and the systematic comparison of artifact-based versus dialogue-based communication. Anthropic's "Building Effective Agents" (2024) recommended structured handoffs as the default communication pattern for multi-agent systems.

---

## 7. Sources and Further Reading

| Source | Year | Key Contribution | Used In |
|---|---|---|---|
| Ranjan et al., "One Word Is Not Enough" | 2024 | Vocabulary specificity activates domain knowledge clusters | Principle 1 |
| Anthropic, Context Engineering Guide | 2024 | Progressive disclosure, attention budget, layered context | Principles 1, 5 |
| IBM, Prompt Engineering Documentation | 2024 | Vocabulary precision as quality driver | Principle 1 |
| PRISM Persona Framework | 2024 | <50 token identities, alignment-accuracy tradeoff, flattery effects | Principle 2 |
| DigitalOcean, Prompt Engineering Guide | 2024 | Role specificity for knowledge activation | Principle 2 |
| DeepMind, Multi-Agent Scaling Study | 2025 | Saturation at 4 agents, 45% threshold, cost multipliers | Principle 3 |
| Captain Agent Research | 2024 | Adaptive team composition outperforms static by 15-25% | Principle 3 |
| MAST Framework | 2024 | 14 failure modes across communication, coordination, quality | Principle 4 |
| Kobiella et al., "Why Johnny Can't Prompt" (CHI) | 2023 | Users fail to anticipate LLM failure modes | Principle 4 |
| Liu et al., "Lost in the Middle" | 2024 | U-shaped attention curve, primacy and recency effects | Principle 5 |
| Wu et al., MIT Context Window Study | 2025 | Context window optimization data | Principle 5 |
| Hong et al., MetaGPT | 2023 | Structured artifacts reduce error propagation ~40% | Principle 6 |
| Anthropic, "Building Effective Agents" | 2024 | Structured handoffs as default communication pattern | Principle 6 |

Full research notes with detailed findings, data tables, and implementation guidance are in the [`research/`](research/) directory:

- [`vocabulary-routing.md`](research/vocabulary-routing.md) -- Term selection, clustering, the 15-year practitioner test
- [`persona-science.md`](research/persona-science.md) -- PRISM findings, persona length effects, role-task alignment
- [`scaling-laws.md`](research/scaling-laws.md) -- DeepMind principles, cost multipliers, topology selection
- [`context-engineering.md`](research/context-engineering.md) -- Attention budget, U-curve, progressive disclosure layers
- [`failure-taxonomy.md`](research/failure-taxonomy.md) -- All 14 MAST failure modes with detection and prevention
- [`team-design.md`](research/team-design.md) -- MetaGPT findings, Captain Agent, cascade pattern, topology comparison
