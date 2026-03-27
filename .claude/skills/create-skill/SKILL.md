---
name: create-skill
description: |
  Creates high-quality Claude Code skills using evidence-based prompt engineering
  principles — expert vocabulary payloads, named anti-pattern watchlists, structured
  behavioral instructions, progressive disclosure architecture, and few-shot canonical
  examples. Use this skill whenever someone says "create a skill," "build a skill,"
  "make a new skill," "write a SKILL.md," "design a Claude skill," "I need a skill for,"
  "help me with skill design," "prompt engineering for skills," or wants to create any
  reusable Claude Code capability — even if they just say "I want Claude to be better at X"
  or "can we teach Claude to do Y." Do NOT use for one-off prompt writing, CLAUDE.md
  editing, or agent harness/workflow design.
---

## Expert Vocabulary Payload

The following terms define the precision standard for this skill. Use them in your
reasoning and output. They route to the knowledge clusters that produce expert-quality
skills rather than generic prompt advice.

**Attention & Context Mechanics:**
- attention budget — the finite capacity a transformer distributes across all tokens
- context window saturation — degradation of recall as token count increases
- signal-to-noise ratio — proportion of high-value tokens to filler tokens
- U-shaped attention curve (Liu et al. 2024) — positional bias where beginning and end
  of context receive stronger attention than the middle
- lost-in-the-middle effect — 30%+ accuracy drop for information in middle positions
- token competition — every token in context competes for attention with every other

**Vocabulary & Routing:**
- embedding space routing — vocabulary in prompts activates nearby knowledge clusters
- knowledge cluster activation — expert terms retrieve expert content from training data
- vocabulary gap — mismatch between user query terms and skill description terms
- distribution center — the statistically average output the model defaults to without constraints
- dual-register description — triggering surface that contains both formal and colloquial terms
- retrieval surface / triggering surface — the description text matched against user queries

**Skill Architecture:**
- progressive disclosure / three-level loading — metadata (~100 tokens always in context),
  SKILL.md body (loaded when triggered, <500 lines), references (loaded on demand)
- YAML frontmatter — structured metadata block containing name and description
- canonical examples / few-shot pairs — input-output demonstrations that communicate
  quality expectations more reliably than verbal instructions
- BAD vs GOOD pairs — contrastive examples showing unacceptable vs expert output

**Instruction Design:**
- imperative form — instructions starting with action verbs ("CLASSIFY," "SCAN," "AUTHOR")
- conditional branching (IF/THEN) — explicit logic for handling different situations
- atomic binary checks — instructions decomposed into pass/fail verifiable steps
- right altitude / Goldilocks zone — specific enough to guide, flexible enough to adapt
- over-specification fragility — hardcoded logic that breaks on edge cases

**Anti-Pattern Engineering:**
- negative constraints as steering mechanisms — anti-patterns push output away from
  the distribution center toward more distinctive, expert-level territory
- Detect-Name-Explain-Resolve-Prevent (DNERP) — the full anti-pattern response cycle
- detection signal — observable indicator in user input that a pattern is active
- resolution step — concrete action to address a detected pattern (not just a warning)
- prevention principle — generalizable heuristic that stops recurrence

**Evaluation:**
- generation-evaluation separation (Anthropic harness research, Mar 2026) — using
  distinct evaluation criteria AFTER generation to avoid self-evaluation bias
- deterministic verification gate — build, lint, test as the first quality check
- weighted evaluation criteria — emphasizing dimensions where the model typically falls
  short (originality, domain depth) over dimensions it handles well (formatting, coherence)
- gradable questions — evaluation criteria phrased as concrete, answerable questions

---

## Anti-Pattern Watchlist

Scan the skill being created for these patterns at every step. When detected, apply
the full Detect-Name-Explain-Resolve-Prevent cycle before proceeding.

### 1. Vocabulary Vacuum
- **Detection:** Skill body contains no domain-specific terminology; uses only generic
  terms like "best practices," "good approach," "efficient," "robust," "scalable"
- **Why it fails:** Generic terms route to the distribution center — blog posts and
  introductory tutorials — instead of expert knowledge clusters (embedding space routing)
- **Resolution:** Build a 15-30 term expert vocabulary payload using the "15-year
  practitioner test." Read `references/vocabulary-engineering.md` for the full method.
- **Prevention:** Vocabulary payload is always the first section authored after research.

