# Skill Design Principles: Condensed Research

> Reference for the Skill Creator. Each principle includes the mechanism (WHY it works) and the practical implication. Read this file when you need to explain a design decision or when a user asks why a skill should be structured a certain way.

---

## 1. Vocabulary Routing

**Mechanism:** LLMs organize knowledge in embedding space clusters. When the prompt contains precise terminology, the model activates the knowledge cluster nearest to those terms. "OKLCH tinted neutrals" routes to color science. "Nice colors" routes to blog posts. The vocabulary in a skill definition is literally a query into the model's distributed knowledge.

**Practical Implication:**
- Include 15-30 precise domain terms in every skill, organized in 3-5 clusters
- Use the exact terms a 15-year practitioner would use with a peer
- Include originator attribution: "circuit breaker (Nygard)" activates more specific knowledge than "circuit breaker" alone
- Place vocabulary BEFORE instructions so routing is established before execution begins
- Generic terms ("best practices," "robust solution") route to generic content. They are actively harmful.

**The 15-Year Practitioner Test:** For every vocabulary term, ask: would a senior practitioner with 15+ years use this exact term when speaking with a peer? If not, replace it.

| Generic (Avoid) | Expert (Use) | Cluster Activated |
|---|---|---|
| "separate concerns" | "bounded context (Evans)" | DDD, microservices |
| "handle errors" | "circuit breaker (Nygard)" | Resilience engineering |
| "write good tests" | "mutation testing, property-based testing" | Advanced testing |
| "be secure" | "STRIDE threat modeling (Shostack)" | Application security |
| "deploy safely" | "canary deployment, feature flags" | Progressive delivery |

*Source: Ranjan et al. (2024); Anthropic context engineering guide; Impeccable project*

---

## 2. U-Shaped Attention Curve

**Mechanism:** Tokens at the beginning and end of context receive disproportionately strong attention. Tokens in the middle receive less. Liu et al. (2024) measured 30%+ accuracy drops when target information was in the middle versus at position 1 or 20. Wu et al. (2025) traced this to architectural causes: causal masking and Rotary Position Embedding (RoPE).

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

**Mechanism:** LLMs are pattern-matching engines. Input-to-output examples are matched against demonstrated structure more reliably than complex verbal rules are parsed and followed. Research consistently shows 2-3 well-chosen examples outperform paragraphs of instruction.

**Practical Implication:**
- Include 2-3 diverse examples in every skill (BAD vs GOOD or input-to-output)
- Cover different cases including at least one hard case
- Place the most representative example LAST (recency bias gives it strongest weight)
- Don't exceed 3 examples — diminishing returns, and excess examples can cause context overflow
- Examples communicate implicit quality expectations that words cannot convey

**Key Finding:** LangChain (2024) found 3 examples often match 9 in effectiveness. The format of examples matters as much as the content.

*Source: Anthropic context engineering guide; LangChain few-shot research (2024); Wei et al. CoT (2022)*

---

## 5. Structure Reduces Ambiguity

**Mechanism:** Structural markers (XML tags, Markdown headers, numbered lists) provide explicit boundaries that the model doesn't have to infer. Unstructured prose forces the model to guess where instructions end and context begins. A comparative study found model performance varies up to 40% based on prompt format alone.

**Practical Implication:**
- Use imperative verbs and numbered steps for behavioral instructions
- Use IF/THEN for conditional logic — not "you might want to consider"
- Use YAML/JSON for configuration data, tables for comparisons, bullet points for unordered facts
- Prose is reserved for reasoning and rationale
- Separate sections clearly: who the model is, what it should do, output format, what not to do

**Imperative vs Prose:**
- BAD: "First you should check if there are any anti-patterns, and if so you should probably address them."
- GOOD: "1. Scan input for anti-patterns. IF detected: apply Detect-Name-Explain-Resolve-Prevent. IF none: proceed to step 2."

*Source: Anthropic Claude docs; He et al. (2025) comparative study; Vaarta Analytics (2026)*

---

## 6. Progressive Disclosure (Three-Level Loading)

**Mechanism:** Context is finite, and performance follows an inverted-U: optimal at 15-40% window utilization, degraded above 60%. Loading everything at once wastes attention budget on currently-irrelevant information.

**Three Levels:**
1. **Metadata** (~100 tokens, always in context): name + description in YAML frontmatter. This is the triggering surface — it determines whether the skill fires.
2. **SKILL.md body** (<500 lines, loaded when triggered): vocabulary, anti-patterns, instructions, examples. This is the execution surface.
3. **References** (unlimited depth, loaded on demand): pattern libraries, extended examples, checklists, evaluation criteria. Loaded only when a specific subtask needs them.

**Practical Implication:**
- SKILL.md is the router — it tells the model what to do and where to find deeper content
- Heavy content (>300 lines on a single topic) goes in `references/`
- SKILL.md includes clear guidance on WHEN to read each reference file
- For large reference files, include a table of contents

*Source: Anthropic context engineering guide; Angular.love skill implementation (30+ reference files)*

---

## 7. The 10 Skill-Design Anti-Patterns (Condensed)

| # | Anti-Pattern | Mechanism of Failure | Fix |
|---|---|---|---|
| 1 | **Consultant-Speak Vocabulary** | Routes to generic advice clusters | Replace with 15-year practitioner terms |
| 2 | **Over-Prompting** | Redundancy wastes attention budget; competing constraints reduce accuracy | State once, imperatively; test minimal first |
| 3 | **Flattery Personas** | "World-renowned expert" adds noise, not knowledge | Define persona through domain knowledge and constraints |
| 4 | **Positive-Only Instructions** | No negative constraints = distribution center output | Add 5-10 anti-patterns with detection + resolution |
| 5 | **Flagging Without Fixing** | Warning without action is noise users learn to ignore | Full Detect-Name-Explain-Resolve-Prevent |
| 6 | **Single-Register Description** | Formal-only undertriggers; casual-only underroutes | Dual-register: expert terms + casual scenarios |
| 7 | **Cross-Conversation Assumptions** | Each conversation is isolated; model has no memory | Provide diversity mechanisms within single conversation |
| 8 | **Edge-Case Stuffing** | Long rule lists create conflicts, waste context | Replace with 2-3 diverse canonical examples |
| 9 | **Paragraph-Form Logic** | Prose is ambiguous; many interpretations possible | Imperative numbered steps with IF/THEN |
| 10 | **Overlapping Skill Boundaries** | Ambiguous triggers = mis-triggers or non-triggers | Explicit exclusions; minimal viable skill set |

---

## 8. Description Authoring (The Triggering Surface)

The ~100 words of YAML description are the most important tokens in the entire skill. Rules:

1. **Be pushy** — models undertrigger. Include synonyms, edge cases, adjacent scenarios.
2. **Dual register** — expert terms for routing + casual phrases for activation.
3. **Explicit exclusions** — "Do NOT use for X" prevents mis-triggers.
4. **Test with colloquial queries** — if the skill doesn't fire on "help me with [casual version]," the description needs more casual register.
5. **~100 words** — long enough for comprehensive coverage, short enough to avoid dilution.

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
