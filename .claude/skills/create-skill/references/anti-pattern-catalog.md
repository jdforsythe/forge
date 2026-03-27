# Anti-Pattern Catalog — Templates and Domain Patterns

This reference contains the structural template for anti-pattern entries and the 10
canonical skill-design anti-patterns. Load this at Step 6 (Author Anti-Pattern
Watchlist) of the skill creation workflow.

---

## The DNERP Template (Detect-Name-Explain-Resolve-Prevent)

Every anti-pattern entry in a skill should follow this structure:

```markdown
### [Pattern Name] ([Originator/Source])
- **Detection:** [Observable signal in user input or model output that indicates
  this pattern is active. Must be specific enough to avoid false positives but
  broad enough to catch real instances.]
- **Why it fails:** [One sentence explaining the mechanism — why this pattern
  produces bad outcomes. This enables the model to generalize to related situations.]
- **Resolution:** [Concrete action to take. Not "be careful" or "consider alternatives"
  but a specific step: "Replace X with Y," "Apply framework Z," "Ask question Q."]
- **Prevention:** [Generalizable heuristic that prevents recurrence. Phrased as a
  rule that can be applied to future situations, not just this specific case.]
```

**Why every field matters:**
- **Name + originator:** Acts as an embedding space routing signal, activating the
  same expert knowledge clusters as positive vocabulary
- **Detection:** Makes the skill proactive — it notices patterns rather than waiting
  for the user to ask "am I doing something wrong?"
- **Why it fails:** Enables generalization to novel situations the entry doesn't cover
- **Resolution:** Without a concrete fix, flagging a problem is just noise — users
  learn to ignore warnings that don't come with actions
- **Prevention:** Elevates from tactical fix to strategic principle

---

## Meta vs Domain Anti-Patterns

There are two types of anti-patterns in skill design:

**Meta anti-patterns** — bad patterns in skill design itself. These go in the
meta-skill's watchlist (Vocabulary Vacuum, Trigger Anemia, etc.). They apply to
every skill regardless of domain.

**Domain anti-patterns** — bad patterns within the skill's target domain. These go
in the created skill's watchlist. They are specific to what the skill does (e.g.,
Ice Cream Cone Testing for a testing skill, N+1 Queries for a database skill).

When creating a skill, you need DOMAIN anti-patterns. Use the meta anti-patterns
only when evaluating the skill itself.

---

## The 10 Canonical Skill-Design Anti-Patterns

These are the DON'Ts from the research, reformatted as DNERP entries. Reference
these when evaluating any skill you create.

### 1. Generic Consultant-Speak
- **Detection:** Vocabulary payload contains terms like "best practices," "leverage,"
  "synergy," "alignment," "holistic approach," "scalable solution"
- **Why it fails:** These terms are so overused in training data that they route to
  generic advice — blog posts, consultant slide decks, introductory articles. They are
  the opposite of expert routing signals.
- **Resolution:** Replace every generic term with the precise domain term. "Kanban
  board with WIP limits" instead of "a way to organize work." "STRIDE threat modeling"
  instead of "making sure it's secure."
- **Prevention:** Apply the consultant-speak blacklist from vocabulary-engineering.md.

### 2. Redundant Over-Prompting
- **Detection:** Skill definition exceeds 500 lines; the same instruction is restated
  3-4 different ways "for emphasis"; multiple sections cover the same behavior
- **Why it fails:** Redundancy consumes attention budget. Over-specification can confuse
  high-reasoning models by implying the task is more complex than it is. Research shows
  that at n=19 requirements, average accuracy is LOWER than at n=5.
- **Resolution:** State each instruction once, clearly, in imperative form. Remove
  duplicates. Add detail only for genuinely complex conditional logic.
- **Prevention:** Start with a minimal prompt. Add specificity only where the model fails.

### 3. Flattery-Based Persona
- **Detection:** Persona uses superlatives: "world-renowned expert," "never makes
  mistakes," "always provides the best advice," "unmatched expertise"
- **Why it fails:** Flattery doesn't activate different knowledge — it adds noise.
  Output quality is determined by vocabulary and structure, not compliments.
- **Resolution:** Define persona through domain knowledge, frameworks, experience
  context, and behavioral constraints.
- **Prevention:** Persona is defined by what the agent KNOWS and DOES, not how good it is.

### 4. Positive-Only Instructions
- **Detection:** Skill describes what to do but includes zero anti-patterns, negative
  examples, or "do NOT" constraints
- **Why it fails:** Without negative constraints, the model gravitates to the
  distribution center — the most average output. Without anti-pattern detection, the
  skill has no mechanism to notice when the user is heading toward a known failure mode.
