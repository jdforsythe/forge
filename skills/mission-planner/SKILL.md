---
name: mission-planner
description: |
  Decomposes goals into team blueprints using evidence-based scaling laws, topology selection, and role design. Determines whether a goal needs a single agent or a coordinated team (3-5 agents max), selects the optimal communication topology (sequential pipeline, parallel-independent, centralized coordinator, hierarchical), and produces structured blueprints with artifact chains and quality gates.

  Use this skill when the user wants to build something, plan a project, assemble a team, figure out what roles they need, break down a complex goal, or asks "how should I approach [X]?" — even if they don't mention agents or teams. Also triggers on specific domains: "build a SaaS," "marketing campaign," "security audit," "write a book," "launch a product."

  Do NOT use for simple single-step tasks, direct coding questions, or file operations that need no planning.
---

# Mission Planner

The entry point and brain of the Forge system. Analyzes user goals, assesses complexity, selects coordination topology, and produces team blueprints or single-agent definitions.

---

## Expert Vocabulary Payload

**Decomposition & Planning:** decision decomposition, value stream mapping, work breakdown structure, vertical slice, task decomposability, blast radius, two-way door decision
**Coordination & Topology:** communication topology, topology selection, centralized coordination, decentralized coordination, pipeline architecture, sequential dependency, coordination overhead
**Team Design:** RACI matrix, Conway's Law, artifact chain, quality gate, handoff artifact, role boundary, capability matching
**Scaling & Efficiency:** tool density, capability saturation, error amplification, cost multiplier, efficiency ratio, cascade pattern, 45% threshold

---

## Anti-Pattern Watchlist

### Premature Multi-Agent
- **Detection:** Goal can be stated in one sentence with no parallel workstreams. No genuinely different expertise required across subtasks.
- **Why it fails:** Coordination tax exceeds the benefit of additional agents. A 3-agent team costs 3.5x tokens for 2.3x output.
- **Resolution:** Try Level 0 first. Only escalate when single agent demonstrably fails the task.

### Role Overlap
- **Detection:** Two agents have overlapping deliverables. Decision authority is ambiguous — both agents could make the same call.
- **Why it fails:** Conflict, duplicated work, inconsistent outputs. Violates RACI — every decision needs exactly one Accountable.
- **Resolution:** Merge overlapping roles into one agent, or explicitly delineate boundaries in Decision Authority sections. If two roles share >30% of deliverables, merge them.

### Missing Verification
- **Detection:** Artifact chain has no review step. Artifacts flow downstream without acceptance criteria.
- **Why it fails:** Enables error cascading — mistakes in early artifacts propagate and amplify through the chain. MetaGPT found structured handoffs reduce errors by ~40%, but only when verified.
- **Resolution:** Add a quality gate at every critical handoff. Define specific acceptance criteria, not just "review."

### Sequential-Parallel Mismatch
- **Detection:** Parallel topology assigned to a task where each step depends on the previous output. Agents block waiting for upstream artifacts.
- **Why it fails:** Agents working in parallel on dependent tasks produce inconsistent work that requires expensive rework to reconcile.
- **Resolution:** Use sequential pipeline when dependencies are strong. Reserve parallel-independent for genuinely independent subtasks.

### Tool-Heavy Single-Agent
- **Detection:** Most work involves file I/O, code execution, web search, or other tool-intensive operations. Multiple agents would all need the same tools.
- **Why it fails:** Coordination tax on tool-heavy tasks exceeds multi-agent benefit. Agents spend more tokens coordinating tool access than doing useful work.
- **Resolution:** Single agent with tool augmentation (Level 1). Multi-agent adds overhead without capability diversity.

### Agent Bloat
- **Detection:** Team has more than 5 roles. Roles exist for narrow subtasks that could be responsibilities within a broader role (e.g., separate "formatter," "namer," "documenter" agents).
- **Why it fails:** At 7+ agents, performance typically degrades. Communication channels scale as N*(N-1)/2 — at 7 agents that is 21 channels.
- **Resolution:** Merge adjacent roles. Target 3-5 agents. Every agent must bring genuinely different expertise. The "would a real company hire a separate person for this?" test.

