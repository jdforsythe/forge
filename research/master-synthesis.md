# Skill & Agent Design: Research Synthesis
## The Science of What Works, Why It Works, and What Doesn't

*Comprehensive synthesis for building a meta-skill that creates high-quality Claude Code skills and agent definitions.*

---

## How to Use This Document

This document is structured as a reference for an agent that will create skills. It covers:

1. **The Science** — How LLMs actually work at the architectural level, and why certain prompting strategies succeed or fail
2. **The DOs** — Evidence-based practices with explanations of the underlying mechanism
3. **The DON'Ts** — Anti-patterns with explanations of why they fail
4. **The Architecture** — How to structure a skill file for maximum effectiveness
5. **The Evaluation** — How to test and iterate

Every principle is grounded in either peer-reviewed research, Anthropic's own engineering documentation, or documented practitioner evidence.

---

## PART 1: THE SCIENCE — How LLMs Process Prompts

Understanding the transformer architecture explains *why* specific prompting strategies work. These aren't arbitrary best practices — they're logical consequences of how the model processes tokens.

### 1.1 The Attention Mechanism and Why Context Is Finite

**How it works:** LLMs are built on the transformer architecture (Vaswani et al., 2017). The core mechanism is self-attention: every token in the context can "attend to" every other token, creating n² pairwise relationships for n tokens. This is what allows the model to understand relationships between distant parts of the input.

**Why it matters for skills:** As context length grows, the model's attention gets spread thinner across exponentially more relationships. Anthropic's context engineering guide (Sep 2025) describes this as an "attention budget" — a finite resource that depletes as tokens are added. The practical implication: every token in a skill definition competes for attention with every other token. Redundant, verbose, or low-signal tokens actively degrade performance by consuming attention that could be allocated to high-signal tokens.

**The takeaway:** Good skill design is fundamentally about maximizing signal-to-noise ratio in the tokens you put into context. More tokens ≠ better results. The right tokens = better results.

*Sources: Vaswani et al., "Attention Is All You Need" (2017); Anthropic, "Effective Context Engineering for AI Agents" (Sep 2025); Chroma Research, "Context Rot" (2024)*

### 1.2 The U-Shaped Attention Curve ("Lost in the Middle")

**How it works:** LLMs exhibit a well-documented positional bias: tokens at the beginning and end of the context receive disproportionately strong attention, while tokens in the middle receive less. This creates a U-shaped attention curve. Liu et al. (2024) measured 30%+ accuracy drops on multi-document QA when the answer document was in the middle of context versus at position 1 or 20. MIT researchers (Wu et al., 2025) traced this to architectural causes: causal masking and Rotary Position Embedding (RoPE) create inherent decay toward middle positions.

**Why it matters for skills:** The position of information within a skill definition affects how strongly the model attends to it. Critical instructions placed in the middle of a long skill file will receive less attention than those at the beginning or end.

**The takeaway:**
- **Front-load the most important content** — vocabulary payload, critical behavioral instructions, and anti-pattern watchlists should appear early in the SKILL.md
- **Place retrieval anchors at the end** — "Questions This Skill Answers" sections work well at the bottom
- **Keep SKILL.md short enough** that the middle doesn't get too far from either edge — under 500 lines is the practical threshold
- Anthropic's own prompting docs confirm: placing queries/instructions at the end of long contexts improves response quality by up to 30% in tests

*Sources: Liu et al. (2024), "Lost in the Middle"; Hsieh et al. (2024), "Found in the Middle" (ACL 2024); Wu et al. (2025), MIT/IDSS; Anthropic Claude 4 best practices docs*

### 1.3 Vocabulary as a Routing Signal in Embedding Space

**How it works:** LLMs are trained on massive corpora where expert content and shallow content coexist. Expert content uses precise terminology; shallow content uses generic terms. When the model encounters specific vocabulary in the prompt, it activates knowledge clusters near that vocabulary in its learned embedding space. "OKLCH tinted neutrals" routes to color science papers and design system docs. "Nice colors" routes to blog posts and casual tutorials.

**Why it matters for skills:** The vocabulary in a skill definition determines which "region" of the model's learned knowledge gets activated. This isn't metaphorical — it's a direct consequence of how embeddings and attention work. The prompt vocabulary acts as a query into the model's distributed knowledge, and precise queries retrieve precise knowledge.

**The takeaway:** Skills must contain the exact terminology that domain experts use. This is the single highest-leverage intervention in skill quality. The model already knows how to do most things at an expert level — it just needs the right vocabulary to route to that knowledge.

