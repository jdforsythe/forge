# Forge Methodology

How Forge designs agents and teams, why each principle exists, and the research behind it.

> **Evidence baseline.** This methodology targets the Claude 4.5+/5 model generation (Fable 5, Opus 4.8, Sonnet 5, Haiku 4.5) as of July 2026. It follows a two-tier evidence policy: peer-reviewed papers and lab engineering docs (Anthropic, Google Research/MIT, and similar) support each rule below; practitioner blog posts and vendor guides corroborate but never singly justify one. Every quantitative claim traces to an entry in [`docs/research/source-index.md`](docs/research/source-index.md). Where a rule goes beyond what's published — team size caps, token budgets, iteration limits, cluster counts — it is explicitly labeled a **Forge design standard**, not a research finding.

---

## 1. The Vocabulary Routing Principle

**What we do:** Every agent and skill in Forge carries a payload of 15-30 precise domain terms, organized into 3-5 clusters of 3-8 related concepts (Forge design standard — see [`vocabulary-routing.md`](docs/research/vocabulary-routing.md)).

**Why it works:** Generic prompts get generic output because unguided sampling gravitates toward the safest, most universally-acceptable choices in the training distribution — Anthropic calls this *distributional convergence*. Its frontend-design work found that a ~400-token skill of targeted vocabulary plus explicit never-use lists fixed generic output immediately: precise terms unlock capability the model already has but doesn't express by default. Separately, MASS (Zhou et al., ICLR 2026) found that in multi-agent system design, prompt optimization dominates topology choice — vocabulary and prompt quality outweigh structural complexity as a design lever.

Specificity has a ceiling, though. Schreiter (2025) systematically varied terminology specificity across STEM, law, and medicine QA and found an **optimal mid-range** — neither generic wording nor maximally technical jargon performs best. The **15-year practitioner test** operationalizes that optimum: would a senior practitioner use this exact term when speaking to a peer? "Circuit breaker pattern" passes; "leverage best practices" fails as consultant-speak, and "stochastic gradient descent with momentum" fails in any agent that isn't an ML specialist.

**Honest scope limit:** the popular story that "terms activate knowledge clusters in embedding space" is a plausible working model, not a demonstrated mechanism — no paper shows term-to-cluster routing for generative LLM output. Treat the cluster language in Forge's docs as a mental model, not a citation. And vocabulary steers *register, style, and domain framing* of output — it is not shown to improve factual accuracy. Where correctness is the goal, the lever is verification (§4), not vocabulary.

---

## 2. The Real-World Role Principle

**What we do:** Forge assigns agents real job titles using brief identity statements — roughly one to three sentences, ~20-50 tokens (Forge convention). No flattery, no invented personas, one role per agent.

**Why it works:** A role identity sets **scope, register, and decision boundaries** — it is not a capability lever. Hu, Rostami & Thomason (2026) — PRISM, *Persona Routing via Intent-based Self-Modeling* — identified the central tension: expert personas reliably improve alignment-dependent tasks (safety, format-following, preference judgments) but reliably damage knowledge retrieval, and the damage grows with persona length (MMLU: 71.6% with no persona → 68.0% with a ~5-token persona → 66.3% with a ~150-token persona). This is corroborated by Zheng et al. (2024) — 162 personas across 2,410 factual questions found no accuracy improvement, and some personas mildly hurt it — and by the Wharton Generative AI Labs report, which found no consistent factual gain from expert personas on modern frontier models.

**How Forge resolves the tradeoff:** identities do the alignment job — focusing tone, scope, and register — and nothing else. Factual quality is carried by other mechanisms: precise vocabulary framing, independent verification, and grounding claims in artifacts, not by the persona.

**Design choices, honestly labeled:** brief identities (~20-50 tokens) are a **Forge convention** motivated by the length-scaling direction above, not a measured token optimum — no study establishes a "<50-token" cliff or any other specific threshold. Real job titles and a no-superlatives rule are also **Forge design standards**, chosen for reasons other than accuracy: titles are concise, unambiguous scope descriptors that make teams legible to humans and map cleanly to real deliverables; banning superlatives ("world-class," "best-in-class") is a token-economy and clarity rule — they add length and claim quality instead of defining behavior.

---

## 3. Scaling: When Teams Help and When They Hurt

This is Forge's best-evidenced principle. **What we do:** always try a single agent first (the cascade pattern), and escalate to a team only when all four conditions below hold. Teams are capped at **3-4 agents recommended, 5 hard cap**.

**Why it works:** Kim et al. (2025) — a Google Research/MIT-led study with Google DeepMind co-authors, arXiv:2512.08296 — ran five architectures across three model families and four benchmarks. Its central finding is the **~45% baseline paradox**: tasks where a single agent already exceeds ~45% task accuracy see *negative* returns from added agents (coordination coefficient β = −0.408, p < 0.001). The single agent already covers the accessible part of the problem; added agents add coordination overhead, not capability.

