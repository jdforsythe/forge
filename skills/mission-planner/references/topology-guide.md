# Topology Selection Guide

> Decision matrix and selection flowchart for choosing the right communication topology for a multi-agent team.

---

## The Four Topologies

### 1. Sequential Pipeline

Agents execute in order. Each agent receives the previous agent's output and produces input for the next.

```
Agent A → Artifact → Agent B → Artifact → Agent C → Artifact → Agent D
```

**Characteristics:**
- Low coordination overhead (each agent talks to at most 2 others)
- Clear dependencies and artifact flow
- Bottleneck at the slowest stage
- Easy to reason about and debug

**When to use:**
- Each step depends on the previous step's output
- The work has a natural phase order (plan → design → build → test)
- Low parallelism — subtasks cannot run simultaneously
- Domain follows a value stream (input transforms through stages)

**When NOT to use:**
- Subtasks are independent and could run in parallel
- Multiple agents need to collaborate on the same artifact simultaneously
- The pipeline has more than 5-6 stages (latency becomes excessive)

**Examples:**
- Software product: Requirements → Architecture → Implementation → Testing
- Content creation: Research → Outline → Draft → Edit
- Data pipeline: Collect → Clean → Analyze → Report

---

### 2. Parallel-Independent

Multiple agents work simultaneously on independent subtasks. A synthesis agent merges results.

```
              ┌→ Agent B → Artifact B ─┐
Agent A → ───┤→ Agent C → Artifact C ──├→ Synthesis Agent → Final Artifact
              └→ Agent D → Artifact D ─┘
```

**Characteristics:**
- Maximum throughput for independent work
- Requires a synthesis/integration step
- Higher coordination cost than sequential (synthesis agent must reconcile)
- Risk of inconsistency between parallel outputs

**When to use:**
- Subtasks are genuinely independent (no data dependencies between them)
- A shared goal can be decomposed into distinct facets
- Speed matters — parallel execution reduces wall-clock time
- Each subtask produces a self-contained artifact

**When NOT to use:**
- Subtasks depend on each other's outputs
- Consistency between outputs is critical and hard to enforce via schemas
- The synthesis step would be more complex than the subtasks themselves

**Examples:**
- Marketing campaign: Email copy + Social media copy + Ad copy (synthesized by strategist)
- Security audit: Network scan + Code review + Policy review (synthesized by lead auditor)
- Research: Multiple literature reviews on different aspects (synthesized by research lead)

---

### 3. Centralized Coordinator

One agent manages all communication and task assignment. Worker agents report to the coordinator.

```
              ┌→ Agent B ─┐
Coordinator ──┤→ Agent C ──├→ Coordinator → Final Artifact
              └→ Agent D ─┘
                (↕ ongoing communication)
```

**Characteristics:**
- Coordinator handles all routing and integration
- Workers communicate only with coordinator, not with each other
- Highest coordination cost (coordinator is a bottleneck)
- Most flexible — can handle complex dependencies

**When to use:**
- Complex dependencies between subtasks that change during execution
- Tasks require frequent re-planning or dynamic work assignment
- Many interactions between agents needed (coordinator reduces N-to-N to N-to-1)
- Adaptive composition — coordinator can bring in agents as needed

**When NOT to use:**
- Dependencies are simple and linear (use sequential pipeline instead)
- Subtasks are truly independent (use parallel-independent instead)
- Coordinator becomes a bottleneck — spending more tokens coordinating than workers spend working

**Examples:**
- Complex software project with shifting requirements and multiple workstreams
- Event planning with many interdependent logistics streams
- Incident response where priorities shift dynamically

---

### 4. Hierarchical

A lead agent delegates to sub-agents, reviews their work, and integrates. May have multiple levels.

```
                Lead Agent
               /     |     \
          Agent B  Agent C  Agent D
                      |
                   Agent E
```

**Characteristics:**
- Natural delegation pattern — lead breaks work into pieces
- Lead reviews and integrates sub-agent outputs
- Can scale to larger teams with sub-leads
- Highest overhead — multiple review and integration layers