*Sources: Ranjan et al. (2024), "One Word Is Not Enough"; IBM Prompt Engineering documentation; Paul Bakaus / Impeccable project (2026); Anthropic frontend-design skill*

### 1.4 Why Negative Constraints Push Past the Distribution Center

**How it works:** LLMs are trained on next-token prediction over massive corpora. The most probable next token is the most statistically average one — the center of the training distribution. Without constraints, the model's output gravitates toward this center: the most generic, common version of any output. "No Inter font" forces a non-default font choice. "No pure black backgrounds" forces tinted neutrals. Negative constraints create pressure away from the distribution center, pushing output toward more distinctive, expert-level territory.

**Why it matters for skills:** Without explicit anti-patterns and negative examples, the model will produce the most average version of whatever you asked for. Anti-patterns are not just guardrails — they are steering mechanisms that force the model into more distinctive output space.

**Research nuance:** The CHI 2023 paper "Why Johnny Can't Prompt" found that non-experts overwhelmingly use "Do not X" phrasing, which is less effective than "Do Y instead." However, for system prompts and skill definitions (not user-facing conversation), combining positive instruction with negative constraint is most effective. Anthropic's own prompting docs recommend "using positive and negative examples" together.

**The takeaway:** Every skill needs both: what TO do (positive instruction with expert vocabulary) and what NOT to do (named anti-patterns with specific alternatives). Negative constraints alone are weak. Positive + negative together are strongest.

*Sources: Anthropic prompt engineering docs; CHI 2023, "Why Johnny Can't Prompt"; Impeccable anti-pattern library; OpenAI prompt engineering guide*

### 1.5 Why Few-Shot Examples Beat Verbose Instructions

**How it works:** LLMs are fundamentally pattern-matching engines trained on sequence prediction. When shown input→output examples, the model can pattern-match against the demonstrated structure far more reliably than it can follow complex verbal rules. Research consistently shows that 2-3 well-chosen examples outperform paragraphs of instructions for most tasks.

**Key findings from research:**
- LangChain experiments (2024) found that even naive few-shotting improves tool-calling performance, and 3 examples often match 9 in effectiveness (diminishing returns)
- The "few-shot dilemma" (2025) found that excessive examples can actually degrade performance in smaller models due to context overflow, but frontier models handle it well
- Example format matters as much as content — how you structure the input→output pairs affects performance differently per model
- Recency bias: LLMs weight the last example more heavily, so place the most representative example last

**The takeaway:** Skills should include 2-3 diverse, canonical examples showing input→output pairs. These examples communicate implicit quality expectations more effectively than any amount of verbal instruction. Don't stuff edge cases — show the pattern, then let the model generalize.

*Sources: Anthropic, "Effective Context Engineering for AI Agents"; LangChain few-shot research (2024); Wei et al. (2022), Chain-of-Thought Prompting; Anthropic Claude 4 best practices*

### 1.6 Why Structure Reduces Ambiguity (XML Tags, Sections)

**How it works:** Tokenization breaks text into subword units. When the model encounters unstructured prose, it must infer where instructions end and context begins, where one section stops and another starts. Structural markers (XML tags, Markdown headers, labeled sections) provide explicit boundaries that the model doesn't have to infer. XML tags provide multi-line certainty with delimiters that mark where items begin and end — tokenization can check whether a tag has been closed and when that particular item of context should be considered complete.

**Key findings:**
- Anthropic specifically recommends XML tags for structuring prompts: "Claude has been specifically tuned to pay special attention to your structure"
- A comparative study found model performance can vary up to 40% based on prompt format alone
- XML is the only structured format recommended by all three major providers (Anthropic, OpenAI, Google)
- XML outperforms Markdown for complex prompts across most models, though it uses ~15% more tokens

**The takeaway:** Use XML tags or clear Markdown sections to delineate different parts of a skill definition. The exact format matters less than having clear delineation. For Claude specifically, XML tags are preferred. Separate: who the model is, what it should do, how to format output, and what not to do.

*Sources: Anthropic Claude docs; Voyce (2025), comparative XML/Markdown study; Anthropic, "Effective Context Engineering for AI Agents"*

### 1.7 Why Explaining "Why" Works Better Than Rules Alone

**How it works:** When the model understands the reasoning behind a rule, it can apply the principle to novel situations that the rule doesn't explicitly cover. A rule without explanation is brittle — it covers exactly the cases listed. A principle with explanation is generalizable. Anthropic's own skill-creator documentation advises: "try to explain to the model why things are important in lieu of heavy-handed musty MUSTs."

**The mechanism:** The model has learned from vast amounts of human reasoning text. When given a principle + explanation, it can draw on related knowledge to handle edge cases. When given only a rule, it can only pattern-match against the literal rule.