Task structure, not team size, decides the outcome. Strictly sequential tasks degraded **−39% to −70% under every multi-agent variant tested**; decomposable, parallelizable tasks gained up to **+80.9%** under a centralized topology. Errors also propagate differently by topology: uncoordinated teams amplified errors **17.2x**, versus **4.4x** with a centralized integrator — if a team exists at all, someone must own integration. And teams routinely cost more than they capture in ability: a **2-6x efficiency penalty** is typical, and even Anthropic's own orchestrator-worker research system — which beat a single Claude Opus 4 by 90.2% on a breadth-first research eval — did so only by spending **~15x the tokens**. Multi-agent is a way to spend more compute productively on parallelizable work, not a way to get more capability per token.

**The compute confound:** Tran & Kiela (2026) showed that with thinking-token budgets held equal, single agents matched or beat five multi-agent architectures on multi-hop reasoning. The burden of proof is on the team — a "team beat one agent" result only counts if the single agent got comparable effort.

> **Forge design standard — team size.** 3-4 agents recommended, 5 hard cap. This is an engineering convention informed by Kim et al.'s fixed-budget analysis (turn count grows superlinearly, T ∝ (n+0.5)^1.724; per-agent reasoning capacity becomes "prohibitively thin beyond 3-4 agents") and by early-saturation findings elsewhere — the Ringelmann-effect scaling law (homogeneous teams hit hard diversity ceilings) and MacNet's logistic, early-saturating collaborative-scaling curve. It is not a measured universal optimum.

**Four conditions, all required, before recommending multi-agent:** (1) the task decomposes into subtasks with clean typed interfaces; (2) subtasks require genuinely different expertise — homogeneous teams hit diversity ceilings fast; (3) a single-agent trial at comparable effort showed clear capability gaps, or the task sits below the ~45% baseline regime; (4) project scope justifies the cost (2-6x, or ~15x for research-style orchestration).

---

## 4. The Failure-Mode Principle

**What we do:** every agent and skill definition names failure modes with detection signals and resolutions. Independent, fresh-context verification is a required stage, not an optional nicety. Review loops cap at 3 rounds before escalating to a human.

**Why it works:** MAST — the Multi-Agent System Failure Taxonomy (Cemri et al., NeurIPS 2025, arXiv:2503.13657) — is the real, empirically-grounded reference here: 14 failure modes in 3 categories, drawn from 1,600+ annotated execution traces across 7 frameworks. **Specification issues are the largest category, at 41.77%** — failures baked in before any agent speaks — which is the direct empirical case for Forge's structured agent definitions, explicit deliverables, and typed artifact handoffs. Inter-agent misalignment (36.94%) and task verification (21.30%) round out the taxonomy. Taxonomy-guided interventions produced real, measured gains (+15.6 points on ChatDev, +9.4 on AG2). Notably, MAST shows role disobedience is *rare* in practice (0.5%) — step repetition (17.14%) and reasoning-action mismatch (13.98%) are the dominant modes instead — and it recommends no specific iteration-cap number.

Forge's own **watchlist (W-1 through W-7)** — Rubber-Stamp Review, Role Overlap, Unbounded Review Loops, Confident Overreach, Error Cascade, Handoff Loss, Premature Done — is clearly labeled as house heuristics informed by MAST, Anthropic's harness-design guidance, and practitioner experience, mapped to their nearest MAST analogs but not part of MAST itself (see [`failure-taxonomy.md`](docs/research/failure-taxonomy.md)).

**Verification is structural, not persuasive.** Anthropic found generator models will confidently praise mediocre work; separating the generator from a standalone, skeptical evaluator "proves to be a strong lever" — fresh-context verifier subagents outperform self-critique. The **3-round review cap** is a Forge convention motivated by Self-Refine's observed plateau at roughly the third iteration in a single-agent setting (Madaan et al., 2023) — not a MAST finding.

---

## 5. Structured Artifacts and Context Engineering

**What we do:** Forge teams communicate through structured artifacts — PRDs, ADRs, test reports, interface contracts — never free-form dialogue. Writes stay single-threaded. Context loads progressively: role identity and vocabulary always loaded (~200-500 tokens), task SOPs on trigger, full reference on demand.

**Why structured handoffs work:** MetaGPT (Hong et al., 2023) is the real, sourced comparison here: structured artifact handoffs scored **3.75/4 executability versus 2.25 for dialogue-based ChatDev**, with roughly **3x fewer** human revisions (0.83 vs 2.5). Cognition's boundary condition matters too: artifacts must not become lossy summaries that hide decisions — share full traces where feasible, and keep writes single-threaded, since parallel writers make conflicting implicit decisions.

**Why lean context works:** every token draws on a finite attention budget, so the goal is the smallest set of high-signal tokens (Anthropic, "Effective Context Engineering for AI Agents," Sep 2025). Chroma's "Context Rot" study (18 models, July 2025) found performance degrades **monotonically** with input length — not a U-curve — and that focused prompts outperform full ones; there's no documented penalty for a small, well-chosen context. Progressive disclosure operationalizes this: layer 1 (role + vocabulary) is always loaded; layer 2 (SOPs) loads on task-type match; layer 3 (full reference) loads on demand; layer 4 compresses large inputs into distilled summaries. On current models, durable memory pays off directly — Fable 5 "performs particularly well when recording lessons from previous runs," even as plain markdown files.

