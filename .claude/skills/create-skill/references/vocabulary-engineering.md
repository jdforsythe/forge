# Vocabulary Engineering — Building Expert Vocabulary Payloads

This reference contains the complete method for constructing vocabulary payloads. Load
this at Step 5 (Author Vocabulary Payload) of the skill creation workflow.

---

## Why Vocabulary Is the Highest-Leverage Intervention

The vocabulary in a skill determines which region of the model's knowledge gets
activated. This is a direct consequence of embedding space routing: expert terms
activate expert knowledge clusters; generic terms activate generic content. The model
already knows how to perform most tasks at an expert level — it just needs the right
vocabulary to route to that knowledge.

This single intervention — replacing generic terms with expert terms — produces
larger quality improvements than any other change to a skill definition.

---

## The 15-Year Practitioner Test

For every candidate vocabulary term, ask:

> "Would a senior practitioner with 15+ years of experience use this exact term
> when discussing this domain with a peer?"

- "mutation testing with Stryker" — YES. Practitioners name the tool.
- "good testing" — NO. This is how a beginner describes the concept.
- "OKLCH color space with tinted neutrals" — YES. Color scientists use OKLCH.
- "nice color scheme" — NO. This routes to Pinterest boards.

If a term fails this test, it belongs in the colloquial register of the description
(for triggering), not in the vocabulary payload (for routing).

---

## Sub-Domain Clustering

Organize terms into 3-5 clusters of 3-8 terms each. Each cluster represents a
sub-domain within the skill's scope.

**Why cluster:** Clustering creates local coherence that helps the model understand
relationships between terms. A flat list of 30 terms is harder to process than
5 clusters of 6 related terms.

**Cluster naming:** Use a bold header that names the sub-domain:
```
**Architecture & Boundaries:**
- hexagonal architecture (Cockburn), ports-and-adapters
- bounded context (Evans, DDD), context mapping
- anti-corruption layer, shared kernel
```

**Coverage:** Clusters should span the full scope of the domain, not just one
narrow aspect. If all 30 terms are about the same sub-topic, the vocabulary payload
is too narrow.

---

## The Routing Effectiveness Test

For each important term, compare:
1. Output when the prompt uses the expert term (e.g., "assess blast radius")
2. Output when the prompt uses a generic equivalent (e.g., "check the impact")

IF output quality is identical: The expert term is not activating different knowledge.
Either the term is not specific enough, or the domain uses different expert terminology.
Revise.

IF the expert-term version is noticeably more structured, specific, and actionable:
The vocabulary is working. Keep it.

---

## Named Framework Attribution

When the domain uses established frameworks, always include the originator:

- "task-relevant maturity (Andy Grove, High Output Management)" — not "delegation model"
- "INVEST criteria (Bill Wake)" — not "story quality checklist"
- "Cynefin framework (Dave Snowden)" — not "complexity categorization"

**Why:** The originator name activates the specific theoretical context in which the
framework was developed. "INVEST criteria" alone might route to general agile content.
"INVEST criteria (Bill Wake)" routes to the original user story quality research.

---

## Consultant-Speak Blacklist

These terms are so overused in training data that they route to generic advice content
rather than expert knowledge. Never use them in a vocabulary payload:

- best practices
- leverage / leveraging
- synergy / synergistic
- alignment / aligned
- holistic approach
- digital transformation
- paradigm shift
- strategic initiative
- scalable solution
- robust framework
- cutting-edge
- innovative approach
- stakeholder buy-in
- value proposition
- game-changer
- move the needle
- circle back
- deep dive (as a noun)
- low-hanging fruit
- ecosystem (unless literally biological)
- empower / empowering
- optimize (without specifying what metric)
- streamline
- actionable insights (use specific insight types instead)

**The test:** If a term could appear in a generic consulting slide deck about any
industry, it does not belong in a vocabulary payload.

---

## Worked Examples

### Example A: Code Review Skill

**Raw terms from user:** "good code, clean code, readable, maintainable, testable"

**After vocabulary engineering:**
```
**Structural Analysis:**
- cyclomatic complexity (McCabe), cognitive complexity (SonarSource)
- afferent coupling (Ca), efferent coupling (Ce), instability index (Martin)
- connascence taxonomy (Meilir Page-Jones) — identity, timing, value, position

**Change Safety:**
- shotgun surgery (Fowler, Refactoring), divergent change
- feature envy, inappropriate intimacy (code smell taxonomy)
- Liskov substitution violations, dependency inversion breaks

**Review Process:**
- conventional comments (Paul Slaughter) — labels: suggestion, issue, nitpick
- ship/no-ship decision framework, blocking vs non-blocking feedback
- diff review vs design review (different review altitudes)
```

### Example B: API Design Skill

**Raw terms from user:** "good APIs, RESTful, clean endpoints"

**After vocabulary engineering:**
```
**Resource Design:**
- RESTful resource modeling, sub-resource vs independent resource
- idempotent operations (GET, PUT, DELETE), safe methods
- HATEOAS (Richardson Maturity Model level 3), link relations

**Versioning & Evolution:**
- semantic versioning (SemVer), URI versioning vs header versioning
- additive-only changes (backward compatible), Postel's law (robustness principle)
- API deprecation lifecycle, sunset header (RFC 8594)

**Error Handling:**
- RFC 7807 (Problem Details for HTTP APIs), structured error responses
- idempotency keys for retry safety, request correlation IDs
- rate limiting headers (X-RateLimit-Limit, X-RateLimit-Remaining)
```

### Example C: Data Modeling Skill

**Raw terms from user:** "database design, good schemas, normalization"

**After vocabulary engineering:**
```
**Normalization & Denormalization:**
- Boyce-Codd normal form (BCNF), functional dependency analysis
- strategic denormalization, materialized views for read optimization
- surrogate keys vs natural keys, composite key decomposition

**Temporal Modeling:**
- slowly changing dimensions (Kimball) — Type 1/2/3/6
- bi-temporal modeling (valid time + transaction time)
- event sourcing with snapshot optimization, temporal tables (SQL:2011)

**Query Performance:**
- covering indexes, index-only scans, partial indexes
- query plan analysis (EXPLAIN ANALYZE), sequential scan vs index scan
- write amplification, B-tree vs LSM-tree tradeoffs
```