---

## Behavioral Instructions

### Phase 1: Gather Context

1. Read project context if available.
   IF CLAUDE.md exists in project root: Parse for project constraints, tech stack, team preferences.
   IF existing files present: Scan structure to understand current state.
   OUTPUT: Project context summary (or "greenfield — no existing context").

2. Check library for existing resources.
   IF ./library/index.json exists: Load and scan for matching agents, skills, and templates.
   IF matching agents found: Note them for potential reuse.
   IF matching template found: Note it for potential adaptation.
   OUTPUT: Available library resources list.

### Phase 2: Assess Complexity

3. Classify the goal.
   Identify: primary domain (software, marketing, security, operations, custom).
   Identify: project archetype (product build, campaign, audit, content creation, research, operations).
   OUTPUT: Domain and archetype classification.

4. Evaluate complexity using DeepMind criteria.
   a. **Sequential dependency:** Does each step depend on the previous step's output?
      - High sequential dependency → favors single agent or sequential pipeline.
      - Low sequential dependency → parallel topology viable.
   b. **Tool density:** Does the task require heavy tool use (file I/O, code execution, web search)?
      - High tool density → strongly favors single agent (Level 0-1).
      - Low tool density → multi-agent viable.
   c. **Task decomposability:** Can subtasks be defined with typed artifact interfaces?
      - Yes → multi-agent viable.
      - No (requires continuous back-and-forth) → single agent.
   d. **Expertise diversity:** Do subtasks require genuinely different knowledge domains?
      - Yes → multi-agent justified.
      - No → single agent with broader prompt.
   e. **Single-agent test:** Can a single well-prompted agent handle this?
      - IF yes → Level 0. Stop here.
      - IF no → proceed to team design.
   OUTPUT: Complexity assessment with Level determination (0, 1, 2, or 3).

### Phase 3: Route by Level

5. **IF Level 0 — Single Agent Sufficient:**
   Produce one agent definition following ./schemas/agent-definition.md format.
   Include: role identity, domain vocabulary, deliverables, decision authority, SOP, anti-patterns, interaction model.
   IF matching agent exists in library: Load and adapt rather than creating from scratch.
   Package and present to user.
   DONE.

6. **IF Level 1 — Team Warranted, Known Pattern:**
   a. Select communication topology using the topology decision matrix (see ./references/topology-guide.md).
   b. IF matching template exists in ./library/templates/: Load template and adapt to specific goal.
   c. IF no template: Design team from scratch using topology selection rules.
   d. Determine team size: start at 3 agents, add only if genuinely different expertise required. Never exceed 5 without explicit justification.
   e. Define artifact chain: every agent produces a typed deliverable, every handoff has explicit format.
   f. Define quality gates: identify critical handoffs that require review before proceeding.
   g. Present blueprint to user following ./schemas/team-blueprint.md format.
   h. WAIT for user approval before proceeding.
   i. Upon approval, for each role in the blueprint:
      IF matching agent exists in library: Load and adapt.
      IF no match: Create agent definition (invoke Agent Creator skill or produce inline).
   j. Package all artifacts: blueprint + agent definitions.
   k. Present to user.
   DONE.

7. **IF Level 2 — Novel Domain or Ambiguous Goal:**
   a. Perform decision decomposition: break goal into decision points, identify unknowns.
   b. Present reasoning to user:
      - What you understood the goal to be
      - Why this requires a team (not single agent)
      - Proposed topology and rationale
      - Open questions or assumptions
   c. WAIT for user feedback.
   d. Iterate on blueprint design based on feedback.
   e. Once goal is clarified, proceed as Level 1 (step 6).
   DONE.

### Phase 4: Finalize

8. Log library usage.
   IF any library items were loaded (agents, templates, skills):
   Append usage record to usage-log.jsonl with: timestamp, item loaded, goal context, modifications made.

---

## Output Format

The Mission Planner produces team blueprints following the format specified in `./schemas/team-blueprint.md`.

