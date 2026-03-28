# Multi-Agent Scaling Laws

> Reference for Forge skill design. DeepMind's three principles for multi-agent scaling, the 45% threshold, topology selection, and cost multipliers.

---

## DeepMind Three Principles (2025)

### Principle 1: MAS Effectiveness Depends on Task Decomposability

Tasks that can be cleanly divided into independent subtasks benefit from multiple agents. Tasks with high interdependency between steps do not.

**Test:** Can you define the interfaces between subtasks as typed artifacts with clear schemas? If yes, the task is decomposable. If subtasks require continuous back-and-forth to make progress, the task is not decomposable — use a single agent.

### Principle 2: Coordination Overhead Can Exceed Benefits

Every additional agent adds communication overhead:
- Messages must be composed, transmitted, received, parsed, and acted upon.
- Each handoff risks information loss (MAST FM-1.x failures).
- Agents must maintain shared understanding of progress and state.

For tasks requiring tight coordination, this overhead can consume more tokens than the additional agent contributes in useful work.

### Principle 3: Optimal Team Size is 3-5, Saturation at 4

Performance scales sub-linearly with team size:
- **3 agents:** Best efficiency-to-capability ratio for most tasks.
- **4 agents:** Peak capability for complex decomposable tasks. Saturation point.
- **5 agents:** Marginal gains, significant coordination cost.
- **6+ agents:** Diminishing returns. Often net-negative.
- **7+ agents:** Adding agents typically degrades overall performance.

---

## The 45% Threshold

**Critical finding:** If a single well-prompted agent achieves >45% of the optimal performance on a task, adding more agents has diminishing returns.

The coordination tax on additional agents often prevents them from contributing their proportional share. The single agent already covers the "easy half" of the problem; the remaining difficulty is in coordination-heavy integration work where more agents add more overhead.

**Implication:** Always try the single-agent approach first. Only escalate to multi-agent when the single agent demonstrably cannot handle the task — not when you assume it cannot.

**Application in Forge:** The cascade pattern (Level 0 → Level 3) operationalizes this finding. Start with a single agent, measure results, escalate only on demonstrated failure.

---

## Scaling Cost Multipliers

| Team Size | Token Cost Multiplier | Effective Output Multiplier | Efficiency Ratio |
|---|---|---|---|
| 1 (single) | 1.0x | 1.0x | 1.00 |
| 2 | 2.2x | 1.6x | 0.73 |
| 3 | 3.5x | 2.3x | 0.66 |
| 4 | 5.0x | 2.8x | 0.56 |
| 5 | 7.0x | 3.1x | 0.44 |
| 7+ | 12.0x+ | 3.0x or less | <0.25 |

**Reading the table:**
- A 3-agent team costs 3.5x the tokens of a single agent but produces only 2.3x the effective output.
- Efficiency ratio = Output Multiplier / Cost Multiplier. It drops consistently as team size grows.
- At 5 agents, you pay 7x the cost for 3.1x the output — efficiency is 0.44.
- At 7+ agents, you may get less output than a 4-agent team at much higher cost.

---

## Topology Selection Matrix

| Topology | Best For | Coordination Cost | Max Effective Agents | Example |
|---|---|---|---|---|
| Sequential pipeline | Dependent stages | Low | 5-6 | Requirements → Design → Build → Test |
| Parallel-independent | Independent subtasks with synthesis | Medium | 4-5 | Multiple analyses merged by coordinator |
| Centralized coordinator | Complex interdependencies | High | 4-5 | Coordinator dispatches and integrates |
| Hierarchical | Large scope, delegation layers | Very high | 5-7 | Lead delegates to sub-teams |
| Debate/adversarial | Verification, red-teaming | Medium | 2-3 | Proposer + Critic |

### Topology Decision Rules

1. **Strong sequential dependencies** → Sequential pipeline. Each step depends on previous output.
2. **Independent subtasks, shared goal** → Parallel-independent with synthesis agent.
3. **Complex coordination, many dependencies** → Centralized coordinator manages flow.
4. **Clear hierarchy, delegation pattern** → Hierarchical. Lead delegates, reviews, integrates.
5. **Uncertain or exploratory tasks** → Start with single agent. Add complexity only when needed.
6. **High tool density** (heavy file I/O, web search, code execution) → Single agent preferred. Coordination tax exceeds multi-agent benefit.

---

## When to Use Multi-Agent

All four conditions must be true:

1. **Task is decomposable** into subtasks with clean typed interfaces.
2. **Subtasks require genuinely different expertise** (not just different steps by the same role).
3. **Single-agent trial showed clear capability gaps** (below 45% threshold or qualitative failure).
4. **Project scope justifies 3-5x token cost** multiplier.

If any condition is false, use a single agent with tool augmentation.

---

## The Cascade Pattern (Operationalized)

| Level | Configuration | When to Use |
|---|---|---|
| 0 | Single well-prompted agent | Always try first |
| 1 | Single agent + tools (search, code exec) | Agent needs external data |
| 2 | Two agents (worker + reviewer) | Quality validation needed |
| 3 | Small team (3-5 agents) | Task exceeds single-agent capability |
| 4 | Multi-team with coordinator | Large scope with distinct workstreams |

**Rule:** Never escalate to the next level until the current level demonstrably fails. This prevents unnecessary complexity and token waste.

---

## Key Numbers

| Metric | Value | Source |
|---|---|---|
| Optimal team size | 3-5 agents | DeepMind 2025 |
| Saturation point | 4 agents | DeepMind 2025 |
| Single-agent sufficiency threshold | 45% of optimal | DeepMind 2025 |
| Cost multiplier at 3 agents | 3.5x | DeepMind 2025 |
| Cost multiplier at 5 agents | 7.0x | DeepMind 2025 |
| Performance degradation threshold | 7+ agents | DeepMind 2025 |
| Efficiency ratio at 4 agents | 0.56 | DeepMind 2025 |

---

*Source: Skill Design Research Synthesis §4.1-4.5, DeepMind multi-agent scaling research (2025)*
