---
name: skill-creator
description: |
  Creates high-quality Claude Code and Cowork skills using evidence-based principles: expert vocabulary payloads for knowledge routing, dual-register descriptions for reliable triggering, named anti-pattern watchlists for steering past the distribution center, and progressive disclosure architecture for context efficiency. Produces SKILL.md files with structured behavioral instructions, canonical examples, and bundled references.

  Use this skill when the user wants to create a skill, build a custom capability, make a reusable prompt template, or says "I want Claude to always do X." Also triggers when Mission Planner or Agent Creator need to create a domain skill JIT. Works for any domain.

  Do NOT use for creating agent definitions (use Agent Creator) or team composition (use Mission Planner).
---

# Skill Creator

Research-enhanced skill creator that produces higher-quality skills than built-in defaults. Every design decision is grounded in how transformers process context.

---

## Expert Vocabulary Payload

**Prompt Engineering & Routing:**
expert vocabulary payload, dual-register description, vocabulary routing, embedding space routing, attention budget, distribution center, right altitude, retrieval anchor

**Skill Architecture:**
progressive disclosure, context window management, U-shaped attention curve, YAML frontmatter, trigger surface, structural delineation, three-level loading (metadata / SKILL.md / references)

**Behavioral Design:**
anti-pattern watchlist, detection signal, counter-example, imperative instruction, conditional branching, evaluation criteria

**Quality & Testing:**
canonical example, few-shot learning, 15-year practitioner test, consultant-speak (banned), over-prompting, recency bias

---

## Anti-Pattern Watchlist

These are anti-patterns in the **skills this creator generates**. Scan every generated SKILL.md against this list before delivery.

### 1. Generic Consultant-Speak in Vocabulary
**Detection:** Vocabulary payload contains terms like "best practices," "leverage," "synergy," "robust solution," "scalable framework," or "holistic approach." Apply the 15-year practitioner test: would a senior domain expert use this exact term with a peer? If not, it fails.
**Resolution:** Replace every generic term with the precise domain term it vaguely gestures at. "Best practices for error handling" becomes "circuit breaker pattern (Nygard), exponential backoff, dead letter queue."

### 2. Over-Prompting
**Detection:** SKILL.md exceeds 500 lines. The same concept is stated 2-3 times in different words "for emphasis." Instructions contain hedging phrases ("you might want to consider," "it could be helpful to").
**Resolution:** State each instruction once, in imperative form. Remove hedging. Move heavy reference content to `references/`. Test with a minimal version first; add detail only where the model demonstrably fails.

### 3. Positive-Only Instructions
**Detection:** Zero "do NOT" or "avoid" guidance. No anti-pattern watchlist. The skill only describes what to do, never what not to do.
**Resolution:** Add 5-10 domain-specific anti-patterns with named patterns, detection signals, and resolution steps. Without negative constraints, the model gravitates to the distribution center (the most generic, average output).

### 4. Single-Register Description
**Detection:** Description uses only formal terminology OR only casual language. Test: would the skill trigger if a user said "help me with [casual version of task]"? If not, the casual register is missing.
**Resolution:** Rewrite description to include both expert terms (for routing to deep knowledge) and natural-language trigger scenarios (for reliable activation). Add explicit "even if they don't say [formal term]" phrases.

### 5. Edge-Case Stuffing
**Detection:** More than 15 specific edge-case rules. Long lists of "if X then Y" covering every scenario instead of demonstrating the pattern.
**Resolution:** Replace with 2-3 diverse canonical examples that show the pattern. Include one hard case. Let the model generalize from examples rather than memorize rules. Research shows 2-3 examples often match the effectiveness of 9+.

### 6. Paragraph-Form Logic
**Detection:** Complex multi-step behavior described in prose paragraphs. No numbered steps, no IF/THEN conditions, no imperative verbs.
**Resolution:** Refactor to imperative ordered steps with explicit conditions. "First check for anti-patterns, and if you find some you should probably address them" becomes "1. Scan input for anti-patterns. IF detected: apply Detect-Name-Explain-Resolve-Prevent. IF none: proceed to step 2."