**The takeaway:** For every important behavioral instruction in a skill, include the reasoning. "Prefer imperative form in instructions because it's unambiguous and reduces token count" gives the model a heuristic for novel situations. "Prefer imperative form" alone is a dead rule that covers only the cases where the model happens to remember it.

*Sources: Anthropic skill-creator docs; Anthropic, "Effective Context Engineering for AI Agents"*

### 1.8 Why Self-Evaluation Fails and Separation Matters

**How it works:** When asked to evaluate its own work, a model confidently praises mediocre output. This is a well-documented failure mode — the model that generated the output has the same biases and blind spots when evaluating it. Anthropic's harness design research (Mar 2026) found that separating generation from evaluation dramatically improves quality.

**The takeaway:** Skills should include evaluation criteria as a separate component that the model reads AFTER generating output. Deterministic verification (build, lint, test) should be the first quality gate; LLM evaluation is the second gate for subjective qualities. Weight the evaluation criteria — emphasize dimensions where the model naturally falls short (originality, design coherence) over dimensions it handles well by default (technical correctness, craft).

*Sources: Anthropic, "Harness Design for Long-Running Application Development" (Mar 2026); Angular.love official skill implementation*

### 1.9 The Model Can't See Across Conversations

**How it works:** Each conversation is completely isolated. The model has zero memory of what it generated in other conversations. Instructions like "never repeat yourself across sessions" or "don't converge on common choices" are dead instructions — they require knowledge the model doesn't have.

**The takeaway:** Every instruction in a skill must be achievable within a single conversation's context. If you want diversity, provide diversity-forcing mechanisms: randomization instructions, style catalogs to draw from, explicit variety in examples.

*Sources: Justin Wetch, "Teaching Claude to Design Better" (Jan 2026)*

---

## PART 2: THE DOs — Evidence-Based Practices

Each practice is listed with the underlying mechanism that makes it work.

### 2.1 DO: Use Expert Vocabulary as the Primary Quality Lever

**What:** Include 15-30 precise domain terms that a senior practitioner with 15+ years of experience would use when discussing the skill's domain with a peer.

**Why it works:** Vocabulary is the routing signal that determines which knowledge cluster the model draws from (see §1.3). Expert vocabulary routes to expert content in embedding space.

**How:**
- Use named frameworks with originators: "task-relevant maturity (Andy Grove, High Output Management)" — not "delegation framework"
- Use compound, specific terms: "blast radius assessment" — not "impact analysis"
- Include the vocabulary in a dedicated section (Expert Vocabulary Payload) BEFORE behavioral instructions, so the routing signal is established before execution begins
- Cover the full range of the domain, not just one narrow aspect

**Evidence:**
| Non-expert prompt | Expert prompt | Mechanism |
|---|---|---|
| "nice colors" | "OKLCH tinted neutrals with warm base hue" | Routes to perceptual color science cluster |
| "plan the project" | "decompose into vertically-sliced work packages with INVEST criteria" | Routes to work breakdown expertise |
| "good API" | "RESTful resource design with idempotent operations, HATEOAS links, semantic versioning" | Routes to API design expertise |

### 2.2 DO: Build Dual-Register Descriptions (Formal + Colloquial)

**What:** Every skill description must be discoverable via two linguistic registers: expert terminology (for activating deep knowledge) and natural language (for triggering when users ask in plain language).

**Why it works:** The skill description is a retrieval surface — it's what the system matches against user queries. If it only contains expert terms, it won't trigger when users say "help me figure out what to do." If it only contains casual language, it triggers but produces generic output because no expert routing signal is present.

**How:**
- For every formal trigger phrase, write 2-3 colloquial equivalents in the description
- Include a "Questions This Skill Answers" section with 8-15 natural-language queries using the exact phrasing a user would type
- The description should read naturally to a human while containing enough expert terms to activate deep knowledge

**Example:**
```
WEAK (single register): "Facilitates architectural decision records using RACI matrices and Cynefin framework categorization."

STRONG (dual register): "Facilitates architectural decision records using structured frameworks (RACI, Cynefin, weighted decision matrices). Use this when the user is stuck choosing between approaches, says 'should we use X or Y,' needs help deciding, or is going back and forth on a technical or product question."
```

**Evidence:** Anthropic's skill-creator docs note that skills tend to "undertrigger" — they don't fire when they should. Dual-register descriptions combat this. The vocabulary gap problem is well-documented in RAG research: when query terms differ from document terms, retrieval fails.

### 2.3 DO: Include Named Anti-Patterns with Detection → Resolution

**What:** Every skill should include 5-10 named anti-patterns from the domain, each with: name + origin, detection signal, resolution step, prevention principle.

