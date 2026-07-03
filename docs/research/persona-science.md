# Persona Science

> Reference for Forge skill design. What controlled studies (2023-2026) actually show about role/persona prompting: personas steer alignment, tone, format, and scope — they do not improve factual capability, and on knowledge-heavy tasks they often slightly reduce it. Forge's identity conventions are labeled as design standards where they go beyond the evidence.

---

## The Evidence Base

Four studies anchor this file:

1. **Zheng et al. (2024), "When 'A Helpful Assistant' Is Not Really Helpful"** (arXiv:2311.10054, Findings of EMNLP 2024). 162 personas across 4 LLM families on 2,410 factual questions: adding personas in system prompts **did not improve accuracy** over no persona; some personas mildly hurt; which persona "works" is close to random.
2. **Hu, Rostami & Thomason (2026), "Expert Personas Improve LLM Alignment but Damage Accuracy"** (arXiv:2603.18507; PRISM = *Persona Routing via Intent-based Self-Modeling*, a gated-LoRA method the paper introduces). Core finding — the **alignment-accuracy tradeoff**: expert personas consistently improve alignment-dependent tasks (safety, preference, format-following; e.g. JailbreakBench +17.7% with a long safety persona) but **reliably damage knowledge retrieval** (MMLU 71.6% no-persona baseline → 68.0% with a ~5-token persona → 66.3% with a ~150-token persona). Tested on 7-8B open-weight models and R1 distills; no Claude models.
3. **Wharton Generative AI Labs (2025), Prompting Science Report 4: "Playing Pretend: Expert Personas Don't Improve Factual Accuracy"** (SSRN 5879722). Expert personas produced no consistent factual-accuracy gain on modern frontier models.
4. **Xiao et al. (2026), "When Does Persona Prompting Actually Help?"** (arXiv:2605.29420). 1,140 questions, 38 roles: personas help most on *advisory* questions (medicine, psychology) where structured expert framing has intrinsic value; they underperform on conceptual/explanatory questions; and they systematically increase judged expertise depth while **reducing clarity**.

**The synthesis:** a role identity is a *behavior-shaping* instrument. It focuses tone, register, scope, and instruction-following. It is not an accuracy or capability lever, and treating it as one is unsupported.

Anthropic's current guidance is consistent: "Setting a role in the system prompt focuses Claude's behavior and tone for your use case. Even a single sentence makes a difference" — framed as behavior/tone focusing, with no benchmark claims (Claude prompting best practices, 2026).

---

## The Alignment-Accuracy Tradeoff

The central persona finding (Hu et al. 2026):

| Dimension | Effect of stronger/longer persona |
|---|---|
| **Alignment** (instruction-following, safety, format, preference-judged quality) | Improves — long personas gave the largest gains (MT-Bench Extraction +0.65, STEM +0.60) |
| **Factual accuracy** (knowledge retrieval) | Degrades — and degrades *more* as the persona gets longer |

**The tension:** a strong persona makes the model more compliant with the role, not more correct. On knowledge tasks, even the minimal ~5-token persona underperformed *no persona at all* — brevity is the least-bad option for accuracy, purchased for alignment benefits, not an accuracy optimizer.

**How Forge resolves it:** agent identities exist to set scope, register, and decision boundaries (alignment goods). Factual quality is carried by other mechanisms — deterministic verification, independent review, and grounding claims in artifacts.

---

## Persona Length

**What the evidence says:** on knowledge-heavy tasks, damage grows with persona length (Hu et al.: ~5-token 68.0% vs ~150-token 66.3% on MMLU). On alignment tasks, longer personas helped more. There is **no published token threshold** — no study establishes a "<50 token optimum" or a ">100 token cliff."

> **Forge design standard — identity length.** Forge keeps role identities to roughly one to three sentences (~20-50 tokens): the minimum needed for role, responsibility, and organizational context. This is a convention motivated by the length-scaling direction above and by context economy ("the smallest possible set of high-signal tokens" — Anthropic, Effective Context Engineering, 2025), not a measured optimum.

