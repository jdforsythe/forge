# Annotated Skill Template: Code Review

> Gold-standard example of a well-crafted skill. Every section is annotated with WHY it exists, WHY it's ordered this way, and what makes it effective. Use this as the reference when generating new skills.

---

## The Complete Example

Below is a full "Code Review" skill with inline annotations in `<!-- comments -->`.

---

```markdown
<!-- ============================================================
  YAML FRONTMATTER — THE TRIGGERING SURFACE
  This is the MOST IMPORTANT part of the skill. These ~100 words
  determine whether the skill fires. The description lives in the
  available_skills list and is matched against user queries.
  ============================================================ -->

---
name: code-review
description: |
  Performs structured code review using cyclomatic complexity analysis,
  cognitive complexity (SonarSource), connascence taxonomy (Page-Jones),
  and conventional comments (Slaughter). Evaluates change safety via
  coupling analysis (Martin) and Fowler's refactoring smell catalog.

  Use when the user asks to review code, check a PR, look at changes,
  audit code quality, or says "is this good?" about code — even if
  they don't say "review." Also triggers for diff feedback, merge
  request review, and pre-commit quality checks.

  Do NOT use for architecture decisions (use Decision Advisor) or
  writing new code from scratch (use Code Generator).
---

<!-- WHY this description works:
  1. DUAL REGISTER: Expert terms (cyclomatic complexity, connascence)
     route to deep knowledge. Casual phrases ("is this good?") ensure
     activation on informal queries.
  2. PUSHY: Lists synonyms (diff feedback, merge request review,
     pre-commit checks) to combat undertriggering.
  3. EXPLICIT EXCLUSIONS: Prevents mis-triggers on architecture or
     code generation tasks.
  4. ~100 WORDS: Dense but not diluted.
-->

# Code Review

<!-- One-line summary anchors the skill's purpose. -->
Structured code review that finds real problems, not style nitpicks.

---

## Expert Vocabulary Payload

<!-- WHY THIS IS FIRST: Vocabulary primes the routing signal in
  embedding space BEFORE the model reads instructions. Every term
  here activates a specific knowledge cluster. Without this section,
  the model draws from generic "code review" content (blog posts,
  junior tutorials). With it, the model routes to expert-level
  review knowledge.

  WHY CLUSTERS: Grouping by sub-domain ensures coverage across the
  full scope of code review, not just one narrow aspect. 3-5 clusters
  of 3-8 terms each is the effective range.

  EVERY TERM passes the 15-year practitioner test. -->

**Structural Analysis:**
cyclomatic complexity (McCabe), cognitive complexity (SonarSource),
afferent/efferent coupling (Ca/Ce, Martin), connascence (Page-Jones),
depth of inheritance tree, LCOM4 (lack of cohesion)

**Change Safety & Smell Detection:**
shotgun surgery (Fowler), feature envy, divergent change, inappropriate
intimacy, Liskov substitution violation, primitive obsession, temporal
coupling

**Review Process:**
conventional comments (Slaughter), ship/no-ship framework, diff review
vs design review, LGTM criteria, reviewer cognitive load, change risk
anti-patterns (CRAP index)

**Testing Adequacy:**
branch coverage vs mutation score, test-to-code ratio, property-based
testing (QuickCheck), test pyramid (Cohn), characterization tests
(Feathers)

---

## Anti-Pattern Watchlist

<!-- WHY BEFORE INSTRUCTIONS: The model reads these BEFORE behavioral
  steps, so it checks for problems first. Anti-patterns also function
  as vocabulary routing — "rubber-stamp approval" activates knowledge
  about review failure modes.

  Each pattern has: Name (established term), Detection (observable
  signal), Resolution (concrete action). This is the full
  Detect-Name-Resolve pattern. -->

### 1. Rubber-Stamp Approval
**Detection:** Reviewer approves with "LGTM" or generic praise without specific observations. Review took <2 minutes for 200+ line change.
**Resolution:** Require at least 3 specific observations per 100 lines changed. Flag at least one concern or question per review, even if minor.

### 2. Style-Over-Substance
**Detection:** All comments are about formatting, naming, or whitespace. Zero comments about logic, error handling, or design.
**Resolution:** Apply the 80/20 rule: 80% of review effort on behavior (logic, safety, edge cases), 20% on style. Defer style to linters.

### 3. Nitpick Avalanche
**Detection:** More than 10 minor comments with zero blocking concerns identified. Reviewer flags every imperfection without prioritizing.
**Resolution:** Use conventional comment labels: `nitpick:` for optional, `suggestion:` for recommended, `issue:` for blocking. Limit nitpicks to 3 per review.

### 4. Architecture Review Disguised as Code Review
**Detection:** Comments question fundamental design decisions that should have been resolved before implementation. "Why didn't you use a different pattern entirely?"
**Resolution:** Separate diff review (this PR) from design review (the approach). If architectural concerns arise, flag them for a separate design discussion. Review the code AS SUBMITTED.

### 5. Missing Risk Assessment
**Detection:** Review does not mention blast radius, rollback strategy, or affected consumers for changes to shared code or APIs.
**Resolution:** For any change touching shared code: identify affected consumers, assess rollback difficulty, and note whether the change is backwards-compatible.

---

## Behavioral Instructions

<!-- WHY ORDERED STEPS: Numbered imperative steps produce more reliable
  execution than prose. Each step has exactly one interpretation.
  IF/THEN conditions handle branching explicitly.

  WHY ANTI-PATTERN SCAN IS STEP 1: Check for problems before
  doing the work. This prevents the model from completing a full
  review and then noticing the PR is actually a design question. -->

1. Scan the submitted code and the user's request for anti-patterns from the watchlist above.
   IF an anti-pattern is detected: name it, explain why it matters, and recommend how to proceed before continuing the review.

2. Classify the review scope:
   - **Diff review**: Evaluate the specific changes in this PR/commit.
   - **Design review**: Evaluate the architectural approach.
   - **Full audit**: Comprehensive review of a module or file.
   IF scope is unclear: ask one clarifying question.

3. Read the code changes. For each file, assess:
   a. **Correctness**: Does the logic do what it claims? Edge cases? Off-by-one?
   b. **Change safety**: Blast radius? Coupling changes? Backward compatibility?
   c. **Testability**: Are the changes tested? Is the test coverage meaningful (not just line coverage)?
   d. **Readability**: Would a new team member understand this in 6 months?

4. Write review comments using conventional comment format:
   - `blocking:` — Must fix before merge. Ship/no-ship.
   - `issue:` — Should fix. Not a merge blocker but a real problem.
   - `suggestion:` — Could improve. Take or leave.
   - `nitpick:` — Style preference. Optional.
   - `question:` — Seeking understanding, not requesting change.
   - `praise:` — Explicitly call out good patterns. WHY: positive reinforcement improves future PRs.

5. Summarize with a ship/no-ship recommendation.
   IF ship: state the strongest reason to merge.
   IF no-ship: state the specific blocking issues and what "done" looks like.

---

## Output Format

<!-- WHY STRUCTURED: The reviewer and author both need to scan quickly.
  Structured output with labeled comments is parseable at a glance. -->

```
## Review Summary
**Scope:** [diff review | design review | full audit]
**Verdict:** [ship | ship with suggestions | no-ship]
**Risk Level:** [low | medium | high] — [one-line justification]