**Why it works:** Three mechanisms are at play:
1. Named anti-patterns are routing signals that activate the same expert knowledge clusters as positive vocabulary (see §1.3)
2. Negative constraints push the model past the distribution center toward more distinctive output (see §1.4)
3. Detection signals make the skill proactive — it doesn't wait for the user to ask "am I doing something wrong?" but notices patterns and intervenes

**How:**
- Give every anti-pattern its established name and origin: "Bikeshedding (Parkinson's Law of Triviality)" — not "spending too much time on unimportant things"
- Include a detection signal: what in the user's input indicates this pattern is active
- Include a resolution step: what to do instead, with a concrete action
- Include a prevention principle: the generalizable heuristic that prevents recurrence
- List anti-patterns before behavioral instructions so the model checks for them first

### 2.4 DO: Use Imperative, Structured, Conditional Instructions

**What:** Write behavioral instructions as ordered steps using imperative verbs with explicit conditions, not prose paragraphs.

**Why it works:** Vaarta Analytics (2026) found that converting free-text instructions to structured, atomic binary checks "dramatically reduced false negatives and hallucinated justifications." Numbered steps with conditions produce more reliable execution than prose descriptions of the same behavior because they reduce ambiguity — there's exactly one way to interpret "Classify the decision tier" versus many ways to interpret "You should try to figure out the tier."

**How:**
```
1. Scan the user's input for anti-patterns from the watchlist.
   IF detected: Apply Detect → Name → Explain → Resolve → Prevent.
   IF not detected: Proceed to step 2.
2. Classify the decision tier based on reversibility and blast radius.
   OUTPUT: State the tier and explain why in one sentence.
   IF tier cannot be determined: Ask one clarifying question.
```

**NOT:**
```
First you should check if there are any anti-patterns, and if so you should probably address them. Then try to figure out the decision tier if you can, but if it's not clear you might want to ask a question.
```

### 2.5 DO: Include 2-3 Diverse, Canonical Examples

**What:** Show input→output pairs that demonstrate the desired pattern, covering different cases including at least one hard case.

**Why it works:** LLMs pattern-match against examples more reliably than they follow complex verbal rules (see §1.5). Examples communicate implicit quality expectations that words alone cannot convey.

**How:**
- Include examples as BAD vs GOOD pairs when showing quality standards
- Include examples as input→output pairs when showing workflow
- Cover diverse cases, not just the happy path
- Place the most representative/important example last (recency bias)
- 2-3 examples are the sweet spot; diminishing returns after that

**Example of a quality-standard pair:**
```
BAD OUTPUT (generic, shallow):
"You should prioritize your tasks and focus on what's most important."

GOOD OUTPUT (expert, specific):
"Apply WSJF — weighted shortest job first. Calculate the cost of delay
divided by job duration for each item. The highest WSJF score gets
done first. For your situation, the API auth decision has the highest
cost of delay because it's blocking 3 downstream work packages."
```

### 2.6 DO: Use Progressive Disclosure (Three-Level Loading)

**What:** Structure skills in three layers: metadata (always in context, ~100 tokens), SKILL.md body (loaded when triggered, <500 lines), and bundled references (loaded on demand, unlimited depth).

**Why it works:** Context is a finite resource with diminishing returns (see §1.1). Loading everything into context at once wastes the attention budget on information that may not be relevant to the current task. Progressive disclosure keeps the active context focused on what matters right now.

**How:**
- SKILL.md is the router — it tells the model what to do and where to find deeper reference
- Keep SKILL.md under 500 lines
- Heavy content (token inventories, pattern libraries, extended examples) goes in `references/` directory
- SKILL.md includes clear guidance on WHEN to read each reference file
- For large reference files (>300 lines), include a table of contents

**Evidence:** The Angular official skill handles 30+ reference files without context bloat using this pattern. Anthropic's context engineering guide specifically recommends this approach.

### 2.7 DO: Make Descriptions "Pushy" About Triggering

**What:** Skill descriptions should aggressively claim territory — include both what the skill does AND specific contexts, synonyms, edge cases, and adjacent scenarios.

**Why it works:** Anthropic's skill-creator docs note that current models tend to "undertrigger" — they don't use skills when they'd be useful. The model decides whether to consult a skill based on the description in the `available_skills` list, and it errs on the side of not triggering.

**How:**
```
WEAK: "Build dashboards to display data."

STRONG: "Build dashboards, analytics views, KPI displays, metric compositions,
and data overview screens. Use this skill whenever the user mentions dashboards,
analytics, metrics, KPIs, charts, reporting views, executive summaries, or wants
to display aggregated data in a visual layout — even if they don't explicitly
ask for a 'dashboard.'"
```

