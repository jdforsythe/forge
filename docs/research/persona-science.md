# Persona Science

> Reference for Forge skill design. PRISM research findings on persona effectiveness, the alignment-accuracy tradeoff, persona length effects, and the role-task alignment rule.

---

## PRISM Research Findings

The PRISM (Persona Research in Instruction-following and Systematic Measurement) framework studied how persona assignment affects LLM output quality. Three key findings:

### Finding 1: Brief Identities Work Best
Role assignments under **50 tokens** produce the highest-quality outputs. Longer persona descriptions (100+ tokens) degrade accuracy. The model spends attention processing the persona description rather than the task.

### Finding 2: Real Job Titles Activate Training Data Clusters
"You are a senior site reliability engineer" activates SRE-related training data more effectively than "You are an expert in keeping systems running." Real titles that exist in real organizations have dense representation in training data.

### Finding 3: Flattery Degrades Output
"You are the world's best programmer" performs **worse** than "You are a software engineer." Superlatives activate generic aspirational text patterns (motivational content, marketing copy) rather than domain expertise clusters.

---

## The Alignment-Accuracy Tradeoff

PRISM identified a fundamental tension in persona assignment:

| Dimension | Effect of Stronger Persona |
|---|---|
| **Alignment** | Improves — model follows instructions more closely |
| **Accuracy** | Can degrade — persona bias distorts factual outputs |

**The tension:** A strong persona makes the model more obedient but can make it less truthful. An overly specific persona may cause the model to generate outputs that "sound like" the role rather than outputs that are correct.

**Optimal balance:** Brief, realistic role identity (~20-50 tokens) paired with domain vocabulary. This achieves high alignment without significant accuracy loss.

---

## Persona Length Effects

| Persona Length | Tokens | Alignment | Accuracy | Recommendation |
|---|---|---|---|---|
| None | 0 | Low | Baseline | Unreliable — no role anchoring |
| Brief | <50 | High | High | **Optimal — use this** |
| Medium | 50-100 | High | Moderate | Acceptable for complex roles |
| Long | 100-200 | Very high | Degraded | Not recommended |
| Excessive | 200+ | Extreme | Significantly degraded | Counter-productive |

**Why longer is worse:**
- Attention budget consumed by persona processing instead of task processing.
- Longer descriptions contain more specific claims, increasing bias.
- Flattery and superlatives tend to appear in longer personas, triggering generic clusters.

---

## The Role-Task Alignment Rule

Persona effectiveness depends entirely on whether the role matches the task domain:

| Alignment | Example | Effect |
|---|---|---|
| **Aligned** | Software architect → system design | Strong improvement |
| **Neutral** | Software architect → marketing copy | No improvement |
| **Misaligned** | Software architect → poetry writing | Active degradation |

**Rule:** Always match the persona to the task domain. A misaligned persona is worse than no persona at all.

**Corollary:** For multi-role agents, use the role most central to the primary task. Do not combine roles ("You are a software architect and also a project manager and QA lead") — this fragments the knowledge activation.

---

## Practical Persona Design Rules

1. **Use real job titles** that exist in real organizations. "Senior site reliability engineer" not "system health guardian."
2. **Keep identity under 50 tokens.** This is the empirically optimal length from PRISM.
3. **Define through knowledge and behavior,** not quality claims. Say what they know and do, not how good they are.
4. **Include organizational context.** "Reports to [X], collaborates with [Y]" establishes scope boundaries and activates team-dynamic knowledge.
5. **Pair identity with domain vocabulary.** The persona activates a broad cluster; vocabulary terms narrow it to specific sub-domains.
6. **Never use superlatives.** Banned: "world-class," "best," "expert," "genius," "leading," "top-tier."
7. **One role per agent.** Do not assign multiple job titles. Pick the one most relevant to the primary task.

---

## Forge Agent Identity Format

Based on PRISM findings, Forge agent definitions use this format:

```markdown
## Role Identity
You are a [real job title] responsible for [primary responsibility]
within [organizational context]. You report to [authority] and
collaborate with [adjacent roles].
```

**Token budget:** 20-50 tokens for the identity statement.

**Example — Good:**
> You are a software architect responsible for system design and technical decision-making within a product engineering team. You report to the engineering director and collaborate with the product manager, lead engineer, and QA engineer.

**Example — Bad (too long, flattery):**
> You are a world-class, highly experienced software architect with decades of expertise in building scalable, resilient, high-performance distributed systems. You are known for your exceptional ability to make brilliant technical decisions that consistently lead to successful outcomes.

The bad example wastes ~60 tokens on flattery, activates generic motivational text clusters, and provides no useful organizational context.

---

## Key Numbers

| Metric | Value | Source |
|---|---|---|
| Optimal persona length | <50 tokens | PRISM |
| Accuracy degradation threshold | >100 tokens | PRISM |
| Vocabulary payload | 15-30 terms | Vocabulary routing research |
| Vocabulary clusters | 3-5 per agent | Vocabulary routing research |
| Role-task alignment effect | Strong positive when aligned | PRISM |
| Flattery effect | Negative (degrades accuracy) | PRISM |

---

*Source: Skill Design Research Synthesis §3.1-3.5, PRISM framework*
