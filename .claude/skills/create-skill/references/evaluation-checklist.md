# Evaluation Checklist — Quality Gate for Created Skills

This reference is the complete quality gate. Load this at Step 15 (Evaluate) AFTER
the skill has been assembled. Do not load during assembly — generation and evaluation
must be separated to avoid self-evaluation bias.

---

## Binary Pass/Fail Checklist

Mark each item PASS or FAIL with a one-sentence justification. If ANY item fails,
return to the relevant assembly step and fix the deficiency before re-evaluating.

### Triggering & Discovery

- [ ] **T1.** Description is approximately 100 words (80-120 acceptable range)
- [ ] **T2.** Description is dual-register: contains BOTH formal domain terms AND
      colloquial trigger phrases (e.g., "when someone says...", "even if they just...")
- [ ] **T3.** Description is "pushy" about triggering: includes synonyms, user scenarios,
      edge cases, and at least 8 distinct trigger phrases
- [ ] **T4.** Description includes explicit exclusions: what the skill does NOT handle
- [ ] **T5.** "Questions This Skill Answers" section exists with 8-15 natural-language
      queries in mixed registers (formal, casual, imperative, question-form)

### Vocabulary & Routing

- [ ] **V1.** Expert Vocabulary Payload section exists with 15-30 precise domain terms
- [ ] **V2.** Terms are organized into 3-5 sub-domain clusters
- [ ] **V3.** Named frameworks include originators (e.g., "Cynefin (Dave Snowden)")
- [ ] **V4.** Vocabulary payload appears BEFORE behavioral instructions in the file
- [ ] **V5.** Zero consultant-speak in the vocabulary payload: no "best practices,"
      "leverage," "synergy," "holistic," "robust," "scalable," "cutting-edge"

### Instructions & Structure

- [ ] **I1.** Behavioral instructions use imperative verbs and are numbered/ordered
- [ ] **I2.** Branching logic uses explicit IF/THEN conditions (not "you might want to")
- [ ] **I3.** Important rules include a WHY explanation (not just the rule itself)
- [ ] **I4.** Instructions are at the right altitude: specific enough to guide, flexible
      enough to adapt to novel situations
- [ ] **I5.** Every step has an OUTPUT requirement or is part of a phase with one
- [ ] **I6.** No passive voice in instruction steps; every step starts with a verb

### Anti-Patterns & Guardrails

- [ ] **A1.** Anti-Pattern Watchlist section exists with 5-10 named patterns
- [ ] **A2.** Each anti-pattern has ALL five DNERP fields: name (with originator if
      applicable), detection signal, why it fails, resolution, prevention
- [ ] **A3.** Anti-pattern watchlist appears BEFORE behavioral instructions in the file
- [ ] **A4.** Detection signals are calibrated: specific enough to avoid false positives,
      broad enough to catch real instances
- [ ] **A5.** Every resolution is a concrete action, not a vague warning ("Replace X
      with Y" not "Be careful about X")

### Examples & Evaluation

- [ ] **E1.** Examples section exists with 2-3 BAD vs GOOD contrastive pairs
- [ ] **E2.** Examples cover diverse cases: at least one straightforward and one hard case
- [ ] **E3.** The most representative example is placed LAST (recency bias)
- [ ] **E4.** Each example pair includes an annotation explaining WHY the GOOD version works
- [ ] **E5.** BAD examples are realistic (things the model would actually produce without
      guidance), not strawmen

### Architecture

- [ ] **R1.** SKILL.md is under 500 lines total
- [ ] **R2.** Section order follows the attention-optimized sequence: frontmatter →
      vocabulary → anti-patterns → instructions → format → examples → questions
- [ ] **R3.** Progressive disclosure: heavy content (>50 lines of reference material)
      is in references/ files, not inline
- [ ] **R4.** Every references/ file has an explicit load trigger in SKILL.md's
      behavioral instructions
- [ ] **R5.** No instructions require cross-conversation memory or inaccessible information
- [ ] **R6.** Skill reads project context from filesystem where relevant (CLAUDE.md, configs)

---

## Weighted Evaluation Criteria

After the binary checklist passes, evaluate these subjective quality dimensions.
Weight toward dimensions where the model typically falls short.

### HIGH Weight (Model typically struggles here — prioritize these)

**Vocabulary Specificity** — Would a domain expert recognize the vocabulary payload as
terms they actually use daily? Or do the terms feel like a Wikipedia summary?
Rate: 1 (generic) to 5 (indistinguishable from expert conversation)

**Anti-Pattern Depth** — Does the anti-pattern watchlist detect REAL failure modes that
practitioners encounter, or are they strawmen that nobody actually exhibits?
Rate: 1 (obvious/strawman) to 5 (patterns that catch experienced practitioners)

**Description Trigger Breadth** — Would the description trigger on a colloquial query
that uses NONE of the formal vocabulary? Test with 3 casual phrasings.
Rate: 1 (triggers only on exact terms) to 5 (triggers on casual paraphrases)

**Proactive Insight** — Does the skill notice things the user didn't ask about? Does
it detect anti-patterns unprompted? Does it provide context the user didn't know they
needed?
Rate: 1 (only answers what's asked) to 5 (proactively surfaces relevant patterns)

### MEDIUM Weight (Context-dependent)

**Example Diversity** — Do the examples cover meaningfully different cases, or are they
variations of the same scenario?
Rate: 1 (all similar) to 5 (diverse difficulty, domain areas, and edge cases)

**Instruction Clarity** — Could two different people execute these instructions and
arrive at substantially the same output?
Rate: 1 (highly ambiguous) to 5 (unambiguous execution path)

**Reference Organization** — Are reference files named by purpose, loaded at specific
steps, and focused on one coherent concern?
Rate: 1 (disorganized) to 5 (clean separation with clear load triggers)

### LOW Weight (Model typically handles these well by default)

**Formatting Consistency** — Are headers, lists, and code blocks used consistently
throughout the skill?
Rate: 1 (inconsistent) to 5 (perfectly consistent)

**Line Count Compliance** — Is SKILL.md under 500 lines with appropriate content density?
Rate: 1 (bloated or sparse) to 5 (every line earns its place)

---

## Gradable Evaluation Questions

Answer each question after reviewing the complete skill:

1. "Would a domain expert recognize the vocabulary payload as terms they actually use?"
   → If NO: Vocabulary needs revision. Return to Step 5.

2. "Does the anti-pattern watchlist detect real failure modes, not strawmen?"
   → If NO: Anti-patterns need domain research. Return to Step 3 and 6.

3. "Could a new user understand quality expectations from the examples alone,
   without reading the instructions?"
   → If NO: Examples need more contrast or annotation. Return to Step 9.

4. "Would the description trigger on a colloquial query that uses none of its
   formal terms?"
   → If NO: Description needs colloquial trigger phrases. Return to Step 10.

5. "Does the skill pass its own anti-pattern watchlist?"
   → If NO: The skill exhibits patterns it warns against. Fix the contradiction.
