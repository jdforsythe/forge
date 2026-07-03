# Failure Modes Reference

> Condensed operational extract of `docs/research/failure-taxonomy.md` for the Agent Creator skill — the failure modes an individual agent definition can prevent. Part 1 is the real MAST taxonomy, reported faithfully. Part 2 is the relevant slice of the Forge Watchlist: engineering heuristics, not published findings, mapped to their nearest real MAST modes.

---

## Part 1: MAST, Briefly

**MAST** = Multi-Agent System Failure Taxonomy (Cemri et al., arXiv:2503.13657, NeurIPS 2025). 14 failure modes in 3 categories, from 1,600+ annotated traces across 7 frameworks.

| Category | Share | What it covers |
|---|---|---|
| **Specification issues** | **41.77%** | Poor task/role specification and system design |
| Inter-agent misalignment | 36.94% | Coordination, communication, staying on task |
| Task verification | 21.30% | Missing, incomplete, or incorrect output verification |

**Specification issues are the largest category — and agent definitions are the fix.** A precise Role Identity, Decision Authority, and SOP is a direct intervention on FC1. Real named modes worth knowing: FM-1.3 Step Repetition (17.14%, the single most common mode), FM-2.3 Task Derailment, FM-3.3 Incorrect Verification, FM-3.1 Premature Termination, and FM-1.2 Disobey Role Specification (rare in practice — 0.5% — don't over-engineer against it).

The taxonomy does **not** contain modes named "Rubber-Stamp Approval," "Role Confusion," or "Confident Ignorance." Those are Forge's own watchlist (Part 2) and must never be cited as MAST.

---

## Part 2: Forge Watchlist Items for Single-Agent Design

These four watchlist items are what the Agent Creator builds against when writing one agent's definition. W-3 (Unbounded Review Loops), W-5 (Error Cascade), and W-6 (Handoff Loss) are team-level concerns — Mission Planner's job when assembling multi-agent topologies, not addressed here.

### W-1 Rubber-Stamp Review
**Nearest MAST analogs:** FM-3.2 No or incomplete verification, FM-3.3 Incorrect verification.

**What happens:** A review agent approves work without meaningful critique — only praise, near-instant approval, no findings. Driven by sycophancy and weak review prompting.

**What the Agent Creator adds to prevent it:**
- SOP step mandating at least one finding, or an explicitly evidenced "no issues" clearance.
- A findings template in Deliverables: Issue, Severity, Location, Recommendation.
- Identity framed adversarially for review roles: "responsible for identifying defects and risks," not "responsible for reviewing."
- Add "Rubber-Stamp Review (Forge watchlist W-1)" to the anti-pattern watchlist of any agent with review responsibility.

### W-2 Role Overlap
**Nearest MAST analog:** FM-1.2 Disobey role specification (rare in the data at 0.5% — design for it cheaply).

**What happens:** Two agents both own a decision, producing duplicated or contradictory outputs.

**What the Agent Creator adds to prevent it:**
- All three Decision Authority categories populated: Autonomous, Escalate, Out of Scope.
- Out of Scope lists at least 3 specific areas, not broad categories ("technology selection within approved stack," not "technical decisions").
- When building agents for a team, cross-reference Decision Authority sections across agents to verify zero intersection.

### W-4 Confident Overreach
**Nearest MAST analogs:** FM-2.2 Fail to ask for clarification, FM-2.6 Reasoning-action mismatch.

**What happens:** An agent asked to work beyond its defined scope produces fluent, confident, wrong output instead of flagging the limit.

**What the Agent Creator adds to prevent it:**
- Explicit capability boundaries in Out of Scope: name the tasks that require deeper specialization than this agent's vocabulary payload covers.
- An escalation trigger for out-of-depth situations: "IF task requires knowledge outside your vocabulary payload domains: acknowledge the limitation and escalate."
- SOP instruction to ground status and progress claims in artifacts or tool output rather than narrative — treat "I can't verify this" as a valid, expected output, not a failure.

### W-7 Premature Done
**Nearest MAST analog:** FM-3.1 Premature termination (7.82%).

**What happens:** The agent (or team) declares success before acceptance criteria are met.

**What the Agent Creator adds to prevent it:**
- Deliverables define concrete, checkable acceptance criteria per artifact (sections, format, length) — not "a document."
- SOP's final step ties completion to the criteria list, not to a narrative summary: "Verify against the Deliverables acceptance criteria before reporting done."
- For reviewer/verifier agents: the SOP must check the artifact against the stated criteria list, never against how confident the narrative sounds.

---

## Prevention Checklist for Agent Creator

| Check | Prevents | How to Verify |
|---|---|---|
| Out of Scope has 3+ specific items | W-2 Role Overlap | Count items in Out of Scope |
| No decision-authority overlap across a team | W-2 Role Overlap | Cross-reference authority sections |
| Review agents mandate issue-finding + findings template | W-1 Rubber-Stamp Review | Check SOP for adversarial review step |
| Capability boundaries + escalation trigger stated | W-4 Confident Overreach | Check Out of Scope / Escalate for depth limits |
| Status claims grounded in artifacts, not narrative | W-4 Confident Overreach | Check SOP for evidence requirement |
| Acceptance criteria defined per deliverable | W-7 Premature Done | Check Deliverables for checkable criteria |
| Completion tied to criteria, not narrative | W-7 Premature Done | Check final SOP step |

---

*Condensed from `docs/research/failure-taxonomy.md`. Full bibliography: `docs/research/source-index.md`.*
