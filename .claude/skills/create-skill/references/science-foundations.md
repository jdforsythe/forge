# Science Foundations — Why Skill Design Principles Work

This reference explains the mechanisms behind each design principle. Load this when
you need to explain WHY a recommendation matters, or when the user asks "why should
I do it this way?"

---

## 1. Attention Is Finite (Token Competition)

**Mechanism:** Transformers use self-attention where every token attends to every other
token, creating n² pairwise relationships. As context grows, attention spreads thinner
across exponentially more relationships.

**Implication:** Every token in a skill competes for attention with every other token.
Redundant, verbose, or low-signal tokens consume attention that could go to high-signal
tokens. More tokens does not mean better results — the RIGHT tokens mean better results.

**Evidence:** Vaswani et al. (2017), "Attention Is All You Need"; Chroma Research (2024),
"Context Rot" — recall degrades as context increases; Anthropic (Sep 2025), context
engineering guide describes "attention budget" as a finite resource.

---

## 2. U-Shaped Attention Curve (Lost in the Middle)

**Mechanism:** Tokens at the beginning and end of context receive disproportionately
strong attention. Tokens in the middle receive less. This is caused by causal masking
and Rotary Position Embedding (RoPE) creating inherent decay toward middle positions.

**Measured impact:** 30%+ accuracy drop when target information is in the middle vs
position 1 or 20 (Liu et al., 2024). Placing queries/instructions at the end improves
response quality by up to 30% in Anthropic's tests.

**Implication for skills:**
- Front-load the most important content: vocabulary payload, critical instructions
- Place retrieval anchors at the end: "Questions This Skill Answers"
- Keep SKILL.md under 500 lines so the middle isn't too far from either edge
- Structured content (numbered steps, conditions) survives middle-position degradation
  better than prose

**Evidence:** Liu et al. (2024), "Lost in the Middle" (TACL); Hsieh et al. (2024),
"Found in the Middle" (ACL); Wu et al. (2025), MIT/IDSS; Anthropic Claude 4 docs.

---

## 3. Vocabulary Routes to Knowledge Clusters (Embedding Space Routing)

**Mechanism:** Expert content and shallow content coexist in training data. Expert content
uses precise terminology; shallow content uses generic terms. When the model encounters
specific vocabulary, it activates knowledge clusters near that vocabulary in embedding
space. The prompt vocabulary acts as a query into the model's distributed knowledge.

**Measured impact:** "OKLCH tinted neutrals with warm base hue" routes to perceptual
color science. "Nice colors" routes to blog posts. The difference is not subtle — it
determines whether the model draws from expert papers or introductory tutorials.

**Implication for skills:** The vocabulary in a skill is the single highest-leverage
intervention. The model already knows how to do most things at an expert level — it
just needs the right vocabulary to route to that knowledge. 15-30 expert terms,
appearing BEFORE behavioral instructions, prime the model for expert-level execution.

**Evidence:** Ranjan et al. (2024), "One Word Is Not Enough"; IBM Prompt Engineering
docs; Paul Bakaus / Impeccable project (2026); Anthropic frontend-design skill.

---

## 4. Negative Constraints Push Past the Distribution Center

**Mechanism:** Without constraints, the model's most probable output is the most
statistically average one — the center of the training distribution. Negative
constraints ("No pure black backgrounds," "No Inter font") create pressure away from
this center, forcing output into more distinctive, expert-level territory.

**Nuance:** "Do not X" alone is weaker than "Do Y instead" (CHI 2023). But for system
prompts and skill definitions, combining positive instruction with negative constraint
is most effective. Anti-patterns are not just guardrails — they are steering mechanisms.

**Implication for skills:** Every skill needs both what TO do (positive instructions
with expert vocabulary) and what NOT to do (named anti-patterns with alternatives).
Negative constraints alone are weak. Positive + negative together are strongest.

**Evidence:** Anthropic prompt engineering docs; CHI 2023, "Why Johnny Can't Prompt";
Impeccable anti-pattern library; OpenAI prompt engineering guide.

---

## 5. Few-Shot Examples Beat Verbose Instructions

**Mechanism:** LLMs are pattern-matching engines. Input-output examples allow direct
pattern matching against demonstrated structure, which is more reliable than following
complex verbal rules. Examples communicate implicit quality expectations that words
alone cannot convey.

**Key findings:**
- 2-3 examples often match 9 in effectiveness (diminishing returns)
- Example format matters as much as content — structure the pairs clearly
- Recency bias: the model weights the last example most heavily
- Excessive examples can degrade performance through context overflow

**Implication for skills:** Include 2-3 diverse BAD vs GOOD pairs. Place the most
representative example last. Don't stuff edge cases — show the pattern, let the model
generalize.

**Evidence:** LangChain few-shot research (2024); Wei et al. (2022), Chain-of-Thought;
Anthropic context engineering guide; Anthropic Claude 4 best practices.

---

## 6. Structure Reduces Ambiguity

**Mechanism:** Structural markers (XML tags, Markdown headers, numbered sections)
provide explicit boundaries that the model doesn't have to infer. Tokenization can
check whether a tag has been closed and when a context item is complete. Performance
varies up to 40% based on prompt format alone.

**Implication for skills:** Use clear section delineation. For Claude specifically,
XML tags are preferred for complex prompts. Separate: who the model is, what it should
do, how to format output, and what not to do.

**Evidence:** Anthropic Claude docs (Claude tuned for structure); Voyce (2025),
comparative XML/Markdown study; Anthropic context engineering guide.

---

## 7. Explaining "Why" Generalizes Better Than Rules Alone

**Mechanism:** When the model understands reasoning behind a rule, it can apply the
principle to novel situations the rule doesn't cover. A rule without explanation is
brittle — it covers exactly the listed cases. A principle with explanation is
generalizable, because the model can draw on related knowledge for edge cases.

**Anthropic's own guidance:** "Try to explain to the model why things are important
in lieu of heavy-handed musty MUSTs."

**Implication for skills:** For every important behavioral instruction, include the
reasoning. "Prefer imperative form because it's unambiguous and reduces token count"
gives a heuristic for novel situations. "Prefer imperative form" alone is a dead rule.

**Evidence:** Anthropic skill-creator docs; Anthropic context engineering guide.

---

## 8. Self-Evaluation Fails — Separate Generation from Evaluation

**Mechanism:** When asked to evaluate its own work, the model confidently praises
mediocre output because the generator has the same biases as the evaluator.

**Implication for skills:** Include evaluation criteria as a separate component loaded
AFTER generating output. Deterministic verification (build, lint, test) is the first
quality gate. LLM evaluation with weighted criteria is the second gate. Weight criteria
toward dimensions where the model typically falls short (originality, domain depth)
over dimensions it handles well (technical correctness, formatting).

**Evidence:** Anthropic (Mar 2026), "Harness Design for Long-Running Application
Development"; Angular.love official skill implementation.

---

## 9. No Cross-Conversation Memory

**Mechanism:** Each conversation is completely isolated. Zero memory of previous
conversations. Instructions like "never repeat yourself across sessions" or "don't
converge on common choices" are dead instructions — they require information the model
does not have.

**Implication for skills:** Every instruction must be achievable within a single
conversation's context. For diversity, provide diversity-forcing mechanisms:
randomization instructions, style catalogs to draw from, explicit variety in examples.

**Evidence:** Justin Wetch (Jan 2026), "Teaching Claude to Design Better."