## Blocking Issues
- `blocking:` [file:line] [description]

## Issues
- `issue:` [file:line] [description]

## Suggestions
- `suggestion:` [file:line] [description]

## Praise
- `praise:` [file:line] [what's good and why]

## Testing Assessment
[Coverage evaluation, missing test cases, test quality notes]
```

---

## Examples

<!-- WHY 2 EXAMPLES: Enough to show the pattern, not so many that they
  overflow context. The GOOD example is placed LAST because recency
  bias gives it the strongest influence on output quality.

  WHY BAD vs GOOD: Contrastive pairs communicate quality standards
  more effectively than positive examples alone. The model sees both
  what to avoid and what to produce. -->

### BAD Review Output (generic, unhelpful)

```
The code looks mostly fine. A few suggestions:
- Maybe add some more tests
- Consider renaming some variables for clarity
- The error handling could be improved
- Overall looks good, LGTM!
```

Problems: No specific observations. No line references. No severity labels. No risk assessment. Classic rubber-stamp.

### GOOD Review Output (specific, actionable)

```
## Review Summary
**Scope:** diff review
**Verdict:** ship with suggestions
**Risk Level:** medium — changes PaymentService interface consumed by 3 downstream services

## Blocking Issues
None.

## Issues
- `issue:` payment_service.py:47 — The retry logic uses a fixed 3-attempt
  loop with no backoff. Under load, this becomes a thundering herd against
  the payment provider. Use exponential backoff with jitter (Nygard, Release
  It!, ch. 5).

## Suggestions
- `suggestion:` payment_service.py:23 — The `process_payment` method has
  cyclomatic complexity of 14 (McCabe threshold: 10). Extract the
  validation logic into `_validate_payment_request()` to bring both
  methods under 10.
- `suggestion:` payment_service.py:89 — Feature envy: this method
  accesses 5 fields from `CustomerRecord` and none from `PaymentService`.
  Consider moving it to `CustomerRecord` or extracting a value object.

## Praise
- `praise:` payment_service.py:12 — Clean separation of the idempotency
  key generation. This makes replay-safe retries trivial to implement.

## Testing Assessment
Branch coverage is 78%, but mutation score is likely low — the tests
check happy-path returns but don't assert on the retry count or backoff
timing. Add a test that verifies backoff intervals under failure injection.
```

<!-- WHY this example is last: It's the most representative of the
  desired output quality. Recency bias ensures the model weights it
  most heavily when generating. -->

---

## Questions This Skill Answers

<!-- WHY AT THE END: Retrieval anchors benefit from end-of-context
  attention (the right side of the U-shaped curve). These also serve
  as self-documentation and trigger-testing surface.

  WHY NATURAL LANGUAGE: These use the exact phrasing a user would type,
  including casual and imprecise versions. This is the casual register
  complement to the expert vocabulary payload. -->

- "Review this code"
- "Check my PR"
- "Is this code good?"
- "Look at my changes"
- "What's wrong with this code?"
- "Give me feedback on this diff"
- "Should I merge this?"
- "Do a code review"
- "Audit this module"
- "What would you change in this code?"
```

---

## Why This Template Works: Design Decision Summary

| Section | Position | Reason |
|---|---|---|
| YAML description | First | Triggering surface; most important tokens |
| Vocabulary payload | After frontmatter | Primes routing before execution |
| Anti-pattern watchlist | Before instructions | Model checks for problems first |
| Behavioral instructions | Middle | Structured format survives mid-attention dip |
| Output format | After instructions | Defines the shape of success |
| Examples | Near end | Recency bias strengthens the last example |
| Questions section | Last | Retrieval anchors benefit from end-position attention |

**Total length:** ~250 lines. Well under the 500-line threshold. Heavy content (full smell catalog, complete conventional comments spec, extended testing heuristics) would go in `references/` and be loaded on demand.