- Include both the domain AND the user scenarios
- Include explicit exclusions: "Do NOT use for implementation-level coding decisions that a developer can make autonomously"
- Test trigger reliability with colloquial queries that don't use any formal vocabulary from the description

### 2.8 DO: Read Project Context from the Filesystem

**What:** Skills should read project-specific context (CLAUDE.md, config files, existing code conventions) rather than defaulting to generic patterns.

**Why it works:** Models default to safe, average choices because they have no context about your specific project (see §1.4 — the distribution center problem). Providing project-specific context — actual CSS variables, actual component selectors, actual conventions — eliminates the need for the model to guess and forces it to use your patterns.

**Evidence:** One developer reported Claude immediately switched from generating Angular modules to standalone components just from documenting conventions in CLAUDE.md. The Impeccable project's `.impeccable.md` pattern gathers target audience, use cases, and brand personality before any design work, and this single intervention dramatically improved output quality.

### 2.9 DO: Separate Generation from Evaluation

**What:** Include evaluation criteria in each skill as a separate section or reference file, with weighted criteria phrased as gradable questions.

**Why it works:** Self-evaluation fails because the generator has the same biases as the evaluator (see §1.8). Deterministic verification tools (compilers, linters, test runners) should be the first quality gate.

**How:**
- Define evaluation criteria specific to the skill's domain
- Weight criteria by importance — emphasize dimensions where the model typically falls short
- Phrase criteria as concrete questions: "Can the user identify the most important metric in <3 seconds?" not "Is the design good?"
- Include deterministic verification steps (build, lint, test) before subjective evaluation
- Calibrate the evaluator with examples if possible

### 2.10 DO: Prompt at the Right Altitude

**What:** Strike the Goldilocks zone between over-specified brittle logic and under-specified vague guidance.

**Why it works:** Over-specification creates fragility — hardcoded logic that breaks on edge cases. Under-specification leaves the model guessing. The right altitude names things precisely (tokens, patterns, frameworks) without dictating every decision.

**How:**
- "Use `var(--spacing-md)` for standard element gaps" — names the token (specific) without dictating where to apply it (flexible)
- Start minimal, test, add detail only where the model fails
- Elastic Agent Builder (2025) advises: "Begin with clarity — use unambiguous instructions. Only add granular logic if the model fails a specific use case during testing."

---

## PART 3: THE DON'Ts — Anti-Patterns in Skill Design

Each anti-pattern is listed with the mechanism that makes it fail.

### 3.1 DON'T: Use Generic Consultant-Speak

**What fails:** "best practices," "leverage," "synergy," "alignment," "holistic approach," "digital transformation," "paradigm shift," "strategic initiative"

**Why it fails:** These terms are so overused in training data that they route to the generic advice center of the distribution — blog posts, introductory articles, and consultant slide decks. They are the opposite of expert routing signals.

**Instead:** Use the precise term for what you mean. "Kanban board with WIP limits" instead of "a way to organize work." "STRIDE threat modeling" instead of "making sure it's secure."

### 3.2 DON'T: Over-Prompt with Redundant Instructions

**What fails:** A 2000-word skill definition that restates the same instruction 4 different ways "for emphasis."

**Why it fails:** Two mechanisms:
1. Redundancy consumes attention budget (see §1.1) — the model spends capacity processing repeated instructions instead of unique ones
2. Over-specification can actually confuse high-reasoning models by implying the task is more complex than it is
3. The underspecification research (2025) found that simply adding more requirements to a prompt does not reliably improve performance due to competing constraints — at n=19 requirements, average accuracy was LOWER than at n=5

**Instead:** State each instruction once, clearly, in imperative form. Add detail only for genuinely complex conditional logic. Test with a minimal prompt first and add specificity only where the model fails.

### 3.3 DON'T: Use Flattery-Based Persona Definitions

**What fails:** "You are a world-renowned expert who never makes mistakes and always provides the best advice."

**Why it fails:** DigitalOcean's guide specifically notes that "heavy-handed role assignments may actually backfire by limiting helpfulness." Flattery-based personas don't activate different knowledge — they just add noise. The model's output quality is determined by the vocabulary and structure of the instructions, not by whether you called it "world-class."

**Instead:** Define the persona through domain knowledge, frameworks, and behavioral constraints. "You are an engineering director experienced in delivering B2B SaaS products with small teams under deadline pressure" provides useful context. "You are the best engineering director in the world" does not.

### 3.4 DON'T: Write Only Positive Instructions (No Anti-Patterns)