### 7. Missing Examples
**Detection:** Zero input-to-output examples or BAD/GOOD pairs in the generated skill. The skill relies entirely on verbal instructions.
**Resolution:** Add 2-3 diverse examples. Use BAD vs GOOD pairs for quality standards, input-to-output pairs for workflows. Place the most representative example last (recency bias gives it the strongest influence).

---

## Behavioral Instructions

### Phase 1: Capture Intent

1. Identify what the skill should do, when it should trigger, and what output format is expected.
2. Identify the target audience: who triggers this skill, and what do they typically say?
3. IF the user's request is vague: ask 1-2 clarifying questions about domain, trigger scenarios, and expected output format. Do NOT proceed with a generic skill.

### Phase 2: Research (Conditional)

4. IF unfamiliar domain: research the domain before drafting.
   - Use web search for domain terminology, frameworks, and named patterns.
   - Use codebase exploration if the skill is project-specific.
   - Identify 15-30 expert terms that pass the 15-year practitioner test.
   - Identify 5-10 domain anti-patterns with established names.

### Phase 3: Draft SKILL.md

5. Write YAML frontmatter with dual-register description (~100 words, pushy).
   - Include both expert terminology AND natural-language trigger scenarios.
   - Include explicit exclusions ("Do NOT use for...").
   - Be aggressive about triggering — current models undertrigger. Include synonyms, edge cases, and adjacent scenarios.

6. Write Expert Vocabulary Payload (15-30 terms in 3-5 clusters).
   - Place BEFORE behavioral instructions. WHY: vocabulary primes the routing signal before execution begins.
   - Organize in sub-domain clusters of 3-8 terms each.
   - Include originator attribution for named frameworks: "circuit breaker (Nygard)" not just "circuit breaker."
   - Apply the 15-year practitioner test to every term. Remove any that fail.

7. Write Anti-Pattern Watchlist (5-10 named patterns).
   - Place BEFORE behavioral instructions. WHY: the model checks for problems before proceeding with normal execution.
   - Each pattern needs: name + origin, detection signal, resolution step.
   - Use established names where they exist: "Bikeshedding (Parkinson)" not "spending too much time on details."

8. Write Behavioral Instructions (imperative, ordered, conditional).
   - Use imperative verbs: "Scan," "Classify," "Output," not "You should try to."
   - Number every step. Use IF/THEN for branching logic.
   - Include WHY for non-obvious steps. WHY: the model can generalize principles to edge cases, but dead rules only cover literal matches.
   - Start with an anti-pattern scan step.

9. Write Output Format specification.
   - Define required fields, structure, and templates.
   - Use structured formats (YAML, tables, numbered lists) over prose.

10. Write 2-3 diverse Examples (BAD vs GOOD or input-to-output).
    - Cover different cases including at least one hard case.
    - Place the most representative example LAST. WHY: recency bias gives the final example the strongest influence on output.

11. Write "Questions This Skill Answers" section (8-15 natural-language queries).
    - Place at the END of the file. WHY: retrieval anchors benefit from end-of-context attention (U-shaped curve).
    - Use the exact phrasing a user would type, including casual and imprecise versions.
    - These function as retrieval anchors, self-documentation, and test cases simultaneously.

### Phase 4: Reference Files (Conditional)

12. IF the skill requires heavy reference content (pattern libraries, extended examples, checklists, evaluation criteria):
    - Create files in `references/` directory.
    - Keep each reference file under 300 lines.
    - Include clear guidance in SKILL.md on WHEN to read each reference file.

13. Keep SKILL.md under 500 lines total. IF over 500: move content to references/.

### Phase 5: Library Metadata

14. Include library metadata in YAML frontmatter: name, version, domain tags, and compatibility notes.

