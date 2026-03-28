# Team Blueprint Format Specification

The Mission Planner outputs team blueprints in this format. A blueprint describes the team composition, artifact flow, and coordination model for achieving a specific goal.

---

## YAML Frontmatter

```yaml
---
goal: "One-sentence description of the project goal"
domain: software | marketing | security | operations | custom
complexity: single-agent | team
topology: sequential-pipeline | parallel-independent | centralized-coordinator | hierarchical
agent_count: N
estimated_cost_tier: low | medium | high
---
```

**Field definitions:**
- `goal`: The user's stated objective
- `domain`: Primary domain category
- `complexity`: Whether this requires one agent or a team (single-agent blueprints have agent_count: 1)
- `topology`: Communication/coordination architecture (see Topology Guide)
- `agent_count`: Number of agents (3-5 for teams; exceeding 5 requires explicit justification)
- `estimated_cost_tier`: Relative token cost (low: <50K tokens total, medium: 50-200K, high: >200K)

---

## Body Structure

### Roles

List each role with a brief description of its purpose in THIS specific project.

```markdown
## Roles
1. **[Role Title]** — [One sentence: what they do in this project, not generic description]
2. **[Role Title]** — [One sentence]
```

### Artifact Chain

The sequence of artifacts that flow between roles. This is the core value — structured handoffs, not free-form dialogue.

```markdown
## Artifact Chain
1. [Role A] produces **[Artifact Name]** ([brief format description])
2. [Role B] receives [Artifact Name], produces **[Artifact Name]** ([brief format description])
3. [Role C] receives [Artifact Name], produces **[Artifact Name]** ([brief format description])
```

### Quality Gates

Which handoffs require review or approval before proceeding.

```markdown
## Quality Gates
- **[Artifact Name]** must be reviewed by [Role/User] before [next step]
- **[Artifact Name]** must pass [specific criteria] before [next step]
```

### Topology Rationale

Why this coordination architecture was chosen for this specific goal.

```markdown
## Topology
**Selected:** [topology name]
**Rationale:** [Why this topology fits this goal — reference task characteristics]
**Alternatives considered:** [Other topologies and why they were rejected]
```

### Anti-Patterns to Guard Against

Team-level failure modes specific to this project type.

```markdown
## Anti-Patterns to Guard Against
- **[Pattern Name]:** [What to watch for and how to prevent it]
```

---

## Complete Example

```markdown
---
goal: "Build a B2B SaaS analytics dashboard product"
domain: software
complexity: team
topology: sequential-pipeline
agent_count: 4
estimated_cost_tier: high
---

## Roles
1. **Product Manager** — Defines the analytics dashboard requirements, user personas, and success metrics for the B2B SaaS product
2. **Software Architect** — Designs the system architecture including data pipeline, API layer, and dashboard rendering approach
3. **Lead Engineer** — Implements the dashboard application based on the architecture, including data visualization components
4. **QA Engineer** — Validates the dashboard against requirements, tests data accuracy, and verifies cross-browser rendering

## Artifact Chain
1. Product Manager produces **Product Requirements Document** (markdown: user personas, feature specs, acceptance criteria, priority matrix)
2. Software Architect receives PRD, produces **Architecture Document** (markdown + Mermaid: component diagram, API contracts, data model, ADRs)
3. Lead Engineer receives Architecture Document, produces **Implementation** (code: application, tests, deployment config)
4. QA Engineer receives Implementation + PRD, produces **Test Results Report** (markdown: test coverage, defect list, acceptance criteria verification)

## Quality Gates
- **PRD** must be reviewed by the user before architecture begins
- **Architecture Document** must be reviewed by the user before implementation begins
- **Implementation** must pass all automated tests before QA review
- **Test Results Report** must show all critical acceptance criteria passing before delivery

## Topology
**Selected:** Sequential pipeline
**Rationale:** SaaS product development has strong sequential dependencies — architecture depends on requirements, implementation depends on architecture, testing depends on implementation. Parallelizing would cause rework.
**Alternatives considered:** Parallel-independent rejected because each phase depends on the previous. Centralized-coordinator unnecessary because the pipeline is linear.

## Anti-Patterns to Guard Against
- **Requirements Drift:** PM changing requirements after architecture is finalized. Prevention: Quality gate requires user sign-off on PRD before proceeding.
- **Gold Plating:** Architect over-designing for hypothetical scale. Prevention: Architecture must justify each component against stated requirements.
- **Integration Blindness:** Engineer building components that don't connect. Prevention: Architecture document includes explicit API contracts and data flow.
- **Happy Path Testing:** QA only testing the expected flow. Prevention: Test plan must include error states, edge cases, and performance scenarios.
```

---

## Topology Decision Matrix

| Task Characteristic | Recommended Topology | Rationale |
|---|---|---|
| Strong sequential dependencies | Sequential pipeline | Each step depends on previous output |
| Independent subtasks, shared goal | Parallel-independent with synthesis | Maximum throughput, coordinator merges results |
| Complex coordination, many dependencies | Centralized coordinator | One agent manages flow, prevents chaos |
| Clear hierarchy, delegation pattern | Hierarchical | Lead delegates, reviews, integrates |
| Uncertain/exploratory | Start with single agent | Add complexity only when demonstrated need requires it |

## Complexity Assessment Criteria

Based on DeepMind scaling research:

1. **Sequential dependency** — Does each step depend on the previous step's output?
   - High → sequential pipeline or single agent
   - Low → parallel-independent viable

2. **Tool density** — Does the task require heavy tool use (file I/O, web search, code execution)?
   - High → single agent (coordination tax exceeds benefit)
   - Low → multi-agent viable

3. **Can a single well-prompted agent handle this?**
   - Yes → Level 0 (single agent). Always try this first.
   - No → Proceed to team design
