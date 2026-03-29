# Failure Taxonomy (MAST Framework)

> Reference for Forge skill design. The MAST (Multi-Agent System Testing) taxonomy of 14 failure modes across communication, coordination, and quality categories.

## Table of Contents
- [Overview](#overview)
- [FM-1.x: Communication Failures](#fm-1x-communication-failures)
- [FM-2.x: Coordination Failures](#fm-2x-coordination-failures)
- [FM-3.x: Quality Failures](#fm-3x-quality-failures)
- [Frequency Summary](#frequency-summary)
- [Prevention Strategy Matrix](#prevention-strategy-matrix)

---

## Overview

MAST identifies 14 failure modes in three categories:
- **FM-1.x Communication (4 modes):** Failures in message transmission between agents.
- **FM-2.x Coordination (5 modes):** Failures in task orchestration and role management.
- **FM-3.x Quality (5 modes):** Failures in output correctness and value.

---

## FM-1.x: Communication Failures

### FM-1.1 Message Loss
- **Description:** Agent output fails to reach intended recipient. Caused by context overflow, truncation, or routing error.
- **Frequency:** Common in systems with >4 agents.
- **Detection:** Downstream agent produces output that ignores upstream artifact. Missing references.
- **Prevention:** Structured artifact handoffs with explicit delivery confirmation. Size limits on inter-agent messages. Chunked transmission for large artifacts.

### FM-1.2 Misinterpretation
- **Description:** Receiving agent interprets artifact differently than sender intended. Ambiguous formats, missing schemas, implicit assumptions.
- **Frequency:** Very common without structured handoff formats.
- **Detection:** Downstream output is internally consistent but doesn't match upstream intent. Schema violations.
- **Prevention:** Typed artifacts with explicit schemas. Shared vocabulary payload. Verification step after receipt.

### FM-1.3 Partial Transmission
- **Description:** Only part of an artifact is transmitted. Context window limits or truncation cut off the message.
- **Frequency:** Moderate. Increases with artifact size and team size.
- **Detection:** Downstream agent references only early sections of upstream artifact. Missing sections in handoff.
- **Prevention:** Chunked transmission with completeness checks. Size-aware artifact design. Keep artifacts under context window limits.

### FM-1.4 Stale Context
- **Description:** Agent operates on outdated information because it missed an update from another agent.
- **Frequency:** Common in parallel topologies where agents work concurrently.
- **Detection:** Agent produces output based on superseded version of an artifact. Version conflicts.
- **Prevention:** Version-stamped artifacts. Explicit invalidation signals when artifacts are updated. Sequential topology eliminates this risk.

---

## FM-2.x: Coordination Failures

### FM-2.1 Deadlock
- **Description:** Two or more agents wait for each other's output, creating a circular dependency. Neither can proceed.
- **Frequency:** Rare with proper DAG topology design. Common in ad-hoc systems.
- **Detection:** No agent produces output for an extended period. Circular wait pattern in dependency graph.
- **Prevention:** Directed acyclic graph (DAG) topology enforced at design time. Timeout-based deadlock detection with fallback to coordinator.

### FM-2.2 Livelock
- **Description:** Agents repeatedly exchange messages without making progress. Caused by conflicting instructions, oscillating revisions, or review loops without convergence criteria.
- **Frequency:** Moderate in debate/review topologies.
- **Detection:** Message count between agents exceeds threshold. Same issues raised repeatedly. No net progress between iterations.
- **Prevention:** Maximum iteration limits — cap review rounds at 3. Escalation to human after limit reached. Convergence criteria defined upfront.

### FM-2.3 Role Confusion
- **Description:** Agent performs tasks outside its defined scope, duplicating or contradicting another agent's work.
- **Frequency:** Common without explicit decision authority boundaries.
- **Detection:** Two agents produce overlapping or contradictory outputs. Agent addresses topics in its "out of scope" list.
- **Prevention:** Explicit decision authority matrix with autonomous/escalate/out-of-scope boundaries per agent. Non-overlapping responsibility assignments.

### FM-2.4 Authority Vacuum
- **Description:** A necessary decision falls outside all agents' defined authority. No agent acts, causing a stall.
- **Frequency:** Moderate. Often emerges from incomplete authority mapping.
- **Detection:** Progress stalls with no deadlock — agents are active but avoiding a decision. Decision requests go unanswered.
- **Prevention:** Complete authority mapping at team design time. Default escalation path to coordinator or human for unmapped decisions.

### FM-2.5 Cascade Stall
- **Description:** Failure in one pipeline stage blocks all downstream agents. Entire pipeline halts.
- **Frequency:** Inherent risk in sequential pipelines.
- **Detection:** Downstream agents idle. No output from a mid-pipeline stage.
- **Prevention:** Timeout-based fallbacks. Partial-progress handoffs (deliver what's available). Graceful degradation — downstream agents proceed with reduced input when possible.

---

## FM-3.x: Quality Failures

### FM-3.1 Rubber-Stamp Approval
- **Description:** Review agent approves work without meaningful critique. Caused by sycophantic tendencies in LLMs and weak review prompting.
- **Frequency:** **Very common.** The single most frequent quality failure in multi-agent systems.
- **Detection:** Review output contains only praise. No issues identified across multiple reviews. Approval latency is very short.
- **Prevention:** Require reviewers to identify at least one issue OR explicitly justify "no issues found" with specific evidence. Adversarial review prompting ("your job is to find problems"). Structural mandate: reviewer must fill in a findings template.

### FM-3.2 Error Cascading
- **Description:** Error in an early stage propagates through subsequent stages, amplified at each step. Each downstream agent builds on the flawed foundation.
- **Frequency:** Common in sequential pipelines without quality gates.
- **Detection:** Final output contains errors traceable to early-stage decisions. Downstream agents confidently build on upstream mistakes.
- **Prevention:** Quality gates between stages with rejection authority. Independent verification at critical handoffs. Allow downstream agents to send work back upstream.

### FM-3.3 Capability Saturation
- **Description:** Agent is asked to perform a task beyond its effective capability. Produces confident but low-quality output rather than acknowledging limitations.
- **Frequency:** Moderate. Increases with task complexity.
- **Detection:** Output is fluent and confident but factually wrong or structurally flawed. Agent does not flag uncertainty.
- **Prevention:** Task-capability matching at design time. Explicit capability boundaries in agent definitions. Escalation triggers for complexity thresholds.

### FM-3.4 Groupthink
- **Description:** Multiple agents converge on the same flawed approach because they share similar biases from training data. No genuine diversity of perspective.
- **Frequency:** Moderate in consensus-seeking topologies.
- **Detection:** All agents reach the same conclusion without substantive debate. No alternatives considered.
- **Prevention:** Assign distinct evaluation criteria to each agent. Use adversarial/red-team topology for high-stakes decisions. Require agents to generate alternatives before converging.

### FM-3.5 Lowest Common Denominator
- **Description:** Team output quality converges to the weakest agent's capability. Downstream agents cannot compensate for fundamentally flawed upstream artifacts.
- **Frequency:** Common in pipeline topologies.
- **Detection:** Final output quality matches the weakest stage, not the strongest. Strong downstream agents produce mediocre output because input was poor.
- **Prevention:** Quality gates with rejection authority at every handoff. Allow downstream agents to reject and return upstream work. Match agent capability to stage difficulty.

---

## Frequency Summary

| Failure Mode | Frequency | Category |
|---|---|---|
| FM-3.1 Rubber-Stamp Approval | Very common | Quality |
| FM-1.2 Misinterpretation | Very common | Communication |
| FM-1.1 Message Loss | Common | Communication |
| FM-1.4 Stale Context | Common | Communication |
| FM-2.3 Role Confusion | Common | Coordination |
| FM-3.2 Error Cascading | Common | Quality |
| FM-3.5 Lowest Common Denominator | Common | Quality |
| FM-2.5 Cascade Stall | Common | Coordination |
| FM-1.3 Partial Transmission | Moderate | Communication |
| FM-2.2 Livelock | Moderate | Coordination |
| FM-2.4 Authority Vacuum | Moderate | Coordination |
| FM-3.3 Capability Saturation | Moderate | Quality |
| FM-3.4 Groupthink | Moderate | Quality |
| FM-2.1 Deadlock | Rare | Coordination |

---

## Prevention Strategy Matrix

| Strategy | Prevents | Implementation |
|---|---|---|
| Structured artifact handoffs | FM-1.1, FM-1.2, FM-1.3 | Typed schemas, size limits, completeness checks |
| Version-stamped artifacts | FM-1.4 | Version field in artifact metadata |
| DAG topology enforcement | FM-2.1 | Validate no cycles at team design time |
| Iteration limits (max 3 rounds) | FM-2.2 | Hard cap on review loops with human escalation |
| Decision authority matrix | FM-2.3, FM-2.4 | Autonomous/escalate/out-of-scope per agent |
| Timeout-based fallbacks | FM-2.5 | Partial-progress handoffs on timeout |
| Adversarial review prompting | FM-3.1 | Require issue identification or justified clearance |
| Quality gates with rejection | FM-3.2, FM-3.5 | Independent verification, return-to-sender authority |
| Capability boundary definitions | FM-3.3 | Explicit limits in agent definitions |
| Distinct evaluation criteria | FM-3.4 | Each reviewer gets different assessment dimensions |

---

*Source: Skill Design Research Synthesis §5.1-5.4, MAST framework*
