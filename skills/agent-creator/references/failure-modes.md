# Failure Modes Reference

> Focused extract of the MAST (Multi-Agent System Testing) taxonomy relevant to individual agent design. These are the failure modes the Agent Creator must prevent when building a single agent definition.

---

## Scope

The full MAST taxonomy covers 14 failure modes across communication, coordination, and quality categories. This reference covers the subset that affects **individual agent design** — failure modes that the Agent Creator can prevent through better agent definitions. Team-level failures (deadlock, livelock, groupthink) are handled by Mission Planner.

---

## FM-2.3: Role Confusion

**Category:** Coordination
**Frequency:** Common without explicit decision authority boundaries.

**What happens:** An agent performs tasks outside its defined scope, duplicating or contradicting another agent's work. Two agents both attempt the same decision. Outputs conflict.

**Detection signals:**
- Two agents produce overlapping outputs on the same topic.
- An agent addresses topics listed in its "out of scope" section.
- Decision authority sections of two agents in a team have non-empty intersection.
- Agent produces artifacts not listed in its Deliverables section.

**Root cause in agent design:**
- Missing or vague "out of scope" boundaries.
- Decision authority uses broad categories ("technical decisions") instead of specific decisions ("API contract design," "database schema").
- No explicit list of what the agent does NOT do.

**Prevention in Agent Creator:**
- Every agent definition MUST have all three authority categories: Autonomous, Escalate, Out of Scope.
- "Out of scope" must list at least 3 specific areas this agent does not handle.
- When building agents for a team, cross-reference decision authority sections to verify no overlap.
- Decision categories must be specific enough to be unambiguous. "Technical decisions" is too broad. "Technology selection within approved stack" is checkable.

---

## FM-3.1: Rubber-Stamp Approval

**Category:** Quality
**Frequency:** Very common. The single most frequent quality failure in multi-agent systems.

**What happens:** A review agent approves work without meaningful critique. Every review comes back positive. Issues are never identified. The review step adds latency but no value.

**Detection signals:**
- Review output contains only praise — "looks great," "well done," "comprehensive."
- No issues identified across multiple review cycles.
- Approval is returned very quickly (suggesting no real analysis).
- Review feedback is generic, not specific to the artifact reviewed.

**Root cause in agent design:**
- Agent's SOP does not mandate finding issues.
- No structural requirement to fill a findings template.
- Agent identity includes sycophantic tendencies not counteracted by explicit instructions.
- Review criteria are not specified — agent has no rubric for what "good" means.

**Prevention in Agent Creator:**
- Any agent with a review responsibility MUST have an SOP step that says: "Identify at least one issue or concern. IF no issues found, provide specific evidence justifying clearance."
- Include a findings template in the deliverables: Issue, Severity, Location, Recommendation.
- Add "Rubber-Stamp Approval (MAST FM-3.1)" to the anti-pattern watchlist of every reviewer agent.
- Frame the review role adversarially in the identity: "responsible for identifying defects and risks" not "responsible for reviewing."

---

## FM-3.2: Error Cascading

**Category:** Quality
**Frequency:** Common in sequential pipelines without quality gates.

**What happens:** An error in an early-stage agent's output propagates through subsequent agents. Each downstream agent builds on the flawed foundation, often amplifying the error. By the final output, the error is deeply embedded and difficult to trace.

**Detection signals:**
- Final output contains errors traceable to early-stage decisions.
- Downstream agents confidently build on upstream mistakes without questioning them.
- Errors compound — a small wrong assumption becomes a fundamentally flawed architecture.

**Root cause in agent design:**
- No verification step in the SOP for incoming artifacts.
- Agent trusts all inputs as correct without validation.
- No SOP instruction to question upstream assumptions.
- Missing quality gate between pipeline stages.

**Prevention in Agent Creator:**
- Every agent that receives input from another agent MUST have an SOP step: "Validate incoming artifact against expected format and key assumptions. IF inconsistencies found: flag to sender before proceeding."
- Include in the anti-pattern watchlist: "Uncritical Input Acceptance — proceeding with upstream artifacts without validation."
- Decision authority should include: "Escalate: incoming artifact appears inconsistent or incomplete."
- Interaction model should specify validation expectations for received artifacts.

---

## FM-3.3: Capability Saturation

**Category:** Quality
**Frequency:** Moderate. Increases with task complexity.

**What happens:** An agent is asked to perform a task beyond its effective capability. Instead of acknowledging limitations, it produces confident but low-quality output. The output reads well (fluent, structured) but is factually wrong or structurally flawed.

**Detection signals:**
- Output is fluent and well-formatted but factually incorrect or logically flawed.
- Agent does not flag uncertainty or limitations.
- Task complexity exceeds the domain knowledge activated by the agent's vocabulary payload.
- Agent produces generic advice instead of specific, actionable guidance.

