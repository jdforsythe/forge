# Vocabulary Routing

> Reference for Forge skill design. Precise domain vocabulary steers model output toward expert-register, distinctive results — with important bounds. The mechanism, what the evidence supports, the 15-year practitioner test, sub-domain clustering, and term selection.

---

## What the Evidence Supports

**The effect is real for generative output.** Anthropic's frontend-design work ("Improving frontend design through Skills," Nov 2025) diagnoses generic output as *distributional convergence* — safe, universally acceptable choices dominate training data, so unguided sampling lands on them. The fix that worked was a ~400-token skill of targeted design vocabulary plus explicit never-use lists: "Tell Claude to 'avoid Inter and Roboto' or 'use atmospheric backgrounds instead of solid colors,' and results improve immediately." Specific vocabulary unlocks capability the model already has but doesn't express by default.

**Prompt wording is a first-order design lever.** MASS (Zhou et al., ICLR 2026, arXiv:2502.02533) found that in multi-agent system design, *prompt optimization dominates topology choice* — reinforcing vocabulary/prompt quality as the primary lever, ahead of structural complexity.

**But more specificity is not monotonically better.** Schreiter (2025, arXiv:2505.17037) systematically varied terminology specificity across STEM, law, and medicine QA and found an **optimal mid-range**: neither overly generic nor maximally technical wording performs best, consistently across models. Jargon-maximizing is not the goal; *precision at the register experts actually use* is.

**Scope limits (honesty section).**
- The popular mechanistic story — "terms activate knowledge clusters in embedding space" — is a plausible *working hypothesis*, not a published finding. No paper demonstrates term-to-cluster routing for generative LLM output. Treat cluster language in this file as a mental model.
- Vocabulary steers *register, style, and domain framing* of generated output. It is not demonstrated to improve *factual accuracy* — don't lean on vocabulary where verification is the right tool.
- Ranjan (2025, arXiv:2512.06744) is sometimes cited here; it only shows that prompt formatting affects *word-embedding* quality for isolated words. It says nothing about terminology in agent prompts.

---

## The 15-Year Practitioner Test

For every vocabulary term in an agent definition, apply this test:

> Would a senior practitioner with 15+ years of experience use this exact term when speaking with a peer?

- **Pass:** "circuit breaker pattern" — an SRE uses this daily.
- **Fail:** "best practices for error handling" — no senior says this to a peer. It's consultant-speak.
- **Fail:** "stochastic gradient descent with momentum" — unless the agent is an ML researcher. Domain match matters.

This test operationalizes Schreiter's mid-range optimum: peer-register terms are precise without being maximally technical.

---

## Sub-Domain Clustering

> **Forge design standard.** Organize vocabulary into 3-5 clusters of 3-8 related terms (15-30 total). These counts are Forge conventions for coverage without dilution — no study prescribes them.

### Example: Software Architect Agent

**System Design cluster:**
hexagonal architecture (Cockburn), bounded context (Evans, DDD), event-driven architecture, CQRS, domain model

**Decision Making cluster:**
Architecture Decision Record (ADR), fitness functions (Ford/Parsons), trade-off analysis, Cynefin framework (Snowden)

**Quality Attributes cluster:**
-ilities (maintainability, scalability, observability), SLA/SLO/SLI, circuit breaker pattern (Nygard), bulkhead isolation

### Example: Security Engineer Agent

**Threat Assessment cluster:**
threat modeling (Shostack), STRIDE, attack surface analysis, DREAD scoring, kill chain

**Application Security cluster:**
OWASP Top 10, SAST/DAST, dependency scanning, CSP headers, input validation

**Infrastructure Security cluster:**
zero trust architecture, network segmentation, mTLS, secrets management, IAM policy

### Cluster Design Rules

1. **3-5 clusters per agent.** Fewer underspecifies the role; more fragments focus.
2. **3-8 terms per cluster.** Enough to define the sub-domain, not so many that signal dilutes.
3. **15-30 total terms per agent.** The Forge working range.
4. **Group by knowledge proximity.** Terms in a cluster should co-occur in expert discourse.

---

## Generic vs Expert Term Comparison

