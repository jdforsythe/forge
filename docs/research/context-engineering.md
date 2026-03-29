# Context Engineering

> Reference for Forge skill design. Covers attention budget management, the U-curve of context, progressive disclosure, and context window optimization.

## Table of Contents
- [Attention Budget](#attention-budget)
- [The U-Curve of Context](#the-u-curve-of-context)
- [Progressive Disclosure](#progressive-disclosure)
- [Context Window Management](#context-window-management)
- [Information Density](#information-density)
- [Attention Position Effects](#attention-position-effects)
- [Structured vs Unstructured Context](#structured-vs-unstructured-context)
- [Context Poisoning](#context-poisoning)
- [Multi-Turn Decay](#multi-turn-decay)
- [Design Rules Summary](#design-rules-summary)

---

## Attention Budget

LLMs have finite context windows. Every token competes for attention weight. The "attention budget" is the practical limit on useful context before quality degrades.

**Core constraint:** Performance is not proportional to context volume. There is an optimal zone, and exceeding it actively harms output quality.

Token budget guidelines for agent prompts:
- **Role identity:** 20-50 tokens (always loaded)
- **Domain vocabulary:** 100-300 tokens (always loaded)
- **Task SOPs:** 500-2000 tokens (loaded per task type)
- **Reference material:** 2000+ tokens (loaded on demand)
- **Total always-loaded core:** 200-500 tokens

---

## The U-Curve of Context

Performance follows an inverted-U relationship with context volume:

| Context Utilization | Effect | Quality |
|---|---|---|
| <10% of window | Information gaps, hallucination, generic output | Low |
| 15-40% of window | Sufficient density without dilution | **Optimal** |
| 40-60% of window | Diminishing returns, some dilution | Acceptable |
| >60% of window | Relevant info buried, "needle in haystack" problem | Degraded |

**Key number:** The optimal zone is **15-40% of the context window**. Design agent prompts to land in this range for the target model.

---

## Progressive Disclosure

Load context in layers based on need, not all at once:

1. **Layer 1 — Always loaded:** Role identity + domain vocabulary. ~200-500 tokens. Establishes the knowledge domain and activates relevant training clusters.
2. **Layer 2 — Task-triggered:** SOPs and checklists specific to the current task type. ~500-2000 tokens. Loaded when the task type is identified.
3. **Layer 3 — On-demand reference:** Full documentation, examples, anti-pattern databases. 2000+ tokens. Loaded only when needed for a specific subtask.
4. **Layer 4 — Compressed summaries:** Large codebases, long conversation histories. Variable size. Summarized to preserve key facts while reducing token count.

This mirrors human expert cognition: core knowledge is always active, specialized knowledge is recalled situationally.

---

## Context Window Management

### Strategy Matrix

| Strategy | When to Use | Token Cost | Load Trigger |
|---|---|---|---|
| Always-loaded core | Role identity, vocabulary | 200-500 | Session start |
| Task-triggered loading | SOPs, checklists | 500-2000 | Task type identified |
| On-demand reference | Documentation, examples | 2000+ | Specific need detected |
| Compressed summaries | Large inputs, histories | Variable | Input exceeds threshold |

### Practical Implementation

- **Skill definitions** should separate always-loaded components (Role Identity, Vocabulary) from task-specific components (SOPs, Anti-Patterns).
- **Team blueprints** should specify which context each agent receives — agents should NOT receive the full context of other agents' work.
- **Artifact handoffs** carry structured data between agents, not raw conversation context.

---

## Information Density

Every token must earn its place. Techniques:

1. **Precise vocabulary over explanation.** "Circuit breaker pattern (Nygard)" replaces a paragraph explaining retry logic with backoff. The term routes to the knowledge cluster directly.
2. **Structured formats over prose.** YAML, tables, and bullet points for configuration-type data. Prose only for reasoning and rationale.
3. **Named patterns over descriptions.** "Rubber-stamp approval (MAST FM-3.1)" activates the failure mode knowledge cluster. No need to explain what it means.
4. **Eliminate filler.** Remove hedging ("it might be helpful to consider"), redundant instructions ("make sure to"), and meta-commentary ("as mentioned above").
5. **Attribution amplifies routing.** "INVEST criteria (Bill Wake)" activates more specific knowledge than "INVEST criteria" alone.

---

## Attention Position Effects

Content at the **beginning** and **end** of prompts receives disproportionate attention (primacy and recency effects).

**Prompt structure rule:**
```
[START] Role identity + critical constraints   ← highest attention
[MIDDLE] Reference material, vocabulary, examples  ← moderate attention
[END] Specific task instruction                ← high attention
```

Implications for agent design:
- Role Identity (Component 1) goes first.
- Domain Vocabulary (Component 2) and reference material in the middle.
- The specific task/instruction should be the last thing the agent reads before generating.

---

## Structured vs Unstructured Context

Structured context is processed more reliably than prose:

| Format | Reliability | Best For |
|---|---|---|
| YAML/JSON | Highest | Configuration, metadata, schemas |
| Markdown tables | High | Comparison data, decision matrices |
| Numbered lists | High | Procedures, ordered steps |
| Bullet points | High | Unordered facts, attributes |
| Headers + bullets | High | Reference documents |
| Prose paragraphs | Moderate | Reasoning, rationale, narrative |
| Unformatted text | Low | Avoid for agent context |

**Rule:** Agents should receive structured input and produce structured output. Free-form prose is reserved for human-facing explanations.

---

## Context Poisoning

Irrelevant or contradictory context **actively degrades** performance. It does not merely waste tokens — it misleads the model.

**Common sources:**
- Stale instructions from previous task iterations
- Resolved issues still present in context
- Contradictory guidance from different sources
- Verbose examples that bury the actual pattern

**Prevention:**
- Curate context aggressively before loading
- Remove resolved issues and superseded instructions
- Version-stamp all reference material
- Prefer few precise examples over many verbose ones

---

## Multi-Turn Decay

In multi-turn conversations, earlier context loses influence. For long-running agent sessions:

1. **Re-state critical constraints** periodically (every 3-5 turns).
2. **Summarize progress** at checkpoints rather than relying on full conversation history.
3. **Use structured artifacts** as the source of truth, not conversation messages.
4. **Reset context** for new phases of work rather than accumulating indefinitely.

---

## Design Rules Summary

1. Target 15-40% context window utilization for optimal performance.
2. Always-loaded core should be 200-500 tokens (role identity + vocabulary).
3. Use progressive disclosure — load context in layers based on need.
4. Place role identity first, task instruction last.
5. Prefer structured formats over prose.
6. Every token must earn its place — maximize information density.
7. Curate aggressively — irrelevant context is actively harmful.
8. In multi-turn sessions, re-state constraints and summarize progress.
9. Use named patterns and attributed vocabulary to activate knowledge clusters efficiently.
10. Design artifacts for structured handoff, not conversation-style communication.

---

*Source: Skill Design Research Synthesis §1.1-1.10*