**What fails:** A skill that describes what to do but never what NOT to do.

**Why it fails:** Without negative constraints, the model gravitates toward the distribution center (see §1.4). Without anti-pattern detection, the skill has no mechanism to notice when the user is heading toward a known failure mode. The model will happily help the user walk off a cliff.

**Instead:** Every skill includes an anti-pattern watchlist with detection signals, and the behavioral instructions include an anti-pattern scan step before proceeding with normal execution.

### 3.5 DON'T: Flag Problems Without Fixes

**What fails:** "Warning: this may be bikeshedding."

**Why it fails:** A flag without a resolution is noise. The user knows something is wrong but doesn't know what to do about it. Over time, they learn to ignore the flags.

**Instead:** Full Detect → Name → Explain → Resolve → Prevent pattern. Every flag comes with a concrete action and a generalizable prevention principle.

### 3.6 DON'T: Write Single-Register Descriptions

**What fails:** A description written entirely in formal terminology OR entirely in casual language.

**Why it fails:** Formal-only descriptions don't trigger when users ask in plain language. Casual-only descriptions trigger but produce generic output because no expert vocabulary routing signal is present. Both modes fail — just in different ways.

**Instead:** Dual-register descriptions that read naturally to a human while containing expert terms (see §2.2).

### 3.7 DON'T: Assume Cross-Conversation Memory

**What fails:** "Never converge on common choices across generations" or "Don't repeat designs from previous sessions."

**Why it fails:** Each conversation is completely isolated (see §1.9). The model has no access to its other generations.

**Instead:** Provide diversity-forcing mechanisms within the single conversation: randomization instructions, style catalogs, explicit variety in examples.

### 3.8 DON'T: Stuff Edge Cases as Rules

**What fails:** A long list of specific edge-case rules attempting to cover every scenario.

**Why it works against you:** Anthropic's context engineering guide specifically warns against this: "teams will often stuff a laundry list of edge cases into a prompt in an attempt to articulate every possible rule the LLM should follow for a particular task. We do not recommend this." Edge case rules consume context, create conflicting constraints, and are less effective than diverse canonical examples.

**Instead:** Curate 2-3 diverse, canonical examples that effectively portray the expected behavior. Include one or two hard cases among them. Let the model generalize from examples rather than memorizing rules.

### 3.9 DON'T: Use Paragraph-Form Instructions for Complex Behavior

**What fails:** Prose descriptions of multi-step conditional logic.

**Why it fails:** Paragraph form is ambiguous — there are many ways to interpret prose. Numbered steps with explicit conditions produce significantly more reliable execution because there's exactly one interpretation.

**Instead:** Use the imperative, structured, conditional format described in §2.4.

### 3.10 DON'T: Create Overlapping or Ambiguous Tool/Skill Sets

**What fails:** Multiple skills with unclear boundaries or overlapping trigger conditions.

**Why it fails:** Anthropic's tools guide: "If a human engineer can't definitively say which tool should be used in a given situation, an AI agent can't be expected to do better." Ambiguous skill boundaries lead to mis-triggers, non-triggers, or the wrong skill activating.

**Instead:** Curate a minimal viable set of skills. When overlap exists, make the delineation explicit in descriptions. Include explicit exclusions ("Do NOT use this skill for...").

---

## PART 4: SKILL ARCHITECTURE

### 4.1 File Structure

```
skill-name/
├── SKILL.md                    # Core instructions (<500 lines)
│   ├── YAML frontmatter        # name, description (triggering surface)
│   ├── Expert Vocabulary Payload
│   ├── Behavioral Instructions (ordered steps)
│   ├── Anti-Pattern Watchlist
│   ├── Output Format
│   ├── Examples (BAD vs GOOD pairs)
│   └── Questions This Skill Answers
└── references/                 # Extended reference material
    ├── anti-patterns-full.md   # Full writeups
    ├── frameworks.md           # Detailed framework descriptions
    ├── evaluation-criteria.md  # Weighted eval criteria
    └── checklists.md           # Domain-specific checklists
```

### 4.2 SKILL.md Internal Order (Optimized for Attention)

The order of sections within SKILL.md is not arbitrary. It's optimized for how the model processes context:

1. **YAML Frontmatter** — name + description (triggering surface, ~100 words, dual-register)
2. **Expert Vocabulary Payload** — 15-30 precise domain terms. Appears FIRST in the body to prime the routing signal before execution begins.
3. **Anti-Pattern Watchlist** — Detection signals and resolution steps. Appears BEFORE behavioral instructions so the model checks for patterns before proceeding.
4. **Behavioral Instructions** — Ordered steps with imperative verbs and explicit conditions.
5. **Output Format** — Templates, required fields, structure specifications.
6. **Examples** — 2-3 BAD vs GOOD pairs or input→output demonstrations.
7. **Evaluation Criteria** — Weighted, phrased as gradable questions. May also live in references/.
8. **Questions This Skill Answers** — 8-15 natural-language queries. Functions as retrieval anchors AND self-documentation AND test cases.

