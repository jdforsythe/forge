# Scaling Laws Reference

> Decision criteria for the Mission Planner: when to use single agent vs. team, the 45% threshold, cost multipliers, and the cascade pattern.

---

## The Single-Agent-First Principle

Always try a single well-prompted agent before considering a team. This is not a suggestion — it is the default. Multi-agent teams are justified only when a single agent demonstrably cannot handle the task.

**Why:** A single agent avoids all coordination overhead. No handoff errors, no communication tax, no artifact format negotiation. The 45% threshold (below) quantifies why this matters.

---

## The 45% Threshold

**Critical finding from DeepMind (2025):** If a single well-prompted agent achieves more than 45% of the optimal performance on a task, adding more agents has diminishing returns.

The coordination tax on additional agents often prevents them from contributing their proportional share. The single agent already covers the "easy half" of the problem. The remaining difficulty is in coordination-heavy integration work where more agents add more overhead.

**How to apply this:**
1. Ask: "Could one agent with a good prompt and the right tools do a reasonable job here?"
2. If yes — that is Level 0. Use it.
3. If the answer is "yes, but the quality would suffer in specific areas" — consider Level 2 (worker + reviewer) before jumping to a full team.
4. Only if the answer is "no, this requires genuinely different expertise across subtasks" — proceed to team design.

---

## Cost Multipliers

| Team Size | Token Cost Multiplier | Effective Output Multiplier | Efficiency Ratio |
|---|---|---|---|
| 1 (single) | 1.0x | 1.0x | 1.00 |
| 2 | 2.2x | 1.6x | 0.73 |
| 3 | 3.5x | 2.3x | 0.66 |
| 4 | 5.0x | 2.8x | 0.56 |
| 5 | 7.0x | 3.1x | 0.44 |
| 7+ | 12.0x+ | 3.0x or less | <0.25 |

**How to read this table:**
- A 3-agent team costs 3.5x the tokens of a single agent but produces only 2.3x the effective output.
- Efficiency ratio = Output Multiplier / Cost Multiplier. It drops consistently.
- At 5 agents, you pay 7x for 3.1x output — efficiency is 0.44.
- At 7+ agents, you may get LESS output than a 4-agent team at much higher cost.

**Decision rule:** If the user's goal does not justify a 3.5-7x token cost increase, use a single agent.

---

## Optimal Team Size

- **3 agents:** Best efficiency-to-capability ratio for most tasks.
- **4 agents:** Peak capability for complex decomposable tasks. Saturation point.
- **5 agents:** Marginal gains, significant coordination cost. Justify explicitly.
- **6+ agents:** Diminishing returns. Often net-negative.
- **7+ agents:** Adding agents typically degrades overall performance.

**Default target: 3-4 agents for teams.** Only go to 5 with explicit justification. Never exceed 5 without extraordinary circumstances.

---

## Four Conditions for Multi-Agent

All four must be true to justify a team:

1. **Task is decomposable** — Subtasks can be defined with clean typed artifact interfaces.
2. **Subtasks require genuinely different expertise** — Not just different steps by the same role.
3. **Single-agent trial showed clear capability gaps** — Below 45% threshold or qualitative failure.
4. **Project scope justifies the cost multiplier** — The 3.5-7x token increase is warranted by the goal.

If any condition is false, use a single agent with tool augmentation.

---

## The Cascade Pattern

| Level | Configuration | Token Cost | When to Use |
|---|---|---|---|
| 0 | Single well-prompted agent | 1.0x | Always try first |
| 1 | Single agent + tools | 1.2-1.5x | Agent needs external data or actions |
| 2 | Two agents (worker + reviewer) | 2.2x | Quality validation needed |
| 3 | Small team (3-5 agents) | 3.5-7.0x | Task exceeds single-agent capability |
| 4 | Multi-team with coordinator | 10x+ | Large scope, distinct workstreams |

**Rules:**
- Never skip levels. Always start at Level 0.
- Escalate only on demonstrated failure at the current level.
- Level 2 (worker + reviewer) is often sufficient when the issue is quality, not capability.
- Level 3 requires all four multi-agent conditions to be met.

---

## Complexity Assessment Criteria

Use these three tests in order:

### Test 1: Sequential Dependency
Does each step depend on the previous step's output?
- **High** → Sequential pipeline or single agent. Parallelism will not help.
- **Low** → Parallel topology viable if other conditions are met.

### Test 2: Tool Density
Does the task require heavy tool use (file I/O, code execution, web search)?
- **High** → Single agent strongly preferred. Coordination tax exceeds benefit.
- **Low** → Multi-agent viable if expertise diversity exists.

### Test 3: Single-Agent Sufficiency
Can a single well-prompted agent with appropriate tools handle this?
- **Yes** → Level 0. Done.
- **Partially** (quality concerns) → Level 2 (add reviewer).
- **No** (genuinely different expertise needed) → Level 3 (team).

---

## Key Numbers for Quick Reference

| Metric | Value | Source |
|---|---|---|
| Single-agent sufficiency threshold | 45% of optimal | DeepMind 2025 |
| Optimal team size | 3-5 agents | DeepMind 2025 |
| Saturation point | 4 agents | DeepMind 2025 |
| Cost multiplier at 3 agents | 3.5x | DeepMind 2025 |
| Cost multiplier at 5 agents | 7.0x | DeepMind 2025 |
| Performance degradation | 7+ agents | DeepMind 2025 |
| Error reduction from structured handoffs | ~40% | MetaGPT 2023 |
| Adaptive vs static team improvement | 15-25% | Captain Agent 2024 |

---

*Adapted from docs/research/scaling-laws.md for Mission Planner operational use.*
