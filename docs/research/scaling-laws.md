# Multi-Agent Scaling Laws

> Reference for Forge skill design. What the 2025-2026 evidence actually says about when agent teams help, when they hurt, team size, and topology. Findings are cited to their sources; Forge's own conventions are explicitly labeled as design standards, not research results.

---

## Primary Source

**Kim et al., "Towards a Science of Scaling Agent Systems" (arXiv:2512.08296, Dec 2025).** A Google Research / MIT-led controlled study (with Google DeepMind co-authors) evaluating five architectures (single-agent, independent, centralized, decentralized, hybrid) across three model families (GPT, Gemini, Claude) and four benchmarks (Finance-Agent, BrowseComp-Plus, PlanCraft, Workbench). Companion blog: [research.google/blog/towards-a-science-of-scaling-agent-systems-when-and-why-agent-systems-work](https://research.google/blog/towards-a-science-of-scaling-agent-systems-when-and-why-agent-systems-work/).

Note: this study predates the Claude 4.5/5 generation; its coefficients come from four benchmarks and may not generalize to open-ended software engineering. Treat its thresholds as strong priors, not laws.

---

## The Three Scaling Effects (Kim et al.)

The paper identifies three dominant effects that determine whether adding agents helps:

### 1. Tool-Coordination Trade-off

Agents that must both operate many tools and coordinate with teammates degrade: the coordination burden competes with tool-use capacity, and the trade-off worsens with tool density (16+ tools). High tool density favors a single agent.

### 2. Capability Saturation

Once a single-agent baseline already performs well on a task, additional agents yield diminishing or negative returns (see the 45% baseline paradox below). Coordination cannot add capability the base model already expresses.

### 3. Topology-Dependent Error Amplification

Errors propagate differently by topology. In the study, independent (uncoordinated) multi-agent teams amplified errors **17.2x** relative to a single agent; a centralized orchestrator contained amplification to **4.4x**. If a team is used at all, someone must own integration.

---

## The 45% Baseline Paradox

**Finding (Kim et al.):** Tasks where single-agent performance already exceeds **~45% accuracy** experience negative returns from additional agents (coordination coefficient β = −0.408, p < 0.001).

The single agent already covers the accessible portion of the problem; the remaining difficulty lives in coordination-heavy integration work where more agents add more overhead, not more capability.

**Precision note:** the threshold is *45% accuracy on the benchmark task*, not "45% of optimal performance." It is an empirical regression threshold observed across the study's benchmarks, not a universal constant.

**Implication:** Always try the single-agent approach first. Escalate to multi-agent only when the single agent demonstrably fails — not when you assume it will.

---

## Task-Architecture Alignment

The same study's headline result is that **task structure, not team size, determines whether multi-agent helps**:

| Task type | Multi-agent effect (Kim et al.) |
|---|---|
| Parallelizable, decomposable (Finance-Agent) | **+57% to +81%** (centralized best: +80.9%) |
| Breadth-first search (BrowseComp-Plus) | +0.2% to +9.2% (marginal) |
| Mixed workflow (Workbench) | −11% to +6% |
| Strictly sequential (PlanCraft) | **−39% to −70%** — every multi-agent variant degraded performance |

Mean multi-agent change across all benchmarks: **−3.5%** — with enormous task-dependent variance. A predictive model over task properties (decomposability, tool count, etc.) selected the best architecture for **87%** of held-out configurations.

**Test for decomposability:** Can you define the interfaces between subtasks as typed artifacts with clear schemas? If subtasks require continuous back-and-forth to make progress, the task is sequential — use a single agent.

---

## Coordination Overhead and Token Economics

Real, sourced numbers to replace intuition:

- **Coordination overhead by topology (Kim et al.):** independent +58%, decentralized +263%, centralized +285%, hybrid +515%.
- **Turn count grows superlinearly with team size (Kim et al.):** T = 2.72·(n+0.5)^1.724 (R² = 0.974). A 4-agent hybrid took ~44 turns where a single agent took ~7. Under fixed budgets, per-agent reasoning capacity "becomes prohibitively thin beyond 3-4 agents."
- **Efficiency coefficients (Kim et al.):** single-agent 0.466 vs multi-agent 0.074–0.234 — a **2-6x efficiency penalty** for teams.
- **Anthropic's production data point:** their orchestrator-worker research system beat single-agent Claude Opus 4 by **90.2%** on an internal breadth-first research eval — while consuming **~15x the tokens** of a chat interaction, with token spend alone explaining ~80% of performance variance ([How we built our multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system), Jun 2025).

The lesson from both: multi-agent is a way to *spend more compute productively on parallelizable work*, not a way to get more capability per token.

---

## The Compute Confound

**Tran & Kiela (arXiv:2604.02460, 2026):** with thinking-token budgets held equal, single agents matched or beat five multi-agent architectures (sequential, debate, ensemble, parallel-roles, subtask-parallel) on multi-hop reasoning benchmarks. Many published multi-agent gains are compute and context-window effects, not architectural benefits.

**Implication for Forge:** the burden of proof is on the team. A "team beat one agent" result only counts if the single agent was given comparable effort.

---

## Team Size: What the Evidence Supports

No study supports a universal saturation point. What the literature shows:

- **Kim et al.:** agent-count optima are model- and architecture-dependent (one model peaked at 7 agents); but fixed-budget turn growth constrains effective teams to **3-4 agents in practice**.
- **Ringelmann-effect scaling law (Bertalanič & Fortuna, arXiv:2606.02646, 2026):** effectiveness follows R(N) = 1/(1 + c(N−1)N^−β) with three regimes (hard-ceiling, sublinear, linear). Homogeneous debating teams hit hard ceilings — 30 agents added no answer diversity over 1. Only heterogeneous teams escape ceilings. A pilot run with N ≤ 5 predicts the ceiling.
- **MacNet (Qian et al., ICLR 2025, arXiv:2406.07155):** collaborative scaling follows logistic growth — gains saturate early.

> **Forge design standard — team size cap.** Forge recommends **3-4 agents** and enforces a **hard cap of 5**. This is an engineering convention informed by the fixed-budget analysis above (Kim et al.'s "prohibitively thin beyond 3-4 agents") and the early-saturation results — not a measured universal optimum. Heterogeneous roles (different expertise, different artifacts) are required; homogeneous teams saturate fastest.

---

## Topology Selection Matrix

> Forge design guidance, informed by the error-amplification and overhead findings above. The "max effective agents" column reflects the 3-4 recommendation / 5 cap, not a per-topology measurement.

| Topology | Best For | Coordination Cost | Max Effective Agents | Example |
|---|---|---|---|---|
| Sequential pipeline | Dependent stages | Low | 4-5 | Requirements → Design → Build → Test |
| Parallel-independent | Independent subtasks with synthesis | Medium | 4-5 | Multiple analyses merged by coordinator |
| Centralized coordinator | Complex interdependencies | High | 4-5 | Coordinator dispatches and integrates |
| Hierarchical | Large scope, delegation layers | Very high | 5 | Lead delegates to sub-teams |
| Debate/adversarial | Verification, red-teaming | Medium | 2-3 | Proposer + Critic |

### Topology Decision Rules

1. **Strong sequential dependencies** → single agent first (sequential tasks degraded 39-70% under *every* multi-agent variant in Kim et al.). If a pipeline is still warranted for role separation, keep it short and gate each handoff.
2. **Independent subtasks, shared goal** → parallel-independent with a synthesis agent. Never leave parallel agents unintegrated: independent teams showed 17.2x error amplification vs 4.4x with a centralized integrator.
3. **Complex coordination, many dependencies** → centralized coordinator manages flow.
4. **Clear hierarchy, delegation pattern** → hierarchical; budget for the highest overhead (+515% for hybrid structures).
5. **Uncertain or exploratory tasks** → start with a single agent. Add structure only on demonstrated failure.
6. **High tool density (16+ tools, heavy file I/O)** → single agent preferred; the tool-coordination trade-off worsens with tool count.
7. **Writes stay single-threaded.** Parallel agents are for reads, research, and review; concurrent writers create conflicting implicit decisions (Cognition, "Don't Build Multi-Agents," 2025).

---

## When to Use Multi-Agent

All four conditions must be true:

1. **Task is decomposable** into subtasks with clean typed interfaces (the +81% regime, not the −70% regime).
2. **Subtasks require genuinely different expertise** — heterogeneous roles; homogeneous teams hit diversity ceilings.
3. **Single-agent trial showed clear capability gaps** — demonstrated failure at comparable effort (the compute confound), or a baseline below the ~45% accuracy regime.
4. **Project scope justifies the cost** — expect a 2-6x efficiency penalty and, for research-style orchestration, up to ~15x token spend.

If any condition is false, use a single agent with tool augmentation.

---

## The Cascade Pattern (Operationalized)

The best-supported idea in Forge: start minimal, escalate only on demonstrated failure. Consistent with Kim et al. (baseline paradox), Tran & Kiela (compute confound), and Anthropic's "Building Effective Agents" (find the simplest solution possible).

| Level | Configuration | When to Use |
|---|---|---|
| 0 | Single well-prompted agent | Always try first |
| 1 | Single agent + tools (search, code exec) | Agent needs external data |
| 2 | Two agents (worker + independent verifier) | Quality validation needed — fresh-context verifiers outperform self-critique (Anthropic, Prompting Claude Fable 5, 2026) |
| 3 | Small team (3-4 agents, 5 max) | Task exceeds single-agent capability at comparable effort |
| 4 | Multi-team with coordinator | Large scope with distinct workstreams |

**Rule:** Never escalate to the next level until the current level demonstrably fails.

---

## Key Numbers

| Metric | Value | Source |
|---|---|---|
| Single-agent baseline threshold (negative returns above) | ~45% accuracy (β = −0.408, p < 0.001) | Kim et al. 2025, arXiv:2512.08296 |
| Sequential-task degradation under multi-agent | −39% to −70% | Kim et al. 2025 |
| Decomposable-task gains (centralized) | up to +80.9% | Kim et al. 2025 |
| Mean multi-agent change across benchmarks | −3.5% | Kim et al. 2025 |
| Error amplification: independent vs centralized | 17.2x vs 4.4x | Kim et al. 2025 |
| Coordination overhead by topology | +58% to +515% | Kim et al. 2025 |
| Turn growth with team size | T ∝ (n+0.5)^1.724 | Kim et al. 2025 |
| Multi-agent efficiency penalty | 2-6x (0.466 vs 0.074-0.234) | Kim et al. 2025 |
| Orchestrator-worker research gain / token cost | +90.2% / ~15x tokens | Anthropic multi-agent research system, 2025 |
| Architecture predictable from task properties | 87% of held-out configs | Kim et al. 2025 |
| Recommended team size / hard cap | 3-4 / 5 | **Forge design standard** (informed by the above) |

---

*Sources: Kim et al. (2025), arXiv:2512.08296; Bertalanič & Fortuna (2026), arXiv:2606.02646; Tran & Kiela (2026), arXiv:2604.02460; Qian et al. (ICLR 2025), arXiv:2406.07155; Anthropic engineering blog (2025-2026); Cognition (2025). See docs/research/source-index.md for the full bibliography.*
