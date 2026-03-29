# Team Design Patterns

> Reference for Forge skill design. MetaGPT structured handoffs, Captain Agent adaptive composition, the cascade pattern, and communication topology comparison.

---

## MetaGPT: Structured Artifacts Over Free Dialogue

**Source:** Hong et al., 2023

MetaGPT demonstrated that multi-agent systems perform significantly better when agents communicate through structured artifact handoffs rather than free-form dialogue.

### Key Findings

- **Structured handoffs reduced error propagation by ~40%** compared to free dialogue.
- Artifact-based communication is more token-efficient — less redundant back-and-forth.
- Each agent produces a typed, verifiable deliverable rather than a conversation message.
- The artifact chain creates an auditable trail of decisions.

### Why Artifacts Beat Dialogue

| Dimension | Free Dialogue | Structured Artifacts |
|---|---|---|
| Error propagation | High — ambiguity compounds | Low — schemas enforce clarity |
| Token efficiency | Low — redundant exchanges | High — one transmission per handoff |
| Verifiability | Difficult — buried in conversation | Easy — discrete deliverable |
| Auditability | Poor — must read full history | Good — artifact chain is the record |
| Scalability | Degrades with team size | Stable — interfaces are typed |

### Implications for Forge

Every agent definition includes a Deliverables component (Component 3) specifying exact artifact formats. The team blueprint Artifact Chain section defines the flow between agents. No free-form dialogue between agents — only typed deliverables.

---

## Captain Agent: Adaptive Team Composition

**Source:** Captain Agent research, 2024

Captain Agent introduced dynamic team composition, where a coordinator selects team members per-task from a library of available agents.

### Key Findings

- **Adaptive composition outperformed static teams by 15-25%** on diverse task sets.
- A "captain" agent analyzes the task and selects team members from an agent library.
- Teams are composed per-task rather than per-project.
- Task decomposition drives capability matching — the captain identifies required capabilities, then matches them to available agents.

### Static vs Adaptive Composition

| Aspect | Static Team | Adaptive (Captain Agent) |
|---|---|---|
| Team definition | Fixed at project start | Selected per task |
| Unused agents | Waste context/tokens | Not loaded |
| Missing capabilities | Gap remains | Captain selects best available |
| Task diversity | One team fits all | Specialized per task |
| Overhead | Low (predefined) | Moderate (selection step) |
| Performance on diverse tasks | 15-25% lower | Baseline (higher) |

### Implications for Forge

The Forge library + Mission Planner architecture follows the Captain Agent pattern:
- Agent definitions stored in the library (the pool of available agents).
- Mission Planner analyzes the user's goal and selects/composes the team (the captain role).
- JIT agent generation fills capability gaps when no library agent matches.

---

## The Cascade Pattern

The cascade applies the principle of minimal complexity — start with the simplest solution, escalate only on demonstrated failure.

### Levels

| Level | Configuration | Token Cost | When to Use |
|---|---|---|---|
| 0 | Single well-prompted agent | 1.0x | **Always try first** |
| 1 | Single agent + tools (search, code exec, file I/O) | 1.2-1.5x | Agent needs external data or actions |
| 2 | Two agents (worker + reviewer) | 2.2x | Quality validation needed |
| 3 | Small team (3-5 agents, structured topology) | 3.5-7.0x | Task exceeds single-agent capability |
| 4 | Multi-team with coordinator | 10x+ | Large scope, distinct workstreams |

### Cascade Decision Rules

1. **Start at Level 0.** Single agent with role identity, vocabulary, and task-specific SOP.
2. **Escalate to Level 1** if the agent needs to search, execute code, or access files.
3. **Escalate to Level 2** if output quality is insufficient and would benefit from independent review.
4. **Escalate to Level 3** only if the task requires genuinely different expertise across subtasks AND a single agent trial showed clear capability gaps.
5. **Escalate to Level 4** only for large-scope projects with multiple distinct workstreams that each require Level 3 teams.

**Never skip levels.** The 45% threshold (DeepMind) means single agents handle more than expected.

---

## Communication Topology Comparison

| Pattern | Communication Style | Strengths | Weaknesses | Best For |
|---|---|---|---|---|
| Free dialogue | Unstructured chat | Flexible, emergent behavior | Error-prone, token-heavy, unauditable | Exploration only (avoid in production) |
| Artifact handoff | Typed deliverables | Verifiable, efficient, auditable | Requires format design upfront | **Default for Forge** |
| Blackboard | Shared state space | Good for iterative refinement | Requires concurrency management | Collaborative editing, research synthesis |
| Publish-subscribe | Event-driven notifications | Decoupled, scalable | Complex routing, message loss risk | Monitoring, reactive systems |
| Pipeline | Sequential stage-to-stage | Simple, clear dependencies | Bottleneck at slowest stage | Linear workflows |

---

## Topology Selection (from DeepMind Research)

| Task Characteristic | Recommended Topology | Rationale |
|---|---|---|
| Strong sequential dependencies | Sequential pipeline | Each step depends on previous output |
| Independent subtasks, shared goal | Parallel-independent + synthesis | Max throughput, coordinator merges |
| Complex coordination, many deps | Centralized coordinator | One agent manages flow, prevents chaos |
| Clear hierarchy, delegation | Hierarchical | Lead delegates, reviews, integrates |
| Uncertain/exploratory | Single agent (Level 0) | Add complexity only when needed |
| High tool density | Single agent (Level 0-1) | Coordination tax exceeds multi-agent benefit |

---

## Team Design Anti-Patterns

### Premature Scaling
Adding agents before proving single-agent insufficiency. Violates the cascade pattern. The 45% threshold means most tasks should start at Level 0.

### Role Proliferation
Creating a separate agent for every conceivable subtask. A "documentation agent," "naming agent," "formatting agent" — these should be responsibilities within a broader role, not separate agents. Each agent adds coordination overhead.

### Communication Explosion
N agents create N*(N-1)/2 potential communication channels. At 5 agents, that's 10 channels. At 7, it's 21. Keep teams at 3-5 agents and use structured topologies to limit actual channels.

### Missing Quality Gates
Allowing artifacts to flow downstream without verification. This enables error cascading (MAST FM-3.2) and lowest-common-denominator output (FM-3.5). Every handoff in the artifact chain should have explicit acceptance criteria.

### Homogeneous Teams
All agents with similar capabilities and no role differentiation. This provides redundancy but not capability diversity. Multi-agent teams justify their cost only when agents bring genuinely different expertise.

---

## Key Numbers

| Metric | Value | Source |
|---|---|---|
| Error reduction from structured handoffs | ~40% | MetaGPT (Hong et al. 2023) |
| Adaptive vs static team improvement | 15-25% | Captain Agent (2024) |
| Optimal team size | 3-5 agents | DeepMind (2025) |
| Saturation point | 4 agents | DeepMind (2025) |
| Max review iterations before escalation | 3 rounds | MAST |
| Communication channels for N agents | N*(N-1)/2 | Graph theory |

---

*Source: Skill Design Research Synthesis §6.1-6.5, MetaGPT, Captain Agent, DeepMind scaling*
