# Skill Architecture Specification

This reference defines the structural rules for skill files. Load this at Phase 3
(Assembly) or whenever you need to verify structural compliance.

---

## Canonical File Tree

```
skill-name/
├── SKILL.md                    # Core instructions (<500 lines)
│   ├── YAML frontmatter        # name, description (triggering surface)
│   ├── Expert Vocabulary Payload
│   ├── Anti-Pattern Watchlist
│   ├── Behavioral Instructions (ordered steps)
│   ├── Output Format
│   ├── Examples (BAD vs GOOD pairs)
│   └── Questions This Skill Answers
└── references/                 # Extended reference material (optional)
    └── [purpose-named].md      # Each file covers one coherent concern
```

---

## SKILL.md Section Order (Attention-Optimized)

The order is not arbitrary. It is optimized for the U-shaped attention curve:
beginning and end receive strongest attention; structured content survives the middle.

| Position | Section | Rationale |
|----------|---------|-----------|
| 1 (top) | YAML frontmatter | Always processed first; defines triggering surface |
| 2 | Expert Vocabulary Payload | Primes embedding space routing BEFORE execution begins |
| 3 | Anti-Pattern Watchlist | Model checks for patterns BEFORE proceeding with instructions |
| 4 (middle) | Behavioral Instructions | Structured (numbered, conditional) — survives middle-position degradation |
| 5 (middle) | Output Format | Structured template — survives middle position |
| 6 | Examples | BAD vs GOOD pairs near the end — benefits from rising attention |
| 7 (bottom) | Questions This Skill Answers | Retrieval anchors at the end benefit from end-of-context attention |

---

## Line Budgets

Total SKILL.md: **500 lines maximum.** Practical target: 350-460 lines.

| Section | Budget | Notes |
|---------|--------|-------|
| YAML frontmatter | 10-20 lines | Description is ~100 words |
| Expert Vocabulary Payload | 30-60 lines | 15-30 terms in 3-5 clusters |
| Anti-Pattern Watchlist | 50-100 lines | 5-10 patterns, ~8-12 lines each |
| Behavioral Instructions | 80-200 lines | 8-20 steps depending on complexity |
| Output Format | 15-40 lines | Templates, required fields |
| Examples | 50-100 lines | 2-3 BAD vs GOOD pairs with annotations |
| Questions This Skill Answers | 15-35 lines | 8-15 natural-language queries |

IF over budget: The first candidate for extraction is extended examples, followed by
detailed anti-pattern writeups, followed by framework descriptions. Behavioral
instructions should stay in SKILL.md — they are the execution core.

---

## Three-Level Loading (Progressive Disclosure)

**Level 1 — Metadata (~100 tokens):** Always in context via the `available_skills` list.
This is just the YAML frontmatter name + description. It determines whether the skill
triggers. Must be dual-register, pushy, and under ~100 words.

**Level 2 — SKILL.md Body (<500 lines):** Loaded when the skill is triggered. Contains
the complete execution instructions: vocabulary, anti-patterns, workflow, format,
examples, questions. This is everything the model needs to execute the skill.

**Level 3 — References (unlimited depth):** Loaded on demand during execution when the
model reaches a step that requires deeper reference. Each reference file is loaded by
an explicit trigger in the behavioral instructions ("Read references/X for Y").

**Why this matters:** Context is finite. Loading everything at once wastes the attention
budget on information that may not be relevant. Progressive disclosure keeps the active
context focused on what matters right now.

---

## Reference File Rules

**When to create reference files:**
- A section exceeds 50 lines of extended material (pattern libraries, frameworks)
- The skill covers a complex domain requiring deep reference material
- Evaluation criteria are detailed enough to warrant a separate file

**When NOT to create reference files:**
- The skill is simple enough that SKILL.md is under 300 lines
- The reference would contain fewer than 20 lines (just put it inline)
- The content is only used once and doesn't benefit from separation

**Naming convention:** Name by content purpose, not by section name.
- GOOD: `testing-patterns.md`, `api-design-frameworks.md`, `evaluation-criteria.md`
- BAD: `section-3-extended.md`, `extra-content.md`, `appendix.md`

**Load triggers:** Every reference file must be explicitly referenced in SKILL.md's
behavioral instructions with when and why to load it:
```
Read `references/testing-patterns.md` for the full pattern catalog when the
user's domain involves test strategy.
```

**Table of contents:** Required for reference files exceeding 200 lines.

---

## YAML Frontmatter Specification

```yaml
---
name: skill-name          # lowercase, hyphenated, 2-4 words
description: |
  [~100 words, dual-register description]
  [Formal domain terms + colloquial trigger phrases]
  [User scenarios: "when someone says...", "even if they just..."]
  [Explicit exclusions: "Do NOT use for..."]
---
```

**Name:** Lowercase, hyphenated. Should be recognizable and descriptive.
2-4 words. Matches the directory name.

**Description:** The most important ~100 words in the entire skill. Rules:
- Dual-register: formal terms AND colloquial equivalents
- Pushy: include 8+ trigger phrases covering synonyms and edge cases
- Scenario-based: describe what the user is trying to do, not just what the skill does
- Exclusions: explicitly state what the skill does NOT handle
- See `references/description-authoring.md` for the full method

---

## XML Tags vs Markdown

For Claude specifically, XML tags receive special attention (Claude is tuned for them).
Use XML tags for:
- Examples: `<example type="BAD">` / `<example type="GOOD">`
- Complex structured blocks that need clear boundaries
- Nested content where Markdown headers would create ambiguity

Use Markdown for:
- Section headers (## and ###)
- Lists and tables
- Inline formatting

The exact format matters less than having clear delineation. Consistency within a
single skill is more important than matching other skills.
