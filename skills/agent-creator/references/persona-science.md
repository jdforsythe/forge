# Persona Science Reference

> Condensed operational extract of `docs/research/persona-science.md` for the Agent Creator skill. Use this when designing the Role Identity component of agent definitions. What controlled studies actually show: personas steer alignment, tone, format, and scope. They do not improve factual accuracy, and on knowledge-heavy tasks longer personas hurt more.

---

## The Evidence Base

Four studies anchor this file:

1. **Zheng et al. (2024)** (arXiv:2311.10054, EMNLP Findings). 162 personas, 4 model families, 2,410 factual questions: personas did **not** improve accuracy over no persona; some mildly hurt.
2. **Hu, Rostami & Thomason (2026), "Expert Personas Improve LLM Alignment but Damage Accuracy"** (arXiv:2603.18507). Introduces PRISM (*Persona Routing via Intent-based Self-Modeling*), a gated-LoRA method — not a token-length rule. Core finding: the **alignment-accuracy tradeoff** (below).
3. **Wharton Prompting Science Report 4 (2025)** (SSRN 5879722). Expert personas: no consistent factual-accuracy gain on modern frontier models.
4. **Xiao et al. (2026)** (arXiv:2605.29420). 1,140 questions, 38 roles: personas help advisory questions, underperform on conceptual/explanatory ones, and increase judged depth while reducing clarity.

**Synthesis:** a role identity is a behavior-shaping instrument — tone, register, scope, instruction-following. It is not an accuracy or capability lever.

---

## The Alignment-Accuracy Tradeoff

The central finding (Hu et al. 2026):

| Dimension | Effect of stronger/longer persona |
|---|---|
| Alignment (instruction-following, safety, format) | Improves — longer personas gave the largest gains |
| Factual accuracy (knowledge retrieval) | Degrades — and degrades *more* as the persona gets longer |

MMLU: 71.6% no-persona baseline → 68.0% with a ~5-token persona → 66.3% with a ~150-token persona. Even the minimal persona underperformed no persona at all on this knowledge task — brevity is the least-bad option for accuracy, not an accuracy optimizer.

**How Forge resolves it:** identities set scope, register, and decision boundaries (alignment goods). Factual quality is carried by other mechanisms — verification, independent review, grounding claims in artifacts — never by the persona.

---

## Length Guidance

**What the evidence says:** damage on knowledge tasks grows with persona length. There is **no published token threshold** — no study establishes a "<50-token optimum" or a ">100-token cliff."

> **Forge design standard:** keep Role Identity to ~20-50 tokens (1-3 sentences) — the minimum needed for role, responsibility, and organizational context. This is a convention motivated by the length-scaling *direction* above and by context economy, not a measured optimum. When building Step 4a, count tokens against this convention and trim; if more domain depth is needed, it belongs in the Vocabulary Payload, not the identity.

---

## Role-Task Fit

| Task type | Persona effect |
|---|---|
| Advisory / open-ended (recommendations, risk communication) | Helps — structured expert framing adds value |
| Safety / format / preference-judged generation | Helps — the alignment side of the tradeoff |
| Conceptual explanation, factual QA | Neutral to harmful — plain, clear prompting wins |

**Rule:** match the role to the task's register; don't expect the role to add knowledge. A misaligned persona buys clarity loss with no offsetting gain.

**Corollary — one role per agent.** Combining titles ("architect and also PM and QA lead") muddles scope and decision boundaries — the things a role actually controls. When building Step 4a, verify the job title matches the primary deliverables in Step 4c.

---

## Real Titles and No Superlatives (Design Standards)

Both are Forge conventions, not measured effects. State them honestly:

1. **Use a real job title.** No study shows real titles outperform invented ones (Zheng et al. tested occupational roles: no accuracy gain). Forge keeps real titles because they are concise, unambiguous scope descriptors that make teams legible to humans and map to real deliverables — not because they unlock hidden model capability.
2. **No superlatives** ("world-class," "best," "genius," "unparalleled"). No study isolates flattery inside personas against plain role statements. Forge bans them as a style convention: they add tokens, add no scope information, and claim quality instead of defining behavior. Adjacent evidence: heavy-handed role assignments "may backfire by limiting helpfulness" (DigitalOcean, practitioner tier); excessive flattery toward the model buys nothing (Yin et al. 2024, arXiv:2402.14531).

**Retired — do not reintroduce:** "real titles activate dense training-data clusters," "flattery routes to motivational/marketing embedding clusters," any attribution of these to PRISM or to Ranjan (2025, arXiv:2512.06744, a word-embedding formatting study unrelated to personas).

---

## Practical Persona Design Rules

1. Use a real job title — a scope descriptor, not a capability claim.
2. Keep the identity to ~20-50 tokens (Forge convention).
3. Define through responsibility and boundaries, not quality claims.
4. Include organizational context — reporting line and collaborators.
5. Carry expertise depth in the vocabulary payload, not the persona.
6. No superlatives — style convention.
7. One role per agent.
8. Don't rely on the persona for correctness — pair every producing agent with independent verification.

---

## Identity Format

```markdown
## Role Identity
You are a [real job title] responsible for [primary responsibility]
within [organizational context]. You report to [authority] and
collaborate with [adjacent roles].
```

**Token budget:** ~20-50 tokens (Forge convention).

**Good:**
> You are a software architect responsible for system design and technical decision-making within a product engineering team. You report to the engineering director and collaborate with the product manager, lead engineer, and QA engineer.

**Bad (long, flattery, no scope information):**
> You are a world-class, highly experienced software architect with decades of expertise in building scalable, resilient, high-performance distributed systems. You are known for your exceptional ability to make brilliant technical decisions that consistently lead to successful outcomes.

The bad example spends ~60 tokens claiming quality, defines no boundaries, and — per the tradeoff above — buys alignment pressure at a small accuracy cost with nothing in return.

---

## Key Findings

| Finding | Result | Source |
|---|---|---|
| Personas → factual accuracy | No improvement; sometimes mildly negative | Zheng et al. 2024, arXiv:2311.10054 |
| Expert personas: alignment vs accuracy | Alignment up, knowledge retrieval down (MMLU 71.6%→68.0%→66.3%) | Hu et al. 2026, arXiv:2603.18507 |
| Expert personas on frontier models | No consistent factual gain | Wharton Prompting Science Report 4, 2025 |
| Where personas help | Advisory/open-ended tasks; depth up, clarity down | Xiao et al. 2026, arXiv:2605.29420 |
| Longer personas on knowledge tasks | More damage than minimal ones | Hu et al. 2026 |
| Identity length ~20-50 tokens | Convention | **Forge design standard** |
| Real titles / no superlatives | Convention (scope clarity, token economy) | **Forge design standard** |

---

*Condensed from `docs/research/persona-science.md`. Full bibliography: `docs/research/source-index.md`.*
