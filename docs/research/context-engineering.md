# Context Engineering

> Reference for Forge skill design. Covers the attention budget, how performance actually degrades with context length, progressive disclosure, and context window optimization. Findings are cited; Forge token budgets are labeled as design standards.

## Table of Contents
- [Attention Budget](#attention-budget)
- [How Performance Degrades with Context](#how-performance-degrades-with-context)
- [Progressive Disclosure](#progressive-disclosure)
- [Context Window Management](#context-window-management)
- [Information Density](#information-density)
- [Attention Position Effects](#attention-position-effects)
- [Structured vs Unstructured Context](#structured-vs-unstructured-context)
- [Context Poisoning](#context-poisoning)
- [Long-Horizon Sessions](#long-horizon-sessions)
- [Design Rules Summary](#design-rules-summary)

---

## Attention Budget

LLMs have an "attention budget that they draw on when parsing large volumes of context" — every token depletes it, so the goal is "the smallest possible set of high-signal tokens that maximize the likelihood of some desired outcome" (Anthropic, "Effective Context Engineering for AI Agents," Sep 2025).

> **Forge design standard — token budgets for agent prompts.** These allocations are Forge conventions applying the attention-budget principle, not published thresholds:
> - **Role identity:** 20-50 tokens (always loaded)
> - **Domain vocabulary:** 100-300 tokens (always loaded)
> - **Task SOPs:** 500-2000 tokens (loaded per task type)
> - **Reference material:** 2000+ tokens (loaded on demand)
> - **Total always-loaded core:** ~200-500 tokens

---

## How Performance Degrades with Context

**The evidence shows monotonic degradation, not a U-curve.** Chroma's "Context Rot" study (July 2025; 18 models including Claude 4-class, GPT-4.1, Gemini 2.5) found performance degrades as input length increases, "often in surprising and non-uniform ways," even on deliberately simple tasks. Focused prompts *outperformed* full prompts — there is no documented penalty for small, relevant contexts, and no evidence for an "optimal utilization percentage" of the window.

Key degradation dynamics to design against:

- **Length itself degrades recall** — before any window limit is reached (Chroma 2025; endorsed in Anthropic's context-engineering post as "context rot").
- **Non-literal retrieval degrades fastest.** Without lexical overlap between query and content, 11 of 12 models fell below 50% of their short-context baseline by 32K tokens (NoLiMa, ICML 2025, arXiv:2502.05167). Classic needle-in-a-haystack scores overstate real long-context ability.
- **Distractors hurt disproportionately** — even a single distractor reduces performance; Claude models showed the lowest hallucination rates under distraction in Chroma's tests.
- **More relevant ≠ more is better:** shuffled haystacks outperformed coherent ones across all 18 models in Chroma's study — a reminder that intuitions about "helpful context" are unreliable; measure.

**Design consequence:** minimize context aggressively. Lean is the optimum; there is no "too little" penalty for well-chosen content.

---

## Progressive Disclosure

Load context in layers based on need, not all at once. This is Anthropic's recommended architecture for skills and agents (context-engineering post; Agent Skills docs: metadata always in context, body on trigger, resources on demand).

1. **Layer 1 — Always loaded:** Role identity + domain vocabulary. ~200-500 tokens (Forge standard). Establishes role scope and domain register.
2. **Layer 2 — Task-triggered:** SOPs and checklists specific to the current task type. ~500-2000 tokens.
3. **Layer 3 — On-demand reference:** Full documentation, examples, anti-pattern databases. 2000+ tokens. Loaded only when needed.
4. **Layer 4 — Compressed summaries:** Large codebases, long histories. Summarized to preserve key facts while reducing token count. Sub-agents should return "a condensed, distilled summary" (Anthropic suggests 1,000-2,000 tokens for research sub-agents).

---

## Context Window Management

### Strategy Matrix

| Strategy | When to Use | Token Cost | Load Trigger |
|---|---|---|---|
| Always-loaded core | Role identity, vocabulary | ~200-500 | Session start |
| Task-triggered loading | SOPs, checklists | 500-2000 | Task type identified |
| On-demand reference | Documentation, examples | 2000+ | Specific need detected |
| Compressed summaries | Large inputs, histories | Variable | Input exceeds threshold |

### Practical Implementation

- **Skill definitions** separate always-loaded components (Role Identity, Vocabulary) from task-specific components (SOPs, Anti-Patterns).
- **Team blueprints** specify which context each agent receives — agents should NOT receive the full context of other agents' work; they receive typed artifacts.
- **Platform note (Claude Code, 2026):** skill descriptions live in a listing budgeted at 1% of the context window (description + when_to_use truncated at 1,536 characters), and on auto-compaction each invoked skill retains only its first 5,000 tokens. Front-load what matters.

---

## Information Density

Every token must earn its place. Techniques:

1. **Precise vocabulary over explanation.** "Circuit breaker pattern (Nygard)" replaces a paragraph explaining retry logic with backoff.
2. **Structured formats over prose.** YAML, tables, and bullet points for configuration-type data. Prose only for reasoning and rationale.
3. **Named patterns over descriptions.** "Rubber-stamp review (Forge watchlist W-1)" names the failure compactly; the watchlist entry carries the detail.
4. **Eliminate filler.** Remove hedging ("it might be helpful to consider"), redundant instructions ("make sure to"), and meta-commentary ("as mentioned above").
5. **Attribute named frameworks.** "INVEST criteria (Bill Wake)" disambiguates and anchors intended meaning.

---

## Attention Position Effects

Content at the **beginning** and **end** of prompts receives disproportionate attention. The architecture-level account: causal masking biases attention toward early positions while rotary position embeddings add recency decay; their interplay reproduces the U-shaped "lost in the middle" pattern (Wu et al., ICML 2025, arXiv:2502.01951; Liu et al., TACL 2024 — >20-point mid-context drops on GPT-3.5-era models; Hsieh et al., Findings of ACL 2024).

**Current-model caveat:** frontier models have substantially reduced — not eliminated — mid-context degradation on literal retrieval; primacy bias persists (Chroma 2025). Position effects remain a sound ordering heuristic, not a cliff.

**Prompt structure rule:**
```
[START] Role identity + critical constraints   ← highest attention
[MIDDLE] Reference material, vocabulary, examples  ← moderate attention
[END] Specific task instruction                ← high attention
```

For long multi-document inputs, Anthropic's docs are explicit: put documents at the top and the query at the end — "queries at the end can improve response quality by up to 30% in tests."

---

## Structured vs Unstructured Context

Structural markers (XML tags, headers, tables) give the model explicit boundaries it doesn't have to infer. Anthropic's current docs recommend XML tags to "parse complex prompts unambiguously." Evidence notes: format choice measurably affects output — up to 40% swing for GPT-3.5-turbo on code translation (He et al. 2024, arXiv:2411.10541) — but larger models are far more robust, no single format wins universally, and no rigorous XML-vs-Markdown benchmark exists for current Claude models. Use XML tags for Claude as a disambiguation practice, not a benchmarked win.

| Format | Reliability | Best For |
|---|---|---|
| YAML/JSON | High | Configuration, metadata, schemas |
| XML-tagged sections | High (Claude-recommended) | Mixing instructions, context, examples |
| Markdown tables | High | Comparison data, decision matrices |
| Numbered lists | High | Procedures, ordered steps |
| Prose paragraphs | Moderate | Reasoning, rationale, narrative |
| Unformatted text | Low | Avoid for agent context |

**Rule:** Agents receive structured input and produce structured output. Free-form prose is reserved for human-facing explanations.

---

## Context Poisoning

Irrelevant or contradictory context **actively degrades** performance — it misleads rather than merely wasting tokens (distractor results, Chroma 2025).

**Common sources:**
- Stale instructions from previous task iterations
- Resolved issues still present in context
- Contradictory guidance from different sources
- Verbose examples that bury the actual pattern

**Prevention:**
- Curate context aggressively before loading
- Remove resolved issues and superseded instructions
- Version-stamp all reference material
- Prefer few precise examples over many verbose ones — and never stuff "a laundry list of edge cases" (Anthropic: "We do not recommend this")

---

## Long-Horizon Sessions

For long-running agent work (Anthropic harness guidance, Nov 2025 / Mar 2026):

1. **Structured artifacts are the source of truth,** not conversation messages — progress files, feature lists, git state.
2. **Compaction alone is insufficient**; pair it with note-taking (progress logs) so a fresh context can recover state quickly.
3. **Prefer fresh contexts per phase** over indefinite accumulation. (Model-generation note: "context anxiety" — wrapping up early near perceived limits — largely disappeared with Opus 4.5+; scaffolding needs shrink as models improve.)
4. **Memory systems pay off on current models:** Fable 5 "performs particularly well when recording lessons from previous runs," even as simple Markdown files (Prompting Claude Fable 5, 2026).

---

## Design Rules Summary

1. Minimize context: the smallest set of high-signal tokens. Lean is the optimum — there is no documented "too little" penalty for well-chosen content.
2. Always-loaded core ~200-500 tokens (Forge standard: role identity + vocabulary).
3. Use progressive disclosure — load context in layers based on need.
4. Place role identity first, task instruction last; for long inputs, query at the end.
5. Prefer structured formats; use XML tags for Claude.
6. Every token must earn its place — maximize information density.
7. Curate aggressively — irrelevant context is actively harmful.
8. In long sessions, persist state in artifacts and memory files, not conversation history.
9. Design artifacts for structured handoff, not conversation-style communication.
10. Expect degradation to grow with total input length — especially for non-literal retrieval — and verify rather than assume recall.

---

*Sources: Anthropic, "Effective Context Engineering for AI Agents" (Sep 2025); Chroma Research, "Context Rot" (Jul 2025); NoLiMa (ICML 2025), arXiv:2502.05167; Wu et al. (ICML 2025), arXiv:2502.01951; Liu et al. (TACL 2024); Hsieh et al. (Findings of ACL 2024); He et al. (2024), arXiv:2411.10541; Anthropic Claude prompting docs and harness posts (2025-2026). Token budgets are Forge design standards. See docs/research/source-index.md.*
