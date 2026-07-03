# Failure Taxonomy (MAST + Forge Watchlist)

> Reference for Forge skill design. Part 1 is the real MAST taxonomy — **Multi-Agent System Failure Taxonomy** (Cemri et al., "Why Do Multi-Agent LLM Systems Fail?", arXiv:2503.13657, NeurIPS 2025) — reported faithfully. Part 2 is Forge's own engineering watchlist: named failure heuristics we design against, mapped to their nearest real MAST modes but **not** part of MAST itself.

## Table of Contents
- [Part 1: The MAST Taxonomy (Cemri et al., 2025)](#part-1-the-mast-taxonomy-cemri-et-al-2025)
- [Part 2: The Forge Watchlist (design heuristics)](#part-2-the-forge-watchlist-design-heuristics)
- [Prevention Strategy Matrix](#prevention-strategy-matrix)

---

## Part 1: The MAST Taxonomy (Cemri et al., 2025)

**What it is:** the first empirically grounded failure taxonomy for multi-agent LLM systems: 14 failure modes in 3 categories, derived from 1,600+ annotated execution traces across 7 frameworks (MetaGPT, ChatDev, HyperAgent, AppWorld, AG2, Magentic-One, OpenManus), with inter-annotator agreement κ = 0.88 and an LLM-as-judge pipeline at κ = 0.77.

**Why it matters for Forge:** taxonomy-guided interventions produced measurable gains — +15.6 points on ChatDev correctness and +9.4 points on AG2 from role-specification improvements. And the category split is a direct argument for Forge's core design choices.

### The Three Categories

| Category | Share of failures | What it covers |
|---|---|---|
| **FC1: Specification issues** | **41.77%** | Poor task/role specification and system design |
| **FC2: Inter-agent misalignment** | **36.94%** | Agents failing to coordinate, communicate, or stay on task |
| **FC3: Task verification** | **21.30%** | Missing, incomplete, or incorrect verification of outputs |

The largest category is *specification* — failures baked in before any agent speaks. This is the empirical case for Forge's structured agent definitions, explicit deliverables, and typed artifact handoffs.

### The 14 Failure Modes

Frequencies are from the paper's updated (NeurIPS 2025) trace corpus; earlier arXiv versions report slightly different figures.

| ID | Failure mode | Frequency |
|---|---|---|
| FM-1.1 | Disobey task specification | 10.98% |
| FM-1.2 | Disobey role specification | 0.50% |
| FM-1.3 | **Step repetition** (most common single mode) | 17.14% |
| FM-1.4 | Loss of conversation history | 3.33% |
| FM-1.5 | Unaware of termination conditions | 9.82% |
| FM-2.1 | Conversation reset | 2.33% |
| FM-2.2 | Fail to ask for clarification | 11.65% |
| FM-2.3 | Task derailment | 7.15% |
| FM-2.4 | Information withholding | 1.66% |
| FM-2.5 | Ignored other agent's input | 0.17% |
| FM-2.6 | Reasoning-action mismatch | 13.98% |
| FM-3.1 | Premature termination | 7.82% |
| FM-3.2 | No or incomplete verification | 6.82% |
| FM-3.3 | Incorrect verification | 6.66% |

### What MAST does and does not say

- It **does** show that verification failures (FM-3.2 + FM-3.3, ~13.5% combined) are a persistent failure surface — the motivation for independent, substantive verification steps in Forge pipelines.
- It **does** show role disobedience (FM-1.2) is rare in practice (0.5%) — role drift is less of a live threat than folklore suggests, while *step repetition* and *reasoning-action mismatch* are the dominant modes.
- It does **not** recommend any maximum number of review iterations. (Its ChatDev case study mentions only "a maximum iteration cutoff to prevent infinite loops," with no number.)
- It does **not** contain modes named "Rubber-Stamp Approval," "Role Confusion," "Confident Ignorance," "Message Loss," "Deadlock," or "Groupthink." Those belong to Part 2 below and must never be cited as MAST.

---

## Part 2: The Forge Watchlist (design heuristics)

> **Forge design standard.** These are engineering heuristics Forge agents are built to detect and prevent. They are informed by MAST, by Anthropic's harness-design guidance, and by practitioner experience — but the names, definitions, and frequency intuitions are Forge's own, not published findings. Where a real MAST analog exists, it is noted.

### W-1 Rubber-Stamp Review
- **What:** A review agent approves work without meaningful critique — only praise, no findings, near-instant approval. Driven by LLM sycophancy and weak review prompting.
- **Nearest MAST analogs:** FM-3.2 No or incomplete verification, FM-3.3 Incorrect verification.
- **Supporting evidence:** Anthropic found generator models "confidently praising the work — even when... quality is obviously mediocre," and that separating the generating agent from a standalone skeptical evaluator "proves to be a strong lever" (Harness design for long-running application development, Mar 2026). Fresh-context verifier subagents outperform self-critique (Prompting Claude Fable 5, 2026).
- **Prevention:** Independent verifier with fresh context. Require at least one finding OR an explicitly justified "no issues" with evidence. Findings template the reviewer must fill in.

### W-2 Role Overlap
- **What:** Two agents both own a decision, producing duplicated or contradictory outputs.
- **Nearest MAST analog:** FM-1.2 Disobey role specification (note: rare in MAST data at 0.5% — design for it cheaply, don't over-engineer).
- **Prevention:** Explicit decision-authority boundaries (autonomous / escalate / out-of-scope) per agent; non-overlapping deliverables.

### W-3 Unbounded Review Loops
- **What:** Review cycles oscillate without converging; same issues re-raised, no net progress.
- **Nearest MAST analogs:** FM-1.3 Step repetition, FM-1.5 Unaware of termination conditions.
- **Supporting evidence:** self-refinement gains plateau by roughly the third iteration (Madaan et al., Self-Refine, arXiv:2303.17651 — single-agent setting).
- **Prevention:** **Forge convention: cap review loops at 3 rounds**, then escalate to a human with the open findings. Define convergence criteria upfront.

### W-4 Confident Overreach
- **What:** An agent asked to work beyond its defined scope produces fluent, confident, wrong output instead of flagging the limit.
- **Nearest MAST analogs:** FM-2.2 Fail to ask for clarification, FM-2.6 Reasoning-action mismatch.
- **Current-model note:** frontier models increasingly express honesty via abstention; harnesses must treat "I can't verify this" as a valid output, and progress claims should be audited against tool results — Anthropic reports this "nearly eliminated fabricated status reports" (Prompting Claude Fable 5, 2026).
- **Prevention:** Explicit capability boundaries and escalation triggers in agent definitions; ground all status claims in artifacts or tool output.

### W-5 Error Cascade
- **What:** An early-stage error propagates downstream; each agent builds confidently on the flawed foundation.
- **Nearest MAST analogs:** FM-2.6 Reasoning-action mismatch compounding across handoffs; topology-dependent error amplification (Kim et al., arXiv:2512.08296: 17.2x in independent teams vs 4.4x with a centralized integrator).
- **Prevention:** Quality gates between stages with rejection authority; independent verification at critical handoffs; centralized integration for parallel work.

### W-6 Handoff Loss
- **What:** Artifacts truncated, misread, or silently dropped between agents; downstream output ignores upstream decisions.
- **Nearest MAST analogs:** FM-1.4 Loss of conversation history, FM-2.4 Information withholding, FM-2.5 Ignored other agent's input.
- **Prevention:** Typed artifacts with explicit schemas; completeness checks on receipt; share full traces rather than lossy summaries where feasible (Cognition, 2025).

### W-7 Premature Done
- **What:** The team declares success before acceptance criteria are met — features missing, tests not run.
- **Nearest MAST analog:** FM-3.1 Premature termination (7.82%).
- **Prevention:** Explicit acceptance criteria in every blueprint; a comprehensive feature/criteria list created at kickoff (Anthropic's initializer-agent pattern, Effective harnesses for long-running agents, Nov 2025); verifier checks against the list, not the narrative.

---

## Prevention Strategy Matrix

| Strategy | Prevents | Implementation |
|---|---|---|
| Structured artifact handoffs (typed schemas) | W-6, FC1 spec failures | Typed artifacts, size limits, completeness checks |
| Explicit decision-authority boundaries | W-2 | Autonomous / escalate / out-of-scope per agent |
| Independent fresh-context verification | W-1, W-5, FM-3.x | Standalone verifier agent; deterministic checks first |
| Iteration cap (Forge convention: 3 rounds) | W-3 | Hard cap with human escalation; convergence criteria upfront |
| Grounded status reporting | W-4, W-7 | Audit progress claims against tool results/artifacts |
| Acceptance-criteria list at kickoff | W-7 | Initializer produces criteria list; verifier owns it |
| Centralized integration for parallel work | W-5 | One agent owns merge/synthesis (4.4x vs 17.2x amplification) |
| Capability boundaries + escalation triggers | W-4 | Explicit limits in agent definitions |

---

*Sources: Cemri et al. (2025), "Why Do Multi-Agent LLM Systems Fail?", arXiv:2503.13657 (MAST); Kim et al. (2025), arXiv:2512.08296; Madaan et al. (2023), arXiv:2303.17651; Anthropic engineering blog (2025-2026); Cognition (2025). Forge Watchlist items W-1 through W-7 are Forge design standards. See docs/research/source-index.md.*