### Phase 6: Validate and Package

15. Scan the generated SKILL.md against the Anti-Pattern Watchlist above. Fix any violations.
16. IF subagents are available: test against 2-3 realistic prompts that a real user would type (casual, imprecise, no formal vocabulary).
17. Package the skill: validate structure, zip, and use present_files for installation.
18. Save to `library/skills/` and update `index.json`.
19. Log creation to `usage-log.jsonl`.

---

## Output Format

The primary output is a complete skill directory:

```
skill-name/
  SKILL.md              # Core instructions (<500 lines)
  references/           # Optional: heavy reference content
    [topic].md          # Each file <300 lines
```

SKILL.md internal structure (in this order):
1. YAML frontmatter (name, description, metadata)
2. Expert Vocabulary Payload
3. Anti-Pattern Watchlist
4. Behavioral Instructions
5. Output Format
6. Examples (BAD vs GOOD)
7. Questions This Skill Answers

---

## Examples

### Example 1: BAD vs GOOD Description

**BAD:**
```
description: "Helps with code review."
```
Single-register. Not pushy. No expert terms. No trigger scenarios. No exclusions. This skill will almost never fire, and when it does, it will produce generic output.

**GOOD:**
```
description: |
  Performs structured code review using cyclomatic complexity analysis,
  connascence taxonomy, and conventional comments (Slaughter). Use when
  the user asks to review code, check a PR, look at their changes, or
  says "is this good?" about code -- even if they don't mention "review."
  Also triggers for diff review, merge request feedback, and pre-commit
  quality checks. Do NOT use for architecture decisions (use Decision
  Advisor) or writing new code (use Code Generator).
```
Dual-register. Pushy. Expert terms route to deep knowledge. Casual triggers ensure activation. Explicit exclusions prevent mis-triggers.

### Example 2: BAD vs GOOD Vocabulary Payload

**BAD:**
```
## Domain Vocabulary
good code, clean code, readable, maintainable, well-tested
```
Generic terms that every blog post uses. Routes to introductory content. Fails the 15-year practitioner test: no senior engineer says "good code" to a peer.

**GOOD:**
```
## Domain Vocabulary
**Structural Analysis:** cyclomatic complexity (McCabe), cognitive
complexity (SonarSource), afferent/efferent coupling (Martin),
connascence (Page-Jones)

**Change Safety:** shotgun surgery (Fowler), feature envy, divergent
change, Liskov substitution violation

**Review Process:** conventional comments (Slaughter), ship/no-ship
framework, diff review vs design review, LGTM criteria
```
Precise terms organized in clusters. Named frameworks with originators. Routes to code review expertise, not generic advice. Every term passes the 15-year practitioner test.

---

## Explicit Note on Packaging

This skill uses Anthropic's native `.skill` packaging mechanism (validate, zip, present_files) for delivery. It does NOT invoke the built-in skill creator. It replaces the built-in approach with research-backed principles from the Forge synthesis:

- Vocabulary routing (embedding space activation via precise terminology)
- U-shaped attention optimization (front-load vocabulary, back-load retrieval anchors)
- Negative constraint steering (anti-patterns push past the distribution center)
- Progressive disclosure (three-level context loading)
- Few-shot superiority (examples beat verbose instructions)

See `./references/skill-principles.md` for the condensed research and `./references/skill-template.md` for an annotated gold-standard example.

---

## Questions This Skill Answers

- "Create a skill for [domain/task]"
- "Build a skill that [does X]"
- "I want Claude to always [behavior] when [trigger]"
- "Make a reusable [domain] helper"
- "How do I create a good skill?"
- "Turn this workflow into a skill"
- "I need a skill for [task]"
- "Build me a custom [domain] assistant"
- "Create a prompt template for [task]"
- "Package this as a skill"
- "What makes a skill effective?"
- "My skill isn't triggering, how do I fix it?"
- "How should I structure a SKILL.md?"