- **Resolution:** Add 5-10 domain anti-patterns with detection-resolution-prevention.
- **Prevention:** Anti-pattern watchlist is a mandatory section in every skill.

### 5. Flagging Without Fixes
- **Detection:** Anti-pattern entries that say "Warning: this may be X" without a
  resolution step; instructions that identify problems but don't provide actions
- **Why it fails:** A flag without a resolution is noise. Users learn to ignore warnings
  that don't come with concrete next steps.
- **Resolution:** Every flag must complete the full DNERP cycle. If you can name the
  problem, you can name the fix.
- **Prevention:** Test each anti-pattern entry: "If I detected this, would I know
  exactly what to do next?"

### 6. Single-Register Descriptions
- **Detection:** Description uses only formal terminology OR only casual language;
  no mixture of registers
- **Why it fails:** Formal-only descriptions don't trigger when users ask in plain
  language. Casual-only descriptions trigger but produce generic output because no
  expert vocabulary routing signal is present.
- **Resolution:** Rewrite with both registers: formal terms that activate deep knowledge
  AND colloquial phrases that match how users actually ask.
- **Prevention:** For every formal trigger phrase, write 2-3 colloquial equivalents.

### 7. Cross-Conversation Memory Assumptions
- **Detection:** Instructions reference "previous sessions," "don't repeat what you've
  done before," "build on past conversations," "remember that..."
- **Why it fails:** Each conversation is isolated. Zero memory. These are dead
  instructions that waste tokens.
- **Resolution:** Remove or replace with in-conversation mechanisms: catalogs,
  randomization, explicit variety in examples.
- **Prevention:** Every instruction must be verifiable within a single conversation.

### 8. Edge-Case Rule Stuffing
- **Detection:** Long list of specific edge-case rules attempting to cover every
  scenario; rule count exceeds 15-20
- **Why it fails:** Edge case rules consume context, create conflicting constraints,
  and are less effective than examples. Anthropic explicitly warns against this.
- **Resolution:** Replace with 2-3 diverse canonical examples that portray expected
  behavior. Include hard cases among the examples.
- **Prevention:** Curate examples, don't accumulate rules.

### 9. Paragraph-Form Instructions
- **Detection:** Behavioral instructions written as flowing prose; no numbered steps;
  no IF/THEN conditions; ambiguous execution order
- **Why it fails:** Prose is ambiguous — there are many interpretations. Numbered
  imperative steps with conditions produce significantly more reliable execution.
- **Resolution:** Refactor into imperative, ordered steps with IF/THEN branching and
  OUTPUT requirements per step.
- **Prevention:** Every step starts with a verb. Every branch has an explicit condition.

### 10. Overlapping Skill Boundaries
- **Detection:** Multiple skills with unclear boundaries; description doesn't specify
  what the skill does NOT handle; two skills could plausibly trigger on the same query
- **Why it fails:** If a human can't definitively say which skill to use, the model
  can't either. Ambiguous boundaries cause mis-triggers, non-triggers, and wrong
  activations.
- **Resolution:** Add explicit exclusions to every description. Make delineation
  explicit when overlap exists.
- **Prevention:** Curate a minimal skill set. Prefer one focused skill over two
  overlapping ones.

---

## How to Discover Domain Anti-Patterns

When creating a new skill, use these methods to find the domain's anti-patterns:

1. **Ask the expert:** "What are the most common mistakes in this domain? What makes
   you cringe when you see it?"
2. **Search for established names:** Most mature domains have named anti-patterns
   (God Object, Cargo Cult Programming, Premature Optimization). Use the established
   names — they are routing signals.
3. **Invert the best practices:** For each expert practice, the anti-pattern is the
   naive alternative. If the practice is "use mutation testing," the anti-pattern is
   "line coverage theater."
4. **Check the model's defaults:** Ask the model to perform the skill's task without
   the skill. Whatever it defaults to that an expert would reject — that's an anti-pattern.

---

## Detection Signal Calibration

**Too broad (false positives):** "The user mentions testing" — this would trigger on
any testing discussion, even expert ones.

**Too narrow (misses patterns):** "The user says 'I only write E2E tests and nothing
else'" — this exact phrasing almost never occurs.

**Calibrated:** "Test suite has more E2E/integration tests than unit tests; test
execution time exceeds 10 minutes for <1000 tests" — specific enough to identify the
pattern, broad enough to catch real instances.

The detection signal should describe observable indicators, not intentions. What you
can SEE in the user's input or described situation, not what you infer about their mindset.