| Generic Term (Avoid) | Expert Term (Use) | Domain framing established |
|---|---|---|
| "separate concerns" | "bounded context (Evans)" | DDD, microservices, context mapping |
| "handle errors gracefully" | "circuit breaker pattern (Nygard)" | Resilience engineering, Release It! |
| "write good tests" | "mutation testing, property-based testing" | Advanced testing, PIT, QuickCheck |
| "make it scalable" | "horizontal scaling, sharding strategy" | Distributed systems, CAP theorem |
| "good architecture" | "fitness functions (Ford/Parsons)" | Evolutionary architecture, measurability |
| "plan the work" | "story mapping (Patton), INVEST criteria (Wake)" | Agile planning, user-centered decomposition |
| "be secure" | "OWASP Top 10, threat modeling (Shostack)" | Application security, STRIDE |
| "monitor the system" | "observability (Majors), distributed tracing, SLI/SLO" | SRE, modern observability |
| "improve performance" | "profiling, flame graphs, p99 latency" | Performance engineering, systems analysis |
| "document the code" | "ADR, RFC, runbook, API contract (OpenAPI)" | Technical writing, structured documentation |
| "deploy safely" | "canary deployment, feature flags, blue-green" | Release engineering, progressive delivery |
| "manage the project" | "WIP limits, cycle time, cumulative flow diagram" | Lean/Kanban, flow-based management |

---

## Attribution

Including the originator of a framework ("fitness functions (Ford and Parsons)") disambiguates the term and anchors the intended meaning — several named patterns share words with unrelated concepts. This is a precision practice; claims that attribution "amplifies activation strength" are the working hypothesis, not a measured effect.

**When to attribute:**
- Always attribute foundational frameworks: DDD (Evans), SOLID (Martin), 12-Factor (Wiggins)
- Always attribute named patterns: Circuit Breaker (Nygard), Strangler Fig (Fowler)
- Attribution optional for widely-known standards: REST, OWASP, ACID

---

## Anti-Patterns in Vocabulary Selection

### Consultant-Speak
Terms that sound professional but carry no domain precision:
- "best practices," "leverage," "synergy," "paradigm shift," "holistic approach"
- "optimize," "streamline," "robust solution," "scalable framework"
- Banned in Forge agent definitions — they are the distribution center, not a route out of it.

### Buzzword Stacking
Listing trendy terms without coherence:
- "AI-driven blockchain microservices with DevSecOps and zero trust"
- No coherent domain framing; reads as marketing copy and invites marketing-register output.

### Over-Specificity
The inverse failure — Schreiter's result cuts both ways. Maximally technical wording underperforms the expert mid-range:
- "utilize polysemous lexical disambiguation heuristics" → "pick unambiguous names"
- If a peer would simplify the term, simplify it.

### Over-Abstraction
Using umbrella terms when specifics exist:
- "clean architecture" → specify: hexagonal (Cockburn), onion (Palermo), or ports-and-adapters?
- "agile methodology" → specify: Scrum, Kanban, XP, or which specific practices?
- "testing strategy" → specify: unit, integration, contract, property-based, mutation?

### Domain Mismatch
Loading vocabulary from a different domain than the task:
- ML vocabulary in a frontend agent definition; security terms in a content-writing agent.
- Mismatched terms waste context and set the wrong register for the output.

---

## Vocabulary Payload Specification

For Forge agent definitions:

```markdown
## Domain Vocabulary
**[Sub-domain 1]:** term1, term2 (originator), term3, term4
**[Sub-domain 2]:** term5, term6, term7 (framework), term8
**[Sub-domain 3]:** term9, term10, term11, term12
```

**Constraints (Forge design standard):**
- 15-30 terms total, 3-5 clusters, 3-8 terms per cluster
- Include originator attribution for named frameworks
- Every term passes the 15-year practitioner test
- No consultant-speak, no buzzword stacking, no over-abstraction — and no jargon beyond the peer register

---

*Sources: Anthropic, "Improving frontend design through Skills" (Nov 2025); Schreiter (2025), arXiv:2505.17037; Zhou et al., MASS (ICLR 2026), arXiv:2502.02533. Cluster/count conventions are Forge design standards. See docs/research/source-index.md.*