**Root cause in agent design:**
- Agent identity and vocabulary cover a broad domain but the task requires deep specialization.
- No explicit capability boundaries defined.
- No SOP instruction to recognize and flag out-of-depth situations.
- Vocabulary payload is too shallow — 15 generic terms instead of 15 precise terms.

**Prevention in Agent Creator:**
- Define explicit capability boundaries in the Decision Authority section: "Out of scope: [tasks requiring deeper specialization]."
- Include an SOP step: "IF task requires knowledge outside your vocabulary payload domains: acknowledge the limitation and escalate."
- Vocabulary payload must be deep, not broad. 15 precise terms in a focused domain outperform 30 generic terms across a wide domain.
- Include "Confident Ignorance (MAST FM-3.3)" in the anti-pattern watchlist: "Detection: producing specific recommendations in areas outside defined vocabulary clusters. Resolution: acknowledge limitation and recommend specialist."

---

## FM-1.2: Misinterpretation

**Category:** Communication
**Frequency:** Very common without structured handoff formats.

**What happens:** A receiving agent interprets an artifact differently than the sender intended. The downstream output is internally consistent but does not match upstream intent. Both agents believe they are correct.

**Detection signals:**
- Downstream output is coherent but addresses a different problem than upstream specified.
- Key terms are used with different meanings by different agents.
- Downstream agent's summary of the upstream artifact does not match the original.

**Root cause in agent design:**
- Interaction model does not specify artifact schemas.
- Shared vocabulary is not established between interacting agents.
- Handoff format is vague ("send a message") instead of structured ("markdown document with sections X, Y, Z").
- No verification step after receiving an artifact.

**Prevention in Agent Creator:**
- Interaction model MUST specify artifact types and formats for every handoff.
- Deliverables MUST describe the structure (sections, format) of each artifact.
- When building agents for a team, ensure vocabulary payloads share key terms that cross role boundaries.
- Include an SOP step: "After receiving input, summarize your understanding of the key requirements before proceeding. IF the sender is available, confirm your understanding."

---

## FM-2.4: Authority Vacuum

**Category:** Coordination
**Frequency:** Moderate. Emerges from incomplete authority mapping.

**What happens:** A necessary decision falls outside all agents' defined authority. No agent acts. Progress stalls without a deadlock — agents are active but avoiding the unmapped decision.

**Detection signals:**
- Progress stalls with no circular dependency.
- Decision requests go unanswered or are deflected.
- Agents produce outputs that carefully avoid committing to the unmapped decision.
- The gap only becomes visible when the team encounters a situation not anticipated during design.

**Root cause in agent design:**
- Decision authority sections were designed in isolation — each agent's boundaries make sense individually but collectively leave gaps.
- Edge cases and cross-cutting concerns are not mapped to any agent.
- No default escalation path for unmapped decisions.

**Prevention in Agent Creator:**
- When building agents for a team, enumerate known decision categories and verify complete coverage across all agents.
- Every agent MUST have an escalation path: "IF a decision falls outside all defined authority boundaries: escalate to [coordinator/human]."
- Include cross-cutting concerns in at least one agent's authority: security, compliance, performance, accessibility.
- Design the authority mapping as a complete RACI matrix: every decision type has exactly one Responsible party.

---

## Summary: Prevention Checklist for Agent Creator

When building any agent definition, verify these failure mode preventions are in place:

| Check | Prevents | How to Verify |
|---|---|---|
| Out of Scope has 3+ specific items | FM-2.3 Role Confusion | Count items in Out of Scope |
| No decision authority overlap with team | FM-2.3 Role Confusion | Cross-reference authority sections |
| Review agents mandate issue-finding | FM-3.1 Rubber-Stamp | Check SOP for adversarial review step |
| Input validation step in SOP | FM-3.2 Error Cascading | Check SOP for incoming artifact validation |
| Capability boundaries stated | FM-3.3 Capability Saturation | Check Out of Scope for complexity limits |
| Escalation for unknown situations | FM-3.3 Capability Saturation | Check Escalate section for uncertainty trigger |
| Artifact formats specified | FM-1.2 Misinterpretation | Check Deliverables for structure descriptions |
| Handoff schemas in Interaction Model | FM-1.2 Misinterpretation | Check Interaction Model for format details |
| Default escalation path exists | FM-2.4 Authority Vacuum | Check for catch-all escalation rule |
| Anti-pattern watchlist has 5+ items | All | Count anti-patterns |

---

*Source: Extracted from docs/research/failure-taxonomy.md (MAST framework) for Agent Creator skill use.*