---

## 6. Current-Model Addendum

What changes for the Claude 4.5+/5 generation, distinct from the durable principles above:

- **Over-prescription now degrades output.** Older, heavily-scripted skills should be refactored: push detail into the *description* (models still "undertrigger" under-described skills, so descriptions should be pushy and specific) and keep the *body* lean — verbose, prescriptive bodies measurably hurt current models (Prompting Claude Fable 5, 2026).
- **Never instruct reasoning-echo.** Don't build harnesses that force a model to narrate its step-by-step reasoning verbatim — on Fable 5 this can trip the refusal classifier.
- **Manual chain-of-thought is deprecated.** Current models run adaptive thinking internally; explicit "think step by step" scaffolding gives marginal-to-negative returns on reasoning models and adds latency for no benefit.
- **Plan for refusal fallback on security-adjacent work.** Fable 5 shows elevated false-positive refusals on security-adjacent tasks following its redeploy; harnesses doing this kind of work should fall back to Opus 4.8 rather than retry indefinitely.
- **Parallel subagents and memory are first-class, not workarounds.** Current platforms support nested subagents, agent teams for parallel research and review, and persistent memory files as durable state — Forge blueprints should use these directly rather than simulating them.

---

## 7. Sources and Further Reading

| Source | Year | Key Contribution | Used In |
|---|---|---|---|
| Kim et al., "Towards a Science of Scaling Agent Systems" (Google Research/MIT, with Google DeepMind co-authors) | 2025 | ~45% baseline paradox, sequential-task degradation, error amplification, coordination overhead | §3 |
| Tran & Kiela, "Single-Agent LLMs Outperform MAS Under Equal Thinking Budgets" | 2026 | The compute confound | §3 |
| Bertalanič & Fortuna, Ringelmann-effect scaling law | 2026 | Team-size ceilings, homogeneous-team saturation | §3 |
| Qian et al., MacNet (ICLR 2025) | 2024 | Logistic, early-saturating collaborative scaling | §3 |
| Cemri et al., MAST — Multi-Agent System Failure Taxonomy (NeurIPS 2025) | 2025 | 14 failure modes, 3 categories; specification issues 41.77% | §4 |
| Madaan et al., Self-Refine | 2023 | Iteration plateau (~3 rounds), single-agent setting | §4 |
| Hu, Rostami & Thomason, PRISM — Persona Routing via Intent-based Self-Modeling | 2026 | Alignment-accuracy tradeoff; persona-length damage | §2 |
| Zheng et al., "When 'A Helpful Assistant' Is Not Really Helpful" | 2024 | 162 personas, no accuracy gain | §2 |
| Wharton Generative AI Labs, Prompting Science Report 4 | 2025 | No consistent factual gain from expert personas | §2 |
| Xiao et al., "When Does Persona Prompting Actually Help?" | 2026 | Task-type dependence of persona effects | §2 |
| Anthropic, "Improving Frontend Design through Skills" | 2025 | Distributional convergence; vocabulary unlocks latent capability | §1 |
| Schreiter, prompt-vocabulary specificity study | 2025 | Mid-range specificity optimum | §1 |
| Zhou et al., MASS (ICLR 2026) | 2025 | Prompt optimization dominates topology choice | §1 |
| Hong et al., MetaGPT (ICLR 2024 oral) | 2023 | Structured artifacts vs. dialogue: 3.75 vs 2.25 executability | §5 |
| Anthropic, "Effective Context Engineering for AI Agents" | 2025 | Attention budget, progressive disclosure | §5 |
| Chroma Research, "Context Rot" | 2025 | Monotonic degradation, no U-curve | §5 |
| Anthropic, "Prompting Claude Fable 5" | 2026 | Skill refactoring, verifier subagents, memory, refusal classifier | §4, §5, §6 |
| Anthropic, "Introducing Claude Fable 5 and Claude Mythos 5" | 2026 | Refusal fallback behavior, model generation specs | §6 |

Full research notes with detailed findings, data tables, and implementation guidance are in the [`docs/research/`](docs/research/) directory:

- [`vocabulary-routing.md`](docs/research/vocabulary-routing.md) — Term selection, clustering, the 15-year practitioner test
- [`persona-science.md`](docs/research/persona-science.md) — The persona evidence base, alignment-accuracy tradeoff, role-task fit
- [`scaling-laws.md`](docs/research/scaling-laws.md) — Kim et al. scaling principles, coordination costs, topology selection
- [`context-engineering.md`](docs/research/context-engineering.md) — Attention budget, context rot, progressive disclosure layers
- [`failure-taxonomy.md`](docs/research/failure-taxonomy.md) — The MAST taxonomy and the Forge watchlist
- [`team-design.md`](docs/research/team-design.md) — MetaGPT findings, CaptainAgent, cascade pattern, topology comparison
- [`source-index.md`](docs/research/source-index.md) — Full bibliography and verified key numbers