For single-agent results (Level 0), produce an agent definition following `./schemas/agent-definition.md`.

Every blueprint includes:
- YAML frontmatter (goal, domain, complexity, topology, agent_count, estimated_cost_tier)
- Roles section with specific responsibilities for THIS project
- Artifact chain with typed deliverables and explicit handoff formats
- Quality gates with specific acceptance criteria
- Topology rationale with alternatives considered
- Anti-patterns to guard against for this specific project type

---

## Examples

### Example 1: Simple Goal — Avoid Over-Engineering

**User says:** "Build me a blog"

**BAD response:**
Creates a 5-agent team: Content Strategist, Information Architect, Frontend Developer, Backend Developer, QA Engineer. Massive coordination overhead for a straightforward task.

**Why it is bad:** A blog is a well-understood, low-complexity project. A single well-prompted agent with knowledge of web frameworks can handle requirements, design, and implementation. The 45% threshold tells us a single agent covers this easily. Five agents would cost 7x tokens for marginal improvement.

**GOOD response:**
```yaml
---
goal: "Build a personal blog"
domain: software
complexity: single-agent
topology: n/a
agent_count: 1
estimated_cost_tier: low
---
```
Produces one agent definition: a Full-Stack Developer agent with blog-specific SOP covering tech selection, content model design, implementation, and deployment. Single agent, clear deliverables, done.

### Example 2: Complex Goal — Ensure Specificity

**User says:** "Build a B2B SaaS analytics product"

**BAD response:**
Produces vague roles: "Technical Lead — handles technical stuff," "Designer — does design." No artifact chain. No quality gates. No topology rationale. Agents have overlapping responsibilities.

**Why it is bad:** Without explicit artifact chains, agents produce inconsistent work. Without quality gates, errors cascade. Without topology rationale, coordination is ad-hoc. Vague role descriptions fail the "would a senior practitioner recognize this as their job?" test.

**GOOD response:**
```yaml
---
goal: "Build a B2B SaaS analytics dashboard product"
domain: software
complexity: team
topology: sequential-pipeline
agent_count: 4
estimated_cost_tier: high
---
```
Specific roles: Product Manager (defines requirements and acceptance criteria), Software Architect (designs system architecture and API contracts), Lead Engineer (implements application and tests), QA Engineer (validates against requirements and tests edge cases). Each role has explicit deliverables flowing to the next. Quality gates require user sign-off on PRD and architecture before proceeding. Topology rationale explains why sequential pipeline fits the strong dependencies.

---

## Environment Branching

### Claude Code Environment
- Write agent definitions to `.claude/agents/[agent-name].md`
- Write skill definitions to `.claude/skills/[skill-name].md`
- Write team blueprints to `.claude/teams/[team-name].md` or present inline
- Reference library items by relative path from project root

### Cowork / Claude.ai Environment
- Package agents as `.skill` files for installation
- Present blueprints inline in conversation
- Provide copy-paste ready agent definitions
- Include installation instructions for each artifact

---

## Questions This Skill Answers

This skill activates when the user asks any of the following (or variations):

- "I want to build [product/project/thing]"
- "What team do I need for [goal]?"
- "Help me plan [project]"
- "How should I approach [complex task]?"
- "Who do I need to [accomplish goal]?"
- "Should I use multiple agents for this?"
- "Create a team to [goal]"
- "What roles would a real company have for [project type]?"
- "Help me break down [large goal] into manageable work"
- "I need agents for [domain]"
- "Build a SaaS / marketing campaign / security audit"
- "How many agents do I need?"
- "Is this too complex for one agent?"
- "What is the best way to organize [project]?"
- "Plan the architecture for [system]"

---

## References

- `./references/scaling-laws.md` — DeepMind scaling criteria, 45% threshold, cost multipliers
- `./references/topology-guide.md` — Topology decision matrix and selection flowchart
- `./references/team-templates.md` — Pre-built team templates for common project archetypes
- `./schemas/team-blueprint.md` — Output format specification
- `./schemas/agent-definition.md` — Agent definition format for individual roles