---

## Role-Task Fit

Persona effects are task-type dependent (Hu et al.; Xiao et al.):

| Task type | Persona effect |
|---|---|
| Advisory / open-ended (risk communication, recommendations) | Helps — structured expert framing adds value |
| Safety / format / preference-judged generation | Helps — the alignment side of the tradeoff |
| Conceptual explanation, factual QA | Neutral to harmful; plain, clear prompting wins |

**Rule:** match the role to the task's *register*, and don't expect the role to add knowledge. A misaligned persona buys clarity loss with no offsetting gain.

**Corollary:** one role per agent. Combining titles ("architect and also PM and QA lead") muddles scope and decision boundaries — the things a role actually controls.

---

## Real Job Titles, Superlatives, and Flattery

**Honest status of two long-standing Forge rules:**

1. **"Use real job titles."** No published study shows real titles outperform invented personas or generic descriptions (Zheng et al. tested occupational roles among 162 personas: no accuracy gain). Forge keeps real titles as a **design standard** for different, defensible reasons: they are concise, unambiguous scope descriptors; they make teams legible to humans; and they map cleanly to real-world deliverables and decision boundaries.
2. **"Never use superlatives."** No study isolates flattery inside personas ("world-class," "best in the world") against plain role statements. Adjacent evidence: heavy-handed role assignments "may actually backfire by limiting helpfulness" (DigitalOcean guide, practitioner tier); excessive politeness/flattery toward the model buys nothing (Yin et al. 2024, arXiv:2402.14531); tone effects on accuracy are real but model-dependent (Dobariya & Kumar 2026, arXiv:2605.29027). Forge bans superlatives as a **style convention**: they add tokens, add no scope information, and claim quality instead of defining behavior.

**Retired claims (do not reintroduce):** "real titles activate dense training-data clusters," "flattery routes to motivational/marketing embedding clusters," and any attribution of these to PRISM or to Ranjan. The embedding-cluster mechanism appears in no published source; Ranjan (2025, arXiv:2512.06744) is a word-embedding formatting study unrelated to personas.

---

## Practical Persona Design Rules

1. **Use a real job title** — as a scope descriptor, not a capability claim.
2. **Keep the identity to 1-3 sentences (~20-50 tokens)** — Forge convention; see length section.
3. **Define through responsibility and boundaries,** not quality claims: what they own, what they escalate, what is out of scope.
4. **Include organizational context.** "Reports to [X], collaborates with [Y]" sets handoff structure — the thing personas demonstrably control.
5. **Carry expertise in the vocabulary payload and references, not the persona.** Domain terms steer generative output (see vocabulary-routing.md); the persona sets the register they're used in.
6. **No superlatives** ("world-class," "best," "genius," "top-tier") — style convention; they add tokens and claim quality instead of defining behavior.
7. **One role per agent.**
8. **Don't rely on the persona for correctness.** Pair every producing agent with independent verification; a persona cannot make an agent more truthful — the alignment-accuracy tradeoff runs the other way.

---

## Forge Agent Identity Format

```markdown
## Role Identity
You are a [real job title] responsible for [primary responsibility]
within [organizational context]. You report to [authority] and
collaborate with [adjacent roles].
```

**Token budget:** ~20-50 tokens (Forge convention).

**Example — Good:**
> You are a software architect responsible for system design and technical decision-making within a product engineering team. You report to the engineering director and collaborate with the product manager, lead engineer, and QA engineer.

**Example — Bad (long, flattery, no scope information):**
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

*Sources: Zheng et al. (2024), arXiv:2311.10054; Hu, Rostami & Thomason (2026), arXiv:2603.18507; Wharton Generative AI Labs Prompting Science Report 4 (2025), SSRN 5879722; Xiao et al. (2026), arXiv:2605.29420; Yin et al. (2024), arXiv:2402.14531; Anthropic Claude prompting best practices (2026). See docs/research/source-index.md.*