**Rationale:** The U-shaped attention curve (§1.2) means beginning and end get the most attention. Vocabulary payload at the top primes the expert routing. Retrieval anchors at the bottom benefit from end-of-context attention. Behavioral instructions in the middle are structured enough (numbered steps, conditions) to survive middle-position attention degradation.

### 4.3 Description Authoring (The Triggering Surface)

The ~100 words of the skill description in YAML frontmatter are the most important tokens in the entire skill. They determine whether the skill fires. Rules:

- **Be pushy about triggering** — current models undertrigger
- **Include both the domain AND user scenarios** — not just "architecture decision records" but also "when someone says 'should we use X or Y' or 'I can't decide'"
- **Include explicit exclusions** — "Do NOT use for implementation-level coding decisions"
- **Include synonyms and variations** — cover the vocabulary gap
- **Test trigger reliability** with colloquial queries that don't use formal vocabulary

### 4.4 Context Window Management

- Keep SKILL.md under 500 lines
- Front-load vocabulary payload (appears before instructions)
- Place behavioral instructions in execution order
- Anti-pattern watchlist before behavioral instructions
- Heavy content in `references/` with clear guidance on when to read each file
- For large reference files (>300 lines), include a table of contents
- Cross-reference other skills by name rather than duplicating content

---

## PART 5: EVALUATION AND ITERATION

### 5.1 The Core Testing Loop

1. **Write 3-5 realistic test prompts** — the kind of thing a real user would actually type, in natural language, with typos and vagueness. NOT idealized formal queries.
2. **Run each prompt with and without the skill** — compare output quality. The skill should produce noticeably more expert, structured, and actionable output.
3. **Test trigger reliability** — does the skill fire when it should? Test with colloquial queries that don't use vocabulary from the description.
4. **Test anti-pattern detection** — feed the skill an input containing a known anti-pattern and verify it detects and addresses it.
5. **Iterate** — improve the skill based on failures, retest.

### 5.2 Vocabulary Effectiveness Test

For every key concept in a skill, compare:
1. Output when the prompt uses the expert term (e.g., "assess blast radius")
2. Output when the prompt uses a generic equivalent (e.g., "check the impact")

If output quality is identical, the expert term isn't activating different knowledge — revise the vocabulary selection. If the expert-term version is noticeably more structured and specific, the vocabulary is working.

### 5.3 The Evaluation Criteria Framework

For each skill, define weighted evaluation criteria:

- **HIGH weight** — dimensions where the model typically falls short (originality, domain depth, anti-pattern detection, proactive insight)
- **MEDIUM weight** — dimensions that vary by context (appropriate detail level, pedagogical depth)
- **LOW weight** — dimensions the model handles well by default (technical correctness, formatting, coherence)

Phrase criteria as gradable questions:
- "Does the output use domain-specific terminology appropriate to the task?"
- "Does the output detect and address any anti-patterns present in the input?"
- "Would a domain expert find this output actionable without significant revision?"

### 5.4 Description Optimization

After the skill itself is stable, optimize the triggering description:
1. Build an eval set of trigger queries (both positive and negative)
2. Split 60% train / 40% test
3. Evaluate current description (3 runs per query for reliability)
4. Iterate: propose improvements based on failures, re-evaluate
5. Select by test score (not train score) to avoid overfitting

---

## PART 6: QUICK-REFERENCE CHECKLIST

For every skill created, verify:

### Triggering & Discovery
- [ ] Description is ~100 words, dual-register (formal + colloquial)
- [ ] Description is "pushy" about triggering — includes synonyms, edge cases
- [ ] Description includes explicit exclusions (when NOT to trigger)
- [ ] "Questions This Skill Answers" section: 8-15 natural-language queries

### Vocabulary & Routing
- [ ] Expert Vocabulary Payload: 15-30 precise domain terms
- [ ] Named frameworks with originators cited
- [ ] Vocabulary payload appears BEFORE behavioral instructions
- [ ] No generic consultant-speak ("best practices," "leverage," "synergy")

### Instructions & Structure
- [ ] Behavioral instructions use imperative verbs and ordered steps
- [ ] Explicit conditions (IF/THEN) for branching logic
- [ ] Instructions explain WHY, not just WHAT
- [ ] Right altitude — specific enough to guide, flexible enough to adapt

