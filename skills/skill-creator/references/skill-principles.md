# Skill Design Principles: Condensed Research

> Reference for the Skill Creator. Each principle includes the mechanism (WHY it works) and the practical implication. Read this file when you need to explain a design decision or when a user asks why a skill should be structured a certain way.

## Table of Contents
1. [Vocabulary Routing](#1-vocabulary-routing)
2. [U-Shaped Attention Curve](#2-u-shaped-attention-curve)
3. [Negative Constraints Steer Past the Distribution Center](#3-negative-constraints-steer-past-the-distribution-center)
4. [Few-Shot Examples Beat Verbose Instructions](#4-few-shot-examples-beat-verbose-instructions)
5. [Structure Reduces Ambiguity](#5-structure-reduces-ambiguity)
6. [Progressive Disclosure (Three-Level Loading)](#6-progressive-disclosure-three-level-loading)
7. [The 12 Skill-Design Anti-Patterns (Condensed)](#7-the-12-skill-design-anti-patterns-condensed)
8. [Description Authoring (The Triggering Surface)](#8-description-authoring-the-triggering-surface)
9. [Explaining Why (Rationale in Instructions)](#9-explaining-why-rationale-in-instructions)
10. [Separation of Generation and Evaluation](#10-separation-of-generation-and-evaluation)
11. [Over-Prescription Degrades Current Models](#11-over-prescription-degrades-current-models)
12. [Never Instruct the Model to Echo Its Reasoning](#12-never-instruct-the-model-to-echo-its-reasoning)

---

## 1. Vocabulary Routing

**Mechanism:** Training corpora mix expert content with shallow content, and safe, generic phrasing dominates — so unguided sampling gravitates to that "distribution center" (Anthropic calls this distributional convergence: "Improving frontend design through Skills," Nov 2025). Precise domain vocabulary steers the output register toward expert content instead: "OKLCH tinted neutrals" sets a color-science register; "nice colors" sets a casual-tutorial register. The vocabulary in a skill definition sets the register and framing of what the model generates.

*(The popular "terms activate a knowledge cluster in embedding space" story is a useful working model for explaining this mechanism to others — no published study demonstrates term-to-cluster routing for generative LLM output. Treat it as a mental model, not a finding.)*

**Practical Implication:**
- Include 15-30 precise domain terms in every skill (Forge design standard), organized in 3-5 clusters
- Use the exact terms a 15-year practitioner would use with a peer — but don't maximize jargon: Schreiter (2025) found an optimal *mid-range* of terminology specificity across domains; peer-register precision wins, maximal technicality does not
- Include originator attribution: "circuit breaker (Nygard)" disambiguates the term from unrelated uses of "circuit breaker" elsewhere
- Place vocabulary BEFORE instructions so the routing signal is established before execution begins
- Generic terms ("best practices," "robust solution") route to generic content — they are the distribution center, not a path out of it
- **Bound:** vocabulary steers *register and style*, not factual accuracy — use verification, not vocabulary, for correctness

**The 15-Year Practitioner Test:** For every vocabulary term, ask: would a senior practitioner with 15+ years use this exact term when speaking with a peer? If not, replace it.

| Generic (Avoid) | Expert (Use) | Domain Framing Established |
|---|---|---|
| "separate concerns" | "bounded context (Evans)" | DDD, microservices |
| "handle errors" | "circuit breaker (Nygard)" | Resilience engineering |
| "write good tests" | "mutation testing, property-based testing" | Advanced testing |
| "be secure" | "STRIDE threat modeling (Shostack)" | Application security |
| "deploy safely" | "canary deployment, feature flags" | Progressive delivery |

*Source: Anthropic, "Improving frontend design through Skills" (Nov 2025); Schreiter (2025), arXiv:2505.17037; Impeccable project (Bakaus, 2026)*

---

## 2. U-Shaped Attention Curve

**Mechanism:** Tokens at the beginning and end of context receive disproportionately strong attention. Tokens in the middle receive less. Liu et al. (2024) measured mid-context accuracy drops of more than 20 percentage points versus start-of-context — on GPT-3.5-era models. Current frontier models show much smaller (but nonzero) mid-context degradation, with primacy bias (favoring early content) persisting (Chroma Research, 2025). Wu et al. (2025, MIT/ICML) traced the underlying architectural causes: causal masking and Rotary Position Embedding (RoPE) create inherent decay toward middle positions.

**Practical Implication:**
- Front-load vocabulary payload (highest-priority content at the top)
- Place retrieval anchors ("Questions This Skill Answers") at the end
- Keep SKILL.md under 500 lines so the middle is never too far from either edge
- Behavioral instructions survive the middle because numbered steps and IF/THEN conditions are structurally unambiguous even at reduced attention

**Section Order (Optimized):**
1. YAML frontmatter (triggering surface)
2. Vocabulary payload (primes routing)
3. Anti-pattern watchlist (checked before execution)
4. Behavioral instructions (structured enough to survive middle)
5. Output format
6. Examples (recency bias makes the last example strongest)
7. Questions This Skill Answers (retrieval anchors at the end)

*Source: Liu et al. "Lost in the Middle" (2024); Wu et al. (2025, MIT); Hsieh et al. "Found in the Middle" (ACL 2024)*

---

## 3. Negative Constraints Steer Past the Distribution Center

**Mechanism:** Without constraints, LLM output gravitates to the statistical center of training data — the most generic, average version of any output. Negative constraints ("No Inter font," "No pure black backgrounds") create pressure away from the center, forcing more distinctive output. Anti-patterns are not just guardrails; they are steering mechanisms.

**Practical Implication:**
- Every skill needs both positive instructions (what TO do) and negative constraints (what NOT to do)
- Name anti-patterns with established terms: "Bikeshedding (Parkinson)" activates richer knowledge than "spending too much time on details"
- Include detection signals so the skill proactively identifies problems
- Provide resolution steps — flagging a problem without a fix is noise
- Place anti-patterns BEFORE behavioral instructions so the model checks before acting

**The Full Pattern:** Detect the anti-pattern, Name it, Explain why it's harmful, Resolve with a concrete action, Prevent with a generalizable principle.

*Source: CHI 2023 "Why Johnny Can't Prompt"; Anthropic prompt engineering docs; Impeccable anti-pattern library*

---

## 4. Few-Shot Examples Beat Verbose Instructions

**Mechanism:** LLMs are pattern-matching engines. Input-to-output examples are matched against demonstrated structure more reliably than complex verbal rules are parsed and followed. Anthropic's current docs call examples "one of the most reliable ways to steer Claude's output format, tone, and structure" and recommend 3-5 diverse examples.

**Practical Implication:**
- Include 3-5 diverse examples in every skill (BAD vs GOOD or input-to-output)
- Cover different cases including at least one hard case
- Place the most representative example LAST (recency bias gives it strongest weight — Zhao et al., ICML 2021; a practitioner exploitation heuristic, not a tested prescription)
- Beyond 3-5, returns diminish — LangChain's benchmark found 3 examples roughly matched 9
- Examples communicate implicit quality expectations that words cannot convey
- **Exception:** for pure reasoning tasks, zero-shot is the better default — misaligned examples can degrade reasoning-model performance (DeepSeek-R1, 2025)

**Key Finding:** LangChain (2024) found even naive few-shotting improves tool-calling performance, with gains varying by model; message-formatted examples beat string-formatted ones, with Claude gaining the most. The few-shot dilemma (Tang et al., 2025) found excess examples degrade smaller (~≤8B) models, while frontier models stay stable.

*Source: Anthropic Claude prompting best practices (2026); LangChain few-shot research (2024); Tang et al. (2025), arXiv:2509.13196; Zhao et al. (ICML 2021), arXiv:2102.09690; DeepSeek-R1 (2025)*

---

## 5. Structure Reduces Ambiguity

**Mechanism:** Structural markers (XML tags, Markdown headers, numbered lists) provide explicit boundaries that the model doesn't have to infer — where one section ends and another begins. Anthropic's current docs recommend XML tags specifically to "parse complex prompts unambiguously" when mixing instructions, context, examples, and inputs — a structural-disambiguation recommendation, not a benchmarked win. He et al. (Microsoft, 2024, arXiv:2411.10541) found GPT-3.5-turbo's performance varied up to 40% on a code-translation task from prompt format alone (plain text/Markdown/YAML/JSON — XML was not tested); larger models (GPT-4) were substantially more robust and no single format won universally. No rigorous public XML-vs-Markdown benchmark exists for current Claude models.

**Practical Implication:**
- Use imperative verbs and numbered steps for behavioral instructions
- Use IF/THEN for conditional logic — not "you might want to consider"
- Use YAML/JSON for configuration data, tables for comparisons, bullet points for unordered facts
- Use XML tags or clear Markdown sections to delineate parts of a skill (who the model is, what it should do, output format, what not to do) — for Claude specifically, XML tags are the documented recommendation, though the exact format matters less than having clear delineation at all
- Prose is reserved for reasoning and rationale

**Imperative vs Prose:**
- BAD: "First you should check if there are any anti-patterns, and if so you should probably address them."
- GOOD: "1. Scan input for anti-patterns. IF detected: apply Detect-Name-Explain-Resolve-Prevent. IF none: proceed to step 2."

*Source: Anthropic Claude prompting docs (2026); He et al. (2024), arXiv:2411.10541; Vaarta Analytics (2026)*

---

## 6. Progressive Disclosure (Three-Level Loading)

**Mechanism:** Context is finite, and the evidence shows monotonic degradation with input length, not a U-curve — Chroma's "Context Rot" study (Jul 2025, 18 models) found focused prompts *outperform* full prompts, with no documented penalty for small, well-chosen contexts. Lean is the optimum; there is no "too little" tax. Loading everything at once wastes attention budget on information that may not be relevant to the current task.

**Three Levels:**
1. **Metadata** (~100 tokens, always in context): name + description in YAML frontmatter. This is the triggering surface — it determines whether the skill fires.
2. **SKILL.md body** (<500 lines, loaded when triggered): vocabulary, anti-patterns, instructions, examples. This is the execution surface.
3. **References** (unlimited depth, loaded on demand): pattern libraries, extended examples, checklists, evaluation criteria. Loaded only when a specific subtask needs them.

**Practical Implication:**
- SKILL.md is the router — it tells the model what to do and where to find deeper content
- Heavy content (>300 lines on a single topic) goes in `references/`
- SKILL.md includes clear guidance on WHEN to read each reference file
- For large reference files, include a table of contents
- Platform note (Claude Code, 2026): the skill listing is budgeted at ~1% of the context window, with description + `when_to_use` truncated at 1,536 characters; on auto-compaction each invoked skill retains only its first ~5,000 tokens — front-load what matters

*Source: Anthropic, "Effective Context Engineering for AI Agents" (Sep 2025); Chroma Research, "Context Rot" (Jul 2025); Angular.love skill implementation (30+ reference files)*

---

## 7. The 12 Skill-Design Anti-Patterns (Condensed)

| # | Anti-Pattern | Mechanism of Failure | Fix |
|---|---|---|---|
| 1 | **Consultant-Speak Vocabulary** | Routes to generic advice clusters | Replace with 15-year practitioner terms |
| 2 | **Over-Prompting** | Redundancy wastes attention budget; competing constraints reduce accuracy | State once, imperatively; test minimal first |
| 3 | **Flattery Personas** | "World-renowned expert" adds noise, not knowledge | Define persona through domain knowledge and constraints |
| 4 | **Positive-Only Instructions** | No negative constraints = distribution center output | Add 5-10 anti-patterns with detection + resolution |
| 5 | **Flagging Without Fixing** | Warning without action is noise users learn to ignore | Full Detect-Name-Explain-Resolve-Prevent |
| 6 | **Single-Register Description** | Formal-only undertriggers; casual-only underroutes | Dual-register: expert terms + casual scenarios |
| 7 | **Cross-Conversation Assumptions** | Each conversation is isolated; model has no memory | Provide diversity mechanisms within single conversation |
| 8 | **Edge-Case Stuffing** | Long rule lists create conflicts, waste context | Replace with 3-5 diverse canonical examples |
| 9 | **Paragraph-Form Logic** | Prose is ambiguous; many interpretations possible | Imperative numbered steps with IF/THEN |
| 10 | **Overlapping Skill Boundaries** | Ambiguous triggers = mis-triggers or non-triggers | Explicit exclusions; minimal viable skill set |
| 11 | **Over-Prescription for Current Models** | Rules tuned for older, weaker models can degrade output on Claude 4.5+/5 | Brief goal + boundaries; test removing instructions before adding |
| 12 | **Reasoning-Echo Instructions** | Risks the reasoning-extraction refusal classifier on Fable 5; manual CoT is deprecated | Ask for conclusions with brief stated rationale, never a transcript |

---

## 8. Description Authoring (The Triggering Surface)

The YAML description is the most important tokens in the entire skill — it's what the system matches against user queries. Rules:

1. **Be pushy** — models undertrigger (verified in Anthropic's skill-creator docs). Include synonyms, edge cases, adjacent scenarios.
2. **Dual register** — expert terms for routing + casual phrases for activation.
3. **Explicit exclusions** — "Do NOT use for X" prevents mis-triggers.
4. **Third person, states what AND when** — per Anthropic's authoring best practices.
5. **Test with colloquial queries** — if the skill doesn't fire on "help me with [casual version]," the description needs more casual register.
6. **~100 words** (Forge working target) — long enough for comprehensive coverage, short enough to avoid dilution.

**Platform mechanics (Claude Code, 2026):** SKILL.md follows the Agent Skills open standard (agentskills.io, adopted by 26+ platforms). Custom slash commands have merged into skills. The `when_to_use` field is appended to the description and extends the triggering surface; the combined description + `when_to_use` truncates at 1,536 characters inside a skill-listing budget of ~1% of the context window (least-used skills are dropped first). Description hard limit: 1024 characters. Other useful frontmatter: `disable-model-invocation`, `user-invocable`, `allowed-tools`, `model`, `effort`, `context: fork` (runs the skill in a forked subagent), `paths` (glob-scoped activation).

**Pushy descriptions ≠ heavy bodies.** Undertriggering is real and verified (anthropics/skills skill-creator) — keep descriptions pushy. But on Claude 4.5+/5, over-prescriptive skill *bodies* degrade output (see §11). Push hard on the triggering surface; stay lean in the body.

*Source: anthropics/skills skill-creator docs; Skill Authoring Best Practices (platform.claude.com); Claude Code Skills docs (code.claude.com); Agent Skills Specification (agentskills.io)*

---

## 9. Explaining Why (Rationale in Instructions)

**Mechanism:** A rule without explanation is brittle — it covers only literal matches. A principle with explanation is generalizable. The model has learned from vast reasoning text; when given principle + reasoning, it handles edge cases the rule never anticipated.

**Practical Implication:** For every non-obvious behavioral instruction, include the reasoning. "Place vocabulary BEFORE instructions because it primes the routing signal before execution begins" is generalizable. "Place vocabulary first" is a dead rule.

*Source: Anthropic skill-creator docs*

---

## 10. Separation of Generation and Evaluation

**Mechanism:** When asked to evaluate its own work, a model praises mediocre output. The generator and evaluator share the same biases. Separating them dramatically improves quality.

**Practical Implication:**
- Include evaluation criteria as a separate section
- Use deterministic verification (build, lint, test) as the first quality gate
- Weight evaluation criteria: emphasize dimensions where the model falls short (originality, domain depth) over those it handles well (formatting, coherence)
- Phrase criteria as gradable questions: "Would a domain expert find this actionable?" not "Is it good?"

*Source: Anthropic harness design research (Mar 2026)*

---

## 11. Over-Prescription Degrades Current Models

**Mechanism:** Anthropic's Fable 5 prompting guide is explicit: instruction-following is now strong enough that "a brief instruction steers most behaviors," and skills built for prior, weaker models are often too prescriptive — they can actively degrade output quality, not just waste tokens. Instruction-density research agrees: adherence to individual requirements falls as more are packed into one prompt (What Prompts Don't Say, 2025; IFScale, 2025).

**Practical Implication:**
- State the goal, the boundaries, and the verification standard; let the model fill in competent defaults
- When migrating a skill to a new model generation, test whether REMOVING instructions improves output before adding any
- Keep descriptions pushy (triggering is still the weak point) but keep bodies lean — the two are not in tension, they solve different problems

*Source: Anthropic, "Prompting Claude Fable 5" (2026); Yang et al. (2025), arXiv:2505.13360; Jaroslawicz et al. (2025), arXiv:2507.11538*

---

## 12. Never Instruct the Model to Echo Its Reasoning

**Mechanism:** Two failure modes on current models. (1) Reasoning models already reason internally; manual chain-of-thought scaffolding adds negligible accuracy at real time/token cost (Meincke et al., 2025) and is a deprecated path in Anthropic's docs — adaptive thinking replaced it. (2) On Claude Fable 5, instructing the model to echo or transcribe its internal reasoning ("show your chain of thought," "explain your thinking step by step") can trigger the reasoning-extraction refusal classifier, failing the request outright.

**Practical Implication:**
- Never write instructions like "show your chain of thought" or "transcribe your reasoning process" into a skill
- Ask for conclusions with brief stated rationale instead — a deliverable, not a transcript (e.g., "state the tier and explain why in one sentence")
- When migrating an existing skill to Fable 5, audit it for show-your-thinking instructions and remove them

*Source: Anthropic, "Prompting Claude Fable 5" (2026); Meincke et al. (2025), arXiv:2506.07142*
