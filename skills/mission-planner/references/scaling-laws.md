# Scaling Laws Reference

> Decision criteria for the Mission Planner: when to use single agent vs. team, the 45% baseline paradox, real cost economics, team-size guidance, and the cascade pattern. Findings cite Kim et al. (arXiv:2512.08296) and related work; Forge conventions are labeled.

---

## The Single-Agent-First Principle

Always try a single well-prompted agent before considering a team. This is not a suggestion — it is the default. Multi-agent teams are justified only when a single agent demonstrably cannot handle the task *at comparable effort*.

**Why:** A single agent avoids all coordination overhead. No handoff errors, no communication tax, no artifact format negotiation. Three independent lines of evidence back this:
- The 45% baseline paradox (below).
- Sequential tasks degraded **39-70% under every multi-agent variant** tested (Kim et al. 2025).
- With equal thinking-token budgets, single agents matched or beat five multi-agent architectures on multi-hop reasoning — many published team gains are compute confounds (Tran & Kiela 2026, arXiv:2604.02460).

---

## The 45% Baseline Paradox

**Finding (Kim et al., "Towards a Science of Scaling Agent Systems," arXiv:2512.08296):** tasks where single-agent performance already exceeds **~45% accuracy** experience negative returns from additional agents (β = −0.408, p < 0.001).

The single agent already covers the accessible portion of the problem. The remaining difficulty is coordination-heavy integration work where more agents add more overhead.

**How to apply this:**
1. Ask: "Could one agent with a good prompt and the right tools do a reasonable job here?"
2. If yes — that is Level 0. Use it.
3. If "yes, but quality would suffer in specific areas" — consider Level 2 (worker + independent verifier) before jumping to a full team.
4. Only if "no, this requires genuinely different expertise across decomposable subtasks" — proceed to team design.

---

## Real Cost Economics

There is no verified per-team-size cost table. The sourced numbers:

| Fact | Value | Source |
|---|---|---|
| Coordination overhead by topology | independent +58%, decentralized +263%, centralized +285%, hybrid +515% | Kim et al. 2025 |
| Turn count vs team size | T = 2.72·(n+0.5)^1.724 — a 4-agent hybrid took ~44 turns vs ~7 single-agent | Kim et al. 2025 |
| Team efficiency penalty | 2-6x worse tokens-per-success than single agent | Kim et al. 2025 |
| Research-style orchestrator + workers | ~15x the tokens of a chat interaction | Anthropic, 2025 |

**Decision rule:** If the goal does not justify a several-fold token cost increase (and up to ~15x for broad parallel research), use a single agent.

---

## Team Size

**What the evidence shows:** optima are model- and task-dependent (no universal saturation point exists), but fixed-budget turn growth makes per-agent reasoning "prohibitively thin beyond 3-4 agents" (Kim et al.), collaborative gains saturate early on logistic curves (MacNet, ICLR 2025), and homogeneous teams hit hard diversity ceilings (Ringelmann scaling law, 2026).

> **Forge design standard:** **Recommend 3-4 agents. Hard cap at 5** — going to 5 requires explicit justification in the blueprint. Every agent must bring genuinely different expertise; homogeneous teams saturate fastest.

- **2 agents (worker + verifier):** often all that quality problems need.
- **3 agents:** the default team shape.
- **4 agents:** for complex decomposable tasks with four genuinely distinct expertise domains.
- **5 agents:** cap; justify explicitly.
- **6+:** not permitted by Forge blueprints — coordination channels scale as N·(N−1)/2 and overhead compounds.

---

## Four Conditions for Multi-Agent

All four must be true to justify a team:

1. **Task is decomposable** — subtasks defined with clean typed artifact interfaces. (Decomposable tasks gained up to +80.9% with centralized coordination; sequential tasks lost 39-70%.)
2. **Subtasks require genuinely different expertise** — not just different steps by the same role.
3. **Single-agent trial showed clear capability gaps at comparable effort** — demonstrated failure, not assumed failure. Beware the compute confound: give the single agent an honest chance (equal effort/thinking budget) before concluding it can't.
4. **Project scope justifies the cost** — a 2-6x efficiency penalty, and up to ~15x tokens for research-style orchestration.

If any condition is false, use a single agent with tool augmentation.

---

## The Cascade Pattern

| Level | Configuration | Cost expectation | When to Use |
|---|---|---|---|
| 0 | Single well-prompted agent | Baseline | Always try first |
| 1 | Single agent + tools | Small overhead | Agent needs external data or actions |
| 2 | Worker + independent fresh-context verifier | ~2x | Quality validation needed — verifier subagents beat self-critique (Anthropic, 2026) |
| 3 | Small team (3-4 agents, 5 max) | 2-6x efficiency penalty | Task exceeds single-agent capability at comparable effort |
| 4 | Multi-team with coordinator | Up to ~15x tokens | Large scope, distinct parallelizable workstreams |

**Rules:**
- Never skip levels. Always start at Level 0.
- Escalate only on demonstrated failure at the current level.
- Level 2 is often sufficient when the issue is quality, not capability.
- Level 3 requires all four multi-agent conditions to be met.
- Parallel agents handle reads/research/review; keep writes single-threaded (Cognition, 2025).

---

## Complexity Assessment Criteria

Use these three tests in order:

### Test 1: Sequential Dependency
Does each step depend on the previous step's output?
- **High** → Single agent (every multi-agent variant degraded sequential tasks by 39-70%). If role separation is still needed, use a short gated pipeline.
- **Low** → Parallel topology viable if other conditions are met.

### Test 2: Tool Density
Does the task require heavy tool use (file I/O, code execution, web search)?
- **High (especially 16+ tools)** → Single agent strongly preferred. The tool-coordination trade-off worsens with tool count (Kim et al.).
- **Low** → Multi-agent viable if expertise diversity exists.

### Test 3: Single-Agent Sufficiency
Can a single well-prompted agent with appropriate tools handle this?
- **Yes** → Level 0. Done.
- **Partially** (quality concerns) → Level 2 (add independent verifier).
- **No** (genuinely different expertise needed) → Level 3 (team).

---

## Key Numbers for Quick Reference

| Metric | Value | Source |
|---|---|---|
| Single-agent baseline threshold (negative returns above) | ~45% accuracy | Kim et al. 2025, arXiv:2512.08296 |
| Sequential-task degradation under multi-agent | −39% to −70% | Kim et al. 2025 |
| Decomposable-task gain (centralized) | up to +80.9% | Kim et al. 2025 |
| Error amplification: independent vs centralized | 17.2x vs 4.4x | Kim et al. 2025 |
| Team efficiency penalty | 2-6x | Kim et al. 2025 |
| Research orchestration token cost | ~15x chat | Anthropic 2025 |
| MetaGPT structured handoffs vs dialogue | executability 3.75 vs 2.25; revisions 0.83 vs 2.5 | Hong et al. 2023 |
| Adaptive vs static-style MAS baselines | +21.94% | CaptainAgent 2024 |
| Recommended team size / hard cap | 3-4 / 5 | **Forge design standard** |

---

*Adapted from docs/research/scaling-laws.md for Mission Planner operational use. See docs/research/source-index.md for full citations.*