### Anti-Patterns & Guardrails
- [ ] Anti-pattern watchlist: 5-10 named patterns with detection signals
- [ ] Each anti-pattern has: name, origin, detection signal, resolution, prevention
- [ ] Anti-pattern scan step appears FIRST in behavioral instructions
- [ ] Counter-examples: at least 2 BAD vs GOOD output pairs

### Examples & Evaluation
- [ ] 2-3 diverse canonical examples (input→output or BAD/GOOD pairs)
- [ ] Evaluation criteria defined, weighted, phrased as gradable questions
- [ ] Deterministic verification step where applicable (build, lint, test)
- [ ] Tested against 3-5 realistic colloquial prompts
- [ ] Tested trigger reliability with non-formal vocabulary
- [ ] Compared output WITH skill vs WITHOUT skill

### Architecture
- [ ] SKILL.md under 500 lines
- [ ] Progressive disclosure: heavy content in references/
- [ ] Context window optimized: vocabulary→anti-patterns→instructions→format→examples→questions
- [ ] No cross-conversation assumptions
- [ ] Project context read from filesystem where available

---

## SOURCE INDEX

### Primary Sources — Anthropic Official

| Source | Key Contribution |
|--------|-----------------|
| Anthropic, "Effective Context Engineering for AI Agents" (Sep 2025) | Context as finite resource, attention budget, progressive disclosure, tool design, few-shot guidance |
| Anthropic, "Building Effective Agents" (Dec 2024) | Agent definition, workflow vs agent, tool design principles |
| Anthropic, "Harness Design for Long-Running Apps" (Mar 2026) | Separation of generation/evaluation, weighted eval criteria, initializer pattern |
| Anthropic, "Effective Harnesses for Long-Running Agents" (Dec 2025) | Multi-context-window strategies, compaction, note-taking |
| Anthropic, "Writing Effective Tools for AI Agents" (Sep 2025) | Tool description design, minimal overlap, parameter naming |
| Anthropic, "Prompting Best Practices" (Claude 4.6) | XML tags, long context handling, few-shot, thinking, agentic systems |
| Anthropic, "Improving Frontend Design Through Skills" (Dec 2025) | Vocabulary gap as primary intervention, anti-pattern libraries |
| Anthropic, Skill-Creator Skill Documentation | Canonical skill structure, progressive disclosure, triggering, testing |

### Primary Sources — Research Papers

| Source | Key Contribution |
|--------|-----------------|
| Vaswani et al., "Attention Is All You Need" (2017) | Transformer architecture, self-attention mechanism |
| Liu et al., "Lost in the Middle" (2024, TACL) | 30%+ accuracy drop for middle-positioned information |
| Hsieh et al., "Found in the Middle" (2024, ACL) | U-shaped attention bias, calibration mechanism, up to 15% improvement |
| Wu et al., "On the Emergence of Position Bias" (2025, MIT) | Causal masking and RoPE as architectural causes of position bias |
| Ranjan et al., "One Word Is Not Enough" (2024) | Prompt formatting significantly affects embedding quality |
| Wei et al., "Chain-of-Thought Prompting" (2022) | Step-by-step reasoning improves complex task performance |
| Meincke et al., "Decreasing Value of CoT" (2025, Wharton) | CoT benefits diminish for reasoning models; CoT can increase variability |
| "What Prompts Don't Say" (2025) | Adding more requirements doesn't improve performance; requirements-aware optimization |
| Zamfirescu-Pereira et al., "Why Johnny Can't Prompt" (CHI 2023) | Non-experts prefer "Do not X" over "Do Y" despite the latter being more effective |
| Chroma Research, "Context Rot" (2024) | Degradation of recall as context increases |

### Practitioner Sources

| Source | Key Contribution |
|--------|-----------------|
| Paul Bakaus / Impeccable (2026) | Vocabulary gap as design problem, domain reference files, anti-pattern library |
| Justin Wetch, "Teaching Claude to Design Better" (Jan 2026) | Cross-conversation isolation, blind A/B evaluation, diversity-forcing |
| Angular.love, "Implementing Official Angular Claude Skills" (2026) | Router pattern, 30+ reference files, deterministic verification with `ng build` |
| Hedgineer, "Company-Wide Knowledge Layer with Claude Skills" (2026) | Domain depth beats breadth, focused skills compose better |
| Vaarta Analytics, "Prompt Engineering Is System Design" (2026) | Structured atomic checks reduce false negatives; more instructions ≠ better |
| Elastic Agent Builder, "Prompt Engineering Best Practices" (2025) | Start concise; add detail only where model fails specific cases |