### 2. Trigger Anemia
- **Detection:** Description is under 50 words, uses only formal terminology, or lacks
  user-scenario phrases like "when someone says..." or "even if they just..."
- **Why it fails:** Models undertrigger — they err on the side of not using skills.
  Single-register descriptions miss when users ask in the other register (vocabulary gap).
- **Resolution:** Expand to ~100 words with dual-register phrasing. Include 10+ trigger
  phrases spanning formal terms, colloquial equivalents, and edge-case scenarios.
  Read `references/description-authoring.md`.
- **Prevention:** Write 5 colloquial test queries before finalizing the description.

### 3. Positive-Only Drift
- **Detection:** Skill contains behavioral instructions but zero anti-patterns, negative
  examples, or "do NOT" constraints
- **Why it fails:** Without negative constraints, the model gravitates to the distribution
  center — the most average, generic version of the output. Anti-patterns are not just
  guardrails; they are steering mechanisms that force distinctive output.
- **Resolution:** Add 5-10 named anti-patterns with detection-resolution-prevention.
  Read `references/anti-pattern-catalog.md` for the structural template.
- **Prevention:** Anti-pattern watchlist is a mandatory section; never skip it.

### 4. Wall-of-Text Instructions
- **Detection:** Behavioral instructions are written as prose paragraphs instead of
  numbered steps; passive voice; no IF/THEN conditions; ambiguous sequencing
- **Why it fails:** Prose is ambiguous — there are many ways to interpret it. Numbered
  imperative steps with explicit conditions produce significantly more reliable execution
  because there is exactly one interpretation.
- **Resolution:** Refactor into ordered steps. Every step starts with an imperative verb.
  Add IF/THEN for every branching decision. Add OUTPUT requirements per step.
- **Prevention:** Test: "Could two different people execute these instructions identically?"

### 5. Context Bloat
- **Detection:** SKILL.md exceeds 500 lines, or contains extended reference material
  inline (pattern libraries, full framework descriptions, lengthy examples)
- **Why it fails:** Every token competes for attention (token competition). Stuffing
  everything into SKILL.md wastes the attention budget on content that may not be
  relevant to the current step. Context rot degrades recall as length increases.
- **Resolution:** Move heavy content to `references/` with clear load triggers.
  Read `references/skill-architecture-spec.md` for line budgets.
- **Prevention:** Apply three-level loading: metadata / SKILL.md body / references.

### 6. Example Starvation
- **Detection:** Skill contains zero examples, or examples show only the happy path,
  or examples lack contrastive pairs (only GOOD, no BAD)
- **Why it fails:** LLMs pattern-match against examples more reliably than they follow
  complex verbal rules. Without examples, the model infers quality expectations from
  the instruction text alone, which is less precise and more ambiguous.
- **Resolution:** Add 2-3 BAD vs GOOD pairs covering diverse cases including at least
  one hard case. Place the most representative example last (recency bias).
- **Prevention:** Examples section is mandatory. Test: "Would a new user understand
  quality expectations from these examples alone, without reading the instructions?"

### 7. Flattery Persona
- **Detection:** Persona definition uses superlatives ("world-class," "best ever,"
  "unmatched expertise," "never makes mistakes") instead of domain knowledge
- **Why it fails:** Flattery doesn't activate different knowledge — it just adds noise.
  Output quality is determined by vocabulary and structure, not compliments.
- **Resolution:** Define the persona through domain knowledge, frameworks used, years
  of experience in specific contexts, and behavioral constraints. "An engineering
  director experienced in B2B SaaS with small teams under deadline pressure" provides
  useful context. "The best engineering director in the world" does not.
- **Prevention:** Persona is defined by what the agent KNOWS, not how good it is.

