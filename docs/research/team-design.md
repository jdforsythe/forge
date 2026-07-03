# Team Design Patterns

> Reference for Forge skill design. MetaGPT structured handoffs, CaptainAgent adaptive composition, the cascade pattern, and communication topology comparison — with the numbers the sources actually report.

---

## MetaGPT: Structured Artifacts Over Free Dialogue

**Source:** Hong et al., 2023 (arXiv:2308.00352; ICLR 2024 oral)

MetaGPT demonstrated that multi-agent systems perform better when agents communicate through SOP-defined structured artifacts (requirements docs, design diagrams, interface specs) rather than free-form dialogue — explicitly to reduce "hallucinations caused by idle chatter between LLMs."

### Key Findings (the paper's real numbers)

- On the SoftwareDev benchmark, MetaGPT scored **3.75/4 executability vs 2.25 for dialogue-based ChatDev**.
- Human revision cost: **0.83 vs ChatDev's 2.5** — roughly 3x fewer manual corrections.
- Executable-feedback ablation: **+4.2 points Pass@1 (HumanEval) and +5.4 (MBPP)**, reaching 85.9%/87.7%.
- Adding specialized roles cut revision cost from 10 to 2.5 and raised executability from 1.0 to 4.0 in role ablations.

*(A "~40% error reduction" figure previously circulated for MetaGPT appears in no version of the paper — do not cite it.)*

**Caveat:** MAST's trace analysis (Cemri et al. 2025) shows MetaGPT itself still exhibits substantial failure rates — structured handoffs mitigate, not eliminate, multi-agent failures. The 2025-2026 literature treats structured vs natural-language agent communication as task-dependent, not settled universally.

### Why Artifacts Beat Dialogue

| Dimension | Free Dialogue | Structured Artifacts |
|---|---|---|
| Error propagation | High — ambiguity compounds | Lower — schemas enforce clarity |
| Token efficiency | Low — redundant exchanges | High — one transmission per handoff |
| Verifiability | Difficult — buried in conversation | Easy — discrete deliverable |
| Auditability | Poor — must read full history | Good — artifact chain is the record |
| Scalability | Degrades with team size | Stable — interfaces are typed |

**Boundary condition (Cognition, 2025):** artifacts must not become lossy summaries that hide decisions. Where feasible, share full traces for decision-relevant context, and keep *writes* single-threaded — parallel writers make conflicting implicit decisions.

### Implications for Forge

Every agent definition includes a Deliverables component specifying exact artifact formats. The team blueprint Artifact Chain defines flow between agents. No free-form dialogue between agents — only typed deliverables. This also targets MAST's largest failure category: specification issues (41.77% of observed failures).

---

## CaptainAgent: Adaptive Team Composition

**Source:** Song et al., 2024 (arXiv:2405.19425; AutoGen/AG2 ecosystem)

CaptainAgent dynamically forms and manages a fresh sub-team for each step — a captain agent selects members per-subtask from an agent library.

### Key Findings

- **+21.94% average accuracy over existing multi-agent baselines** across six scenarios (math, programming, data analysis, science QA, world-information retrieval) without task-specific prompt engineering.
- Baselines were other multi-agent methods (AutoAgents, AgentVerse, DyLAN, Meta-prompting, two-agent AutoGen) — **not a compute-matched strong single agent**, so this does not settle single-vs-team.
- Adaptive composition also argued to prevent stereotypical/groupthink outputs.

*(A "15-25% vs static teams" range previously cited for this work appears in no version of the paper.)*

**Compute-confound caveat:** Tran & Kiela (2026, arXiv:2604.02460) show that with equal thinking-token budgets, single agents match or beat five MAS architectures on multi-hop reasoning — adaptive-composition gains measured against other MAS baselines should not be generalized into a universal team dividend.

### Static vs Adaptive Composition

| Aspect | Static Team | Adaptive (CaptainAgent) |
|---|---|---|
| Team definition | Fixed at project start | Selected per task |
| Unused agents | Waste context/tokens | Not loaded |
| Missing capabilities | Gap remains | Captain selects best available |
| Task diversity | One team fits all | Specialized per task |
| Overhead | Low (predefined) | Moderate (selection step) |

### Implications for Forge

The Forge library + Mission Planner architecture follows the CaptainAgent pattern:
- Agent definitions stored in the library (the pool of available agents).
- Mission Planner analyzes the goal and composes the team per mission (the captain role).
- JIT agent generation fills capability gaps when no library agent matches.

---

## The Cascade Pattern

Start with the simplest solution, escalate only on demonstrated failure. This is the best-supported pattern in Forge: consistent with the ~45% baseline paradox and sequential-task degradation (Kim et al. 2025, arXiv:2512.08296), the compute confound (Tran & Kiela 2026), and Anthropic's "find the simplest solution possible" doctrine (Building Effective Agents, 2024).

### Levels

| Level | Configuration | Cost expectation | When to Use |
|---|---|---|---|
| 0 | Single well-prompted agent | Baseline | **Always try first** |
| 1 | Single agent + tools (search, code exec, file I/O) | Small overhead | Agent needs external data or actions |
| 2 | Worker + independent fresh-context verifier | ~2x | Quality validation needed — verifier subagents outperform self-critique (Anthropic, 2026) |
| 3 | Small team (3-4 agents, 5 max, structured topology) | 2-6x efficiency penalty vs single agent (Kim et al.) | Task exceeds single-agent capability at comparable effort |
| 4 | Multi-team with coordinator | Up to ~15x tokens for research-style orchestration (Anthropic, 2025) | Large scope, distinct parallelizable workstreams |