**When to use:**
- Clear delegation pattern where a lead can decompose and assign
- Quality control is critical — lead reviews every deliverable
- Scope is large enough to warrant delegation layers
- Team mirrors a real organizational hierarchy (Conway's Law)

**When NOT to use:**
- Team is small (3-4 agents) — hierarchy adds unnecessary overhead
- Work is peer-level with no natural lead role
- The "lead" would spend more time reviewing than doing useful integration

**Examples:**
- Large software project: Tech Lead delegates to frontend, backend, and infrastructure engineers
- Book writing: Editor delegates chapters to writers, reviews and integrates
- Consulting engagement: Partner delegates workstreams to analysts

---

## Decision Flowchart

Follow this flowchart to select the right topology:

```
START: You have determined a team is needed (Level 3)
  │
  ▼
Q1: Do subtasks depend on each other's outputs?
  │
  ├─ YES, strongly (each step needs previous output)
  │   → SEQUENTIAL PIPELINE
  │
  ├─ YES, but dependencies are complex/dynamic
  │   │
  │   ▼
  │   Q2: Do dependencies change during execution?
  │   ├─ YES → CENTRALIZED COORDINATOR
  │   └─ NO  → HIERARCHICAL (if clear lead) or SEQUENTIAL PIPELINE (if linear)
  │
  └─ NO (subtasks are independent)
      │
      ▼
      Q3: Is there a natural lead who should review all outputs?
      ├─ YES → HIERARCHICAL
      └─ NO  → PARALLEL-INDEPENDENT (with synthesis agent)
```

---

## Decision Matrix

| Task Characteristic | Recommended Topology | Coordination Cost | Max Effective Agents |
|---|---|---|---|
| Strong sequential dependencies | Sequential pipeline | Low | 5-6 |
| Independent subtasks, shared goal | Parallel-independent | Medium | 4-5 |
| Complex/dynamic interdependencies | Centralized coordinator | High | 4-5 |
| Clear delegation, lead reviews all | Hierarchical | Very high | 5-7 |
| Verification or adversarial review | Debate (worker + critic) | Medium | 2-3 |
| Uncertain or exploratory | Single agent (Level 0) | None | 1 |

---

## Topology and Communication Channels

The number of potential communication channels affects coordination cost:

| Topology | Channels for N agents | At N=4 |
|---|---|---|
| Sequential pipeline | N-1 | 3 |
| Parallel-independent | N (all to coordinator) | 4 |
| Centralized coordinator | 2*(N-1) (bidirectional to coordinator) | 6 |
| Hierarchical | Varies (tree structure) | 4-6 |
| Fully connected (avoid) | N*(N-1)/2 | 6 |

**Rule:** Never use fully connected topology. Always constrain communication through a structured pattern.

---

## Communication Style: Artifacts, Not Dialogue

Regardless of topology, all agent communication uses structured artifact handoffs, not free-form dialogue.

**Why:**
- Structured handoffs reduce error propagation by ~40% (MetaGPT)
- Artifacts are verifiable — you can check if the deliverable meets its specification
- Artifacts create an auditable trail
- Token-efficient — one transmission per handoff instead of back-and-forth

**Every handoff must specify:**
1. The artifact name and type
2. The format (markdown sections, JSON schema, code structure)
3. Acceptance criteria for the quality gate

---

## Common Mistakes in Topology Selection

### Defaulting to Centralized Coordinator
Coordinator topology has the highest overhead. Use it only when dependencies are truly complex and dynamic. For most projects, sequential pipeline or parallel-independent is sufficient.

### Forcing Parallel When Sequential is Natural
If step B genuinely needs step A's output, making them parallel just creates rework. Accept the sequential dependency.

### Skipping the Synthesis Step in Parallel
Parallel-independent topology requires a synthesis agent to merge results. Without it, you get disconnected artifacts that nobody integrates.

### Hierarchy for Small Teams
Hierarchical topology adds review overhead. For 3-4 agent teams, the lead spends more time coordinating than the team gains from delegation. Use sequential or parallel instead.

---

*Reference for Mission Planner topology selection. See also: scaling-laws.md for cost analysis, team-templates.md for pre-built configurations.*