### 8. Dead Instructions
- **Detection:** Instructions require cross-conversation memory ("never repeat from
  previous sessions"), information the model cannot access, or actions outside the
  model's capabilities
- **Why it fails:** Each conversation is completely isolated — zero memory across
  sessions. Instructions requiring unavailable information are wasted tokens.
- **Resolution:** Remove or replace with in-conversation mechanisms: randomization
  instructions, style catalogs to draw from, explicit variety in examples.
- **Prevention:** Every instruction must be verifiable within a single conversation.

---

## Behavioral Instructions

Follow these steps in order when creating a skill. Each step has a required output.
Do not skip steps. Explain your reasoning at each phase transition.

### Phase 1: Domain Research

These steps are interactive — gather information from the user and the project
environment before authoring anything.

**Step 1. IDENTIFY the skill's domain and scope.**
Ask the user: "What should this skill help Claude do? Describe the task, who benefits,
and one example of when someone would reach for it."
IF the user's answer is vague or overly broad: Ask one follow-up to establish boundaries.
IF the scope overlaps with an existing skill: Flag the overlap and propose clear
boundary delineation with explicit exclusions for each skill.
OUTPUT: One sentence defining the skill's domain and scope.

**Step 2. GATHER domain expertise.**
Read any relevant project files (CLAUDE.md, config files, existing code conventions)
to understand the target environment — because project-specific context eliminates
guessing and pushes output away from the distribution center.
Ask the user: "What does an expert in this domain know that a junior doesn't? Name
5-10 concepts, frameworks, or terms that separate expert work from average work."
IF the user provides fewer than 5 terms: Propose candidate terms from your own domain
knowledge and ask the user to confirm, reject, or expand.
IF the user provides generic terms: Push for specificity — "Can you name the specific
framework, method, or principle? For example, not 'good testing' but 'mutation testing
with PIT' or 'contract testing with Pact.'"
OUTPUT: Raw list of 15-30 candidate expert vocabulary terms.

**Step 3. IDENTIFY the domain's common failure modes.**
Ask the user: "What are the most common mistakes or bad patterns in this domain?
What does bad output look like? What would make an expert cringe?"
IF the user provides fewer than 3 anti-patterns: Propose additional anti-patterns from
your domain knowledge and ask the user to validate.
For each anti-pattern, capture: what it looks like (detection signal), what to do instead
(resolution), and the general principle that prevents it (prevention).
OUTPUT: Raw list of 5-10 anti-patterns with detection signals and resolutions.

**Step 4. COLLECT examples of desired output quality.**
Ask the user: "Show me one example of excellent output in this domain, and one example
of poor output. What specifically makes them different?"
IF the user cannot provide examples: Generate 2-3 candidate BAD vs GOOD pairs from
your domain knowledge and ask the user to validate or adjust.
Ensure diversity: at least one straightforward case and one hard/edge case.
OUTPUT: 2-3 validated BAD vs GOOD example pairs with annotations of what makes each good or bad.

### Phase 2: Skill Assembly

Author each section in order. This order matches the final SKILL.md section order,
so you prime yourself on domain vocabulary before writing instructions.

**Step 5. AUTHOR the expert vocabulary payload.**
Read `references/vocabulary-engineering.md` for the full method.
Organize the 15-30 terms from Step 2 into 3-5 sub-domain clusters.
For each term, apply the "15-year practitioner test": would a senior practitioner
use this exact term when speaking with a peer?
Remove any term that fails the routing test: if replacing the expert term with its
generic equivalent produces identical model output, the term is not specific enough.
Include named frameworks with originators where applicable.
OUTPUT: Formatted Expert Vocabulary Payload section.

**Step 6. AUTHOR the anti-pattern watchlist.**
Read `references/anti-pattern-catalog.md` for the structural template.
For each anti-pattern from Step 3, structure it with the full DNERP pattern:
- **Name** — use the established name with originator if one exists
- **Detection signal** — observable indicator in user input or model output
- **Why it fails** — one sentence explaining the mechanism (aids generalization)
- **Resolution** — concrete action, not just a warning
- **Prevention** — generalizable heuristic for avoiding recurrence
OUTPUT: Formatted Anti-Pattern Watchlist section (5-10 patterns).

**Step 7. AUTHOR the behavioral instructions.**
Write ordered steps using imperative verbs (SCAN, CLASSIFY, GENERATE, VERIFY).
Include IF/THEN conditions for every branching decision.
For every important rule, include one sentence explaining WHY — because principles
with reasoning generalize to novel situations that rules alone don't cover.
Include OUTPUT requirements for each step or phase.
Aim for 8-20 steps depending on domain complexity.
Verify: no prose paragraphs, no passive voice, every step starts with a verb,
no step requires cross-conversation memory.
OUTPUT: Formatted Behavioral Instructions section.

**Step 8. AUTHOR the output format specification.**
Define what the skill's output looks like: structure, required fields, templates.
IF the domain has established output formats: Use them.
IF not: Design a format that is structured enough to guide but flexible enough to adapt.
OUTPUT: Formatted Output Format section.

**Step 9. AUTHOR the examples section.**
Read `references/example-library.md` for structural templates if needed.
Use the BAD vs GOOD pairs from Step 4.
Format each pair with clear BAD and GOOD labels and an annotation explaining WHY
the good version works (which scientific principle is at play).
Ensure diversity: at least one simple case and one hard case.
Place the most representative example LAST — the model weights the last example most
heavily due to recency bias.
OUTPUT: Formatted Examples section with 2-3 BAD vs GOOD pairs.

**Step 10. AUTHOR the YAML frontmatter description.**
Read `references/description-authoring.md` for the full method.
Write ~100 words, dual-register:
- Formal domain terms that activate expert knowledge clusters
- Colloquial trigger phrases ("when someone says...", "even if they just...")
- User scenario descriptions (what the person is trying to accomplish)
- Explicit exclusions ("Do NOT use for...")
- Synonyms and edge-case scenarios to cover the vocabulary gap
This step is done late because it needs the vocabulary, anti-patterns, and instructions
to be fully developed — writing the description first produces vague triggering surfaces.
OUTPUT: Complete YAML frontmatter with name and description.

**Step 11. AUTHOR the "Questions This Skill Answers" section.**
Write 8-15 natural-language queries using exact phrasing a user would type.
Mix registers: some expert-level, some casual, some question-form, some imperative.
These serve three functions: retrieval anchors (benefit from end-of-context attention),
self-documentation, and implicit test cases for trigger reliability.
OUTPUT: Formatted Questions This Skill Answers section.

### Phase 3: Architecture & Assembly

**Step 12. ASSEMBLE the SKILL.md in the correct section order.**
The order is optimized for the U-shaped attention curve — critical content at the
beginning (vocabulary) and end (questions), structured content in the middle:
1. YAML frontmatter (from Step 10)
2. Expert Vocabulary Payload (from Step 5)
3. Anti-Pattern Watchlist (from Step 6)
4. Behavioral Instructions (from Step 7)
5. Output Format (from Step 8)
6. Examples (from Step 9)
7. Questions This Skill Answers (from Step 11)

COUNT the total lines.
IF over 500 lines: Identify sections with extended content and extract to `references/`.
OUTPUT: Complete SKILL.md draft.

**Step 13. IDENTIFY and CREATE reference files.**
IF any section contains extended material (>50 lines of examples, full pattern libraries,
detailed framework descriptions, lengthy checklists): Extract to a `references/` file.
For each reference file:
- Name it by content purpose (e.g., `pattern-library.md`, `framework-guide.md`)
- Add a load trigger in SKILL.md: "Read `references/[filename]` for [specific purpose]"
- Include a brief table of contents if the file exceeds 200 lines
IF the skill is simple enough that SKILL.md is under 300 lines: References may not be
needed. Do not create empty reference files.
OUTPUT: List of reference files created (or "none needed"), each with a one-line summary.

**Step 14. CREATE the skill directory and write all files.**
Write SKILL.md and all reference files to the skill directory.
OUTPUT: Complete skill directory with all files.

### Phase 4: Evaluation

This phase is deliberately separate from generation to avoid self-evaluation bias.
Load the evaluation criteria fresh — do not rely on memory from the assembly phase.

**Step 15. EVALUATE against the quality checklist.**
Read `references/evaluation-checklist.md`.
Run through every checklist item. For each item, mark PASS or FAIL with a one-sentence
justification.
IF any item fails: Return to the relevant Phase 2 step and fix the deficiency.
Re-evaluate after fixes until all items pass.
OUTPUT: Completed checklist with all items passing.

**Step 16. GENERATE 3-5 realistic test prompts.**
Write the kind of query a real user would type — colloquial, possibly vague, maybe
with typos. NOT idealized formal queries.
For each prompt, verify the skill's description would plausibly trigger.
IF any prompt would NOT trigger: Flag the vocabulary gap and revise the description.
OUTPUT: List of test prompts with trigger-likelihood assessment.

**Step 17. PRESENT the completed skill to the user.**
Show: skill directory tree, full SKILL.md content, reference file summaries.
Ask: "Would you like me to adjust any section, add more examples, refine the
description, or modify the anti-patterns?"
OUTPUT: Final skill, ready for use.

---

## Output Format

### During Creation (shown to user at each phase transition):
- **After Phase 1:** Domain scope, vocabulary candidates, anti-pattern candidates, example pairs
- **After Phase 2:** Each authored section as completed
- **After Phase 3:** Assembled SKILL.md with file tree

### Final Deliverable:
```
skill-name/
├── SKILL.md          # Complete, <500 lines, attention-optimized section order
└── references/       # Only if needed
    └── [files].md    # Named by content purpose, each with load trigger in SKILL.md
```

### SKILL.md Internal Structure (always this order):
1. YAML frontmatter — name, description (~100 words, dual-register)
2. Expert Vocabulary Payload — 15-30 terms in sub-domain clusters
3. Anti-Pattern Watchlist — 5-10 named patterns with DNERP structure
4. Behavioral Instructions — ordered imperative steps with IF/THEN
5. Output Format — structure, templates, required fields
6. Examples — 2-3 BAD vs GOOD pairs, most representative last
7. Questions This Skill Answers — 8-15 natural-language queries, mixed register

---

## Examples

### Example 1: Expert Vocabulary Payload — BAD vs GOOD

<example type="BAD" dimension="vocabulary">
## Key Terms
- Good code quality
- Clean architecture
- Best practices for testing
- Efficient API design
- Proper error handling
</example>

<example type="GOOD" dimension="vocabulary">
## Expert Vocabulary Payload

**Architecture & Design:**
- hexagonal architecture (Alistair Cockburn), ports-and-adapters boundary
- aggregate root (Evans, Domain-Driven Design), bounded context mapping
- command-query responsibility segregation (CQRS), event sourcing with snapshots

**Testing & Verification:**
- mutation testing (PIT/Stryker), mutation score vs line coverage
- contract testing (Pact), consumer-driven contract verification
- test pyramid (Mike Cohn) vs testing trophy (Kent C. Dodds)

**Code Health Metrics:**
- cyclomatic complexity (McCabe), cognitive complexity (SonarSource)
- afferent/efferent coupling, instability index (Robert C. Martin, Clean Architecture)
- change failure rate, deployment frequency (DORA metrics, Forsgren et al.)
</example>

**WHY THE GOOD VERSION WORKS:** Every term is precise enough to route to expert
knowledge clusters in embedding space. Named frameworks include their originators,
which activates the specific theoretical context. Terms are organized into sub-domain
clusters. Zero generic consultant-speak — no "best practices," "efficient," or "proper."
The model reading these terms will draw from academic papers, practitioner guides, and
expert discussions rather than introductory blog posts.

### Example 2: Anti-Pattern Entry — BAD vs GOOD

<example type="BAD" dimension="anti-pattern">
### Don't write bad tests
- Tests should be good and cover edge cases
- Make sure tests aren't flaky
- Tests should be fast
</example>

<example type="GOOD" dimension="anti-pattern">
### Ice Cream Cone Testing (Alister Scott)
- **Detection:** Test suite has more E2E/integration tests than unit tests;
  test execution time exceeds 10 minutes for <1000 tests; developers skip
  running tests locally because they're too slow; CI pipeline is the
  bottleneck for merge velocity
- **Why it fails:** Inverted test pyramid creates slow feedback loops, flaky
  results from environment dependencies, and high maintenance cost per test
- **Resolution:** Invert the pyramid. For every E2E test, identify the core
  behavior being verified and write a focused unit test covering it in <100ms.
  Reserve E2E tests for critical user journeys only (login, checkout, data export).
- **Prevention:** Apply the "unit test first" rule — no E2E test is written
  until unit-level coverage for that behavior exists.
</example>

**WHY THE GOOD VERSION WORKS:** Named pattern with originator (embedding space routing
to Alister Scott's testing pyramid work). Detection signal is specific and observable —
not "tests are bad" but measurable indicators. Resolution is a concrete action with a
heuristic (unit test in <100ms). Prevention principle generalizes beyond the specific
case. The "Why it fails" sentence enables the model to apply the principle to novel
testing situations the entry doesn't explicitly cover.

---

## Questions This Skill Answers

- "Create a skill for [domain]"
- "I want to build a Claude skill"
- "Help me write a SKILL.md"
- "How do I make a good Claude Code skill?"
- "I need Claude to be better at [task], can we make a skill?"
- "Can we teach Claude to do [thing] consistently?"
- "Design a reusable skill for my team"
- "What should go in a skill's description?"
- "How do I structure a skill file?"
- "My skill isn't triggering when it should — how do I fix it?"
- "How do I write anti-patterns for a skill?"
- "What makes a skill high quality vs average?"
- "Build me a skill that follows research-backed skill design"
- "I have domain expertise in X, help me turn it into a Claude skill"
- "Create a prompt engineering skill for [use case]"
