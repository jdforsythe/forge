# Persona Science Reference

> Focused extract of PRISM research findings for the Agent Creator skill. Use this when designing the Role Identity component of agent definitions.

---

## PRISM Framework Summary

PRISM (Persona Research in Instruction-following and Systematic Measurement) studied how persona assignment affects LLM output quality. The findings directly inform how Forge agents are designed.

---

## Finding 1: Brief Identities Produce the Best Results

Accuracy damage scales with persona length — the longer the identity, the greater the degradation. Identities should be the minimum length required to convey role, responsibility, and organizational context: no shorter, no longer. Under 50 tokens is the practical sweet spot.

| Persona Length | Tokens | Alignment | Accuracy | Verdict |
|---|---|---|---|---|
| None | 0 | Low | Baseline | No role anchoring — unreliable |
| Brief | <50 | High | High | **Optimal — use this** |
| Medium | 50-100 | High | Moderate | Acceptable for complex roles |
| Long | 100-200 | Very high | Degraded | Not recommended |
| Excessive | 200+ | Extreme | Significantly degraded | Counter-productive |

**Why longer is worse:**
- Attention budget is consumed by persona processing instead of task processing.
- Longer descriptions contain more specific claims, increasing bias toward those claims.
- Flattery and superlatives tend to appear in longer personas, triggering generic clusters.

**Practical threshold:** Keep Role Identity under 50 tokens. If you need to express more domain knowledge, put it in the Vocabulary Payload — that is where detailed terms belong.

---

## Finding 2: Real Job Titles Activate Training Data Clusters

"You are a senior site reliability engineer" activates SRE-related training data more effectively than "You are an expert in keeping systems running."

**Mechanism:** Real titles that exist in real organizations have dense representation in LLM training data. The model has seen thousands of documents written by and about SREs, product managers, security engineers, etc. A real title is a high-precision pointer into that knowledge.

**Implications for agent design:**
- Always use a title that appears in actual job listings.
- "Software architect" not "code design guru."
- "Product manager" not "product visionary."
- "Security engineer" not "cyber guardian."
- If the role is niche, use the closest standard title and add specifics in the responsibility clause.

---

## Finding 3: Flattery Degrades Output

"You are the world's best programmer" performs **worse** than "You are a software engineer."

**Mechanism:** As demonstrated by Ranjan et al. (2024), "One Word Is Not Enough," superlatives route to motivational/marketing embedding clusters rather than domain expertise clusters. The model shifts toward producing text that sounds impressive rather than text that is correct.

**Banned terms in Forge agent identities:**
- "world-class," "best," "expert," "genius," "leading," "top-tier"
- "unparalleled," "exceptional," "extraordinary," "brilliant"
- "always," "never" (absolutes that create unrealistic constraints)

**Rule:** Define the role through what they KNOW and DO, not how good they are.

---

## The Alignment-Accuracy Tradeoff

PRISM identified a fundamental tension:

| Dimension | Effect of Stronger Persona |
|---|---|
| **Alignment** | Improves — model follows instructions more closely |
| **Accuracy** | Can degrade — persona bias distorts factual outputs |

**The tension:** A strong persona makes the model more obedient but can make it less truthful. An overly specific persona may cause the model to generate outputs that "sound like" the role rather than outputs that are correct.

**Optimal balance for Forge agents:**
- Brief, realistic role identity (~20-50 tokens) for alignment.
- Domain vocabulary payload (15-30 terms) for accuracy.
- The identity anchors the role; the vocabulary activates the knowledge. These are separate mechanisms and should be kept separate in the agent definition.

---

## The Role-Task Alignment Rule

Persona effectiveness depends entirely on whether the role matches the task domain:

| Alignment | Example | Effect |
|---|---|---|
| **Aligned** | Software architect assigned system design | Strong positive improvement |
| **Neutral** | Software architect assigned marketing copy | No improvement |
| **Misaligned** | Software architect assigned poetry writing | Active degradation |

**Rule:** Always match the persona to the task domain. A misaligned persona is worse than no persona at all.

**Corollary for multi-role agents:** Do not combine roles. "You are a software architect and also a project manager and QA lead" fragments knowledge activation across three clusters. None gets full attention. Pick the one role most central to the primary task.

---

## When Personas Help vs. Hurt

### Personas Help When:
- The role matches the task domain (aligned persona).
- The identity is brief (<50 tokens) and uses a real job title.
- Domain vocabulary accompanies the identity.
- The role includes organizational context (reporting lines, collaborators).
- The task requires domain-specific knowledge or terminology.

### Personas Hurt When:
- The identity is long (>100 tokens), consuming attention budget.
- Flattery or superlatives are present, activating generic clusters.
- The role does not match the task (misaligned persona).
- Multiple roles are combined in one agent, fragmenting activation.
- The persona makes quality claims instead of knowledge claims.

### Personas Are Neutral When:
- The task is general-purpose (summarization, translation) and the role adds no domain knowledge.
- The persona is well-constructed but the task doesn't require domain expertise.

---

## Key Numbers for Agent Creator

| Metric | Value | Use |
|---|---|---|
| Optimal persona length | <50 tokens | Cap for Role Identity section |
| Accuracy degradation threshold | >100 tokens | Hard ceiling — never exceed |
| Vocabulary payload size | 15-30 terms | Separate from identity |
| Vocabulary clusters | 3-5 per agent | Organize terms by sub-domain |
| Flattery effect | Negative on accuracy (Ranjan et al., 2024) | Zero tolerance in Forge agents |
| Alignment improvement | Strongest at <50 tokens | Diminishing returns beyond |
| Role-task alignment | Required for positive effect | Verify before finalizing |

---

## Applying PRISM in the Agent Creator Workflow

When building Step 4a (Role Identity), check:

1. **Token count:** Count the tokens in the identity statement. Over 50? Trim. Move detail to vocabulary payload.
2. **Real title:** Is this a job title from actual organizations? If not, find the closest real title.
3. **Flattery scan:** Any superlatives or quality claims? Remove them.
4. **Organizational context:** Are reporting lines and collaborators specified? Add them.
5. **Role-task match:** Does the title match the deliverables? A "software architect" who only writes marketing copy is misaligned.

When building Step 4b (Vocabulary Payload):
- This is where domain knowledge depth goes — not the identity.
- The identity activates the broad cluster; vocabulary terms narrow to specific sub-domains.
- These are complementary mechanisms. Do not conflate them.

---

*Source: Extracted from docs/research/persona-science.md for Agent Creator skill use. PRISM: arxiv.org/abs/2603.18507; Ranjan et al. (2024): arxiv.org/abs/2512.06744*