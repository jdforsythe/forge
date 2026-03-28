# Vocabulary Routing

> Reference for Forge skill design. How precise vocabulary routes LLM attention to expert knowledge clusters, the 15-year practitioner test, sub-domain clustering, and term selection.

---

## Core Mechanism

LLMs organize knowledge in embedding space clusters. Specific technical terms activate specific clusters. The choice of vocabulary in a prompt determines which knowledge regions the model draws from.

**Key insight:** A single precise term can replace paragraphs of explanation. "Bounded context (Evans, DDD)" activates the Domain-Driven Design knowledge cluster more reliably than "separate the code into logical pieces."

---

## The 15-Year Practitioner Test

For every vocabulary term in an agent definition, apply this test:

> Would a senior practitioner with 15+ years of experience use this exact term when speaking with a peer?

- **Pass:** "circuit breaker pattern" — an SRE uses this daily.
- **Fail:** "best practices for error handling" — no senior says this to a peer. It's consultant-speak.
- **Fail:** "stochastic gradient descent with momentum" — unless the agent is an ML researcher. Domain match matters.

Terms that fail this test either activate generic knowledge clusters (consultant-speak) or irrelevant clusters (wrong domain).

---

## Sub-Domain Clustering

Organize vocabulary into 3-5 clusters of 3-8 related terms. Each cluster activates a distinct knowledge sub-domain.

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

1. **3-5 clusters per agent.** Fewer than 3 underspecifies; more than 5 fragments attention.
2. **3-8 terms per cluster.** Enough to define the sub-domain, not so many that signal dilutes.
3. **15-30 total terms per agent.** The practical range for effective vocabulary payloads.
4. **Group by knowledge proximity.** Terms in a cluster should co-occur in expert discourse.

---

## Generic vs Expert Term Comparison

| Generic Term (Avoid) | Expert Term (Use) | Knowledge Cluster Activated |
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

## Attribution Amplifies Routing

Including the originator of a framework strengthens knowledge activation:

| Attribution Level | Example | Activation Strength |
|---|---|---|
| Term only | "INVEST criteria" | Moderate |
| Term + author | "INVEST criteria (Bill Wake)" | Strong |
| Term + author + work | "fitness functions (Ford and Parsons, Building Evolutionary Architectures)" | Strongest |

**When to attribute:**
- Always attribute foundational frameworks: DDD (Evans), SOLID (Martin), 12-Factor (Wiggins)
- Always attribute named patterns: Circuit Breaker (Nygard), Strangler Fig (Fowler)
- Attribution optional for widely-known standards: REST, OWASP, ACID

---

## Anti-Patterns in Vocabulary Selection

### Consultant-Speak
Terms that sound professional but activate generic business writing clusters:
- "best practices," "leverage," "synergy," "paradigm shift," "holistic approach"
- "optimize," "streamline," "robust solution," "scalable framework"
- These are banned in Forge agent definitions.

### Buzzword Stacking
Listing trendy terms without coherence:
- "AI-driven blockchain microservices with DevSecOps and zero trust"
- This creates a scatter-shot activation pattern. No single knowledge cluster dominates.

### Over-Abstraction
Using umbrella terms when specifics exist:
- "clean architecture" → specify: hexagonal (Cockburn), onion (Palermo), or ports-and-adapters?
- "agile methodology" → specify: Scrum, Kanban, XP, or which specific practices?
- "testing strategy" → specify: unit, integration, contract, property-based, mutation?

### Domain Mismatch
Loading vocabulary from a different domain than the task:
- ML vocabulary in a frontend agent definition
- Security terms in a content writing agent
- Each term that doesn't match the task domain wastes attention budget and may activate misleading clusters.

---

## Vocabulary Payload Specification

For Forge agent definitions:

```markdown
## Domain Vocabulary
**[Sub-domain 1]:** term1, term2 (originator), term3, term4
**[Sub-domain 2]:** term5, term6, term7 (framework), term8
**[Sub-domain 3]:** term9, term10, term11, term12
```

**Constraints:**
- 15-30 terms total
- 3-5 clusters
- 3-8 terms per cluster
- Include originator attribution for named frameworks
- Every term passes the 15-year practitioner test
- No consultant-speak, no buzzwords, no over-abstractions

---

*Source: Skill Design Research Synthesis §2.1-2.6*