### Cascade Decision Rules

1. **Start at Level 0.** Single agent with role identity, vocabulary, and task-specific SOP.
2. **Escalate to Level 1** if the agent needs to search, execute code, or access files.
3. **Escalate to Level 2** if output quality is insufficient and would benefit from independent review.
4. **Escalate to Level 3** only if the task decomposes into subtasks with typed interfaces AND requires genuinely different expertise AND a single-agent trial at comparable effort showed clear capability gaps.
5. **Escalate to Level 4** only for large-scope projects with multiple distinct workstreams that each justify Level 3.

**Never skip levels.** Tasks where a single agent already exceeds ~45% accuracy show *negative* returns from added agents (Kim et al.).

---

## Communication Topology Comparison

| Pattern | Communication Style | Strengths | Weaknesses | Best For |
|---|---|---|---|---|
| Free dialogue | Unstructured chat | Flexible, emergent behavior | Error-prone, token-heavy, unauditable | Exploration only (avoid in production) |
| Artifact handoff | Typed deliverables | Verifiable, efficient, auditable | Requires format design upfront | **Default for Forge** |
| Blackboard | Shared state space | Good for iterative refinement | Requires concurrency management | Collaborative editing, research synthesis |
| Publish-subscribe | Event-driven notifications | Decoupled, scalable | Complex routing, message-loss risk | Monitoring, reactive systems |
| Pipeline | Sequential stage-to-stage | Simple, clear dependencies | Bottleneck at slowest stage | Linear workflows |

---

## Topology Selection (Kim et al., 2025)

| Task Characteristic | Recommended Topology | Rationale |
|---|---|---|
| Strong sequential dependencies | Single agent first | Every MAS variant degraded sequential tasks by 39-70% |
| Independent subtasks, shared goal | Parallel-independent + centralized synthesis | Decomposable tasks gained up to +80.9%; centralized integration contains error amplification (4.4x vs 17.2x) |
| Complex coordination, many deps | Centralized coordinator | One agent manages flow; budget +285% overhead |
| Clear hierarchy, delegation | Hierarchical | Highest overhead (+515% hybrid); reserve for large scope |
| Uncertain/exploratory | Single agent (Level 0) | Add complexity only when needed |
| High tool density (16+ tools) | Single agent (Level 0-1) | Tool-coordination trade-off worsens with tool count |

---

## Team Design Anti-Patterns

### Premature Scaling
Adding agents before proving single-agent insufficiency. Violates the cascade; the baseline paradox means capable single agents are actively hurt by teammates.

### Compute-Confounded Comparison
Judging "the team did better" without giving the single agent comparable effort/thinking budget. Many published MAS gains vanish under equal budgets (Tran & Kiela 2026).

### Role Proliferation
A separate agent for every conceivable subtask ("documentation agent," "naming agent," "formatting agent"). These are responsibilities within a role; each extra agent adds coordination overhead.

### Communication Explosion
N agents create N*(N-1)/2 potential channels: 10 at 5 agents, 21 at 7. Keep teams at 3-4 (5 max) and use structured topologies to limit actual channels.

### Missing Quality Gates
Artifacts flowing downstream without verification enables error cascades — and MAST attributes 21.3% of failures to the verification category. Every handoff gets explicit acceptance criteria.

### Homogeneous Teams
All agents with similar capabilities provide redundancy, not capability diversity — and homogeneous teams hit diversity ceilings fastest (Ringelmann-effect scaling law, 2026). Teams justify their cost only with genuinely different expertise.

### Unintegrated Parallelism
Parallel agents with no owner for synthesis. Independent teams amplified errors 17.2x vs 4.4x under a centralized integrator (Kim et al.).

---

## Key Numbers

| Metric | Value | Source |
|---|---|---|
| MetaGPT vs ChatDev executability | 3.75 vs 2.25 | Hong et al. 2023, arXiv:2308.00352 |
| MetaGPT human revision cost | 0.83 vs 2.5 (~3x fewer) | Hong et al. 2023 |
| Adaptive composition vs MAS baselines | +21.94% | Song et al. 2024, arXiv:2405.19425 |
| Sequential-task degradation under MAS | −39% to −70% | Kim et al. 2025, arXiv:2512.08296 |
| Error amplification, independent vs centralized | 17.2x vs 4.4x | Kim et al. 2025 |
| Research orchestrator-worker gain / cost | +90.2% / ~15x tokens | Anthropic, 2025 |
| Recommended team size / hard cap | 3-4 / 5 | **Forge design standard** (see scaling-laws.md) |
| Max review iterations before escalation | 3 rounds | **Forge design standard** (basis: Self-Refine plateau) |
| Communication channels for N agents | N*(N-1)/2 | Graph theory |

---

*Sources: Hong et al. (2023), arXiv:2308.00352; Song et al. (2024), arXiv:2405.19425; Kim et al. (2025), arXiv:2512.08296; Tran & Kiela (2026), arXiv:2604.02460; Cemri et al. (2025), arXiv:2503.13657; Anthropic engineering blog (2024-2026); Cognition (2025). See docs/research/source-index.md.*
