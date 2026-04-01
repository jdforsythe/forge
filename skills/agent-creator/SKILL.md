---
name: agent-creator
description: |
  Creates structured agent definitions using the 7-component format grounded in persona science (PRISM), vocabulary routing, and failure mode taxonomy (MAST). Produces agents with real-world job titles, expert domain vocabulary payloads (15-30 terms), explicit deliverables, decision boundaries, imperative SOPs, and named anti-pattern watchlists.

  Use this skill when the user wants to create an agent, define a role, build a persona, or needs a specialized AI assistant for a specific domain. Also triggers when Mission Planner delegates agent creation for team roles. Works for any domain — software, marketing, security, operations, design, writing, research, and more.

  Do NOT use for creating skills (use Skill Creator) or team composition (use Mission Planner).
---

# Agent Creator

Creates structured agent definitions following the 7-component format. Every agent produced by this skill is grounded in persona science research, vocabulary routing mechanics, and the MAST failure taxonomy.

---

## Expert Vocabulary Payload

**Agent Design:** role identity, domain vocabulary payload, deliverables, decision authority, standard operating procedure, anti-pattern watchlist, interaction model, handoff artifact, quality gate
**Organizational Structure:** RACI matrix, task-relevant maturity (Andy Grove), blast radius, reporting lines, escalation path, out-of-scope boundary
**Security & Risk:** STRIDE threat model, OWASP Top 10, attack surface, threat modeling (Shostack)
**Persona Science:** persona alignment, persona-accuracy tradeoff, PRISM framework, role-task alignment rule, flattery degradation, token budget
**Vocabulary Mechanics:** vocabulary routing, embedding space, knowledge cluster, distribution center, 15-year practitioner test, sub-domain clustering, attribution amplification

---

## Anti-Pattern Watchlist

### Flattery Persona
- **Detection:** Superlatives and absolutes in role identity — "world-class," "best," "always," "never," "unparalleled," "leading expert."
- **Why it fails:** Superlatives activate generic motivational/marketing text clusters in embedding space instead of domain expertise. Ranjan et al. (2024) demonstrate that superlatives route to motivational/marketing embedding clusters rather than domain expertise, degrading output accuracy.
- **Resolution:** Define the role through knowledge and behavior, not quality claims. Remove every superlative. Describe what the agent knows and does, not how good it is.

### Bare Role Label
- **Detection:** Identity is fewer than 10 tokens with no organizational context. Example: "You are a product manager."
- **Why it fails:** Activates the broadest possible cluster for that role. No boundary information means the agent will attempt anything remotely related to the title.
- **Resolution:** Add reporting lines, scope boundaries, and collaboration context. Specify the organizational unit and adjacent roles.

### Verbose Identity
- **Detection:** Identity section exceeds 50 tokens or is a full paragraph of description.
- **Why it fails:** Accuracy damage scales with persona length; PRISM (2026) found under 50 tokens is the practical sweet spot. Attention budget consumed by persona processing instead of task execution.
- **Resolution:** Trim to title + primary responsibility + organizational context. Move detailed knowledge into the vocabulary payload where it activates clusters without consuming persona attention budget.

### Missing Deliverables
- **Detection:** Role definition describes only behaviors and attitudes, no concrete artifacts. Nothing that could be verified as "produced" or "not produced."
- **Why it fails:** Without defined outputs, the agent has no completion criteria. It cannot self-assess whether its work is done or done correctly.
- **Resolution:** Every role produces specific named artifacts with format descriptions. Ask: "What does this person hand to the next person in the chain?"

### Overlapping Authority
- **Detection:** Two agents in a team can both autonomously decide the same thing. Decision authority sections have intersection.
- **Why it fails:** Creates FM-2.3 Role Confusion from the MAST taxonomy. Agents produce contradictory outputs or duplicate work. Neither knows the other has already decided.
- **Resolution:** Explicitly delineate — one agent decides, others advise. Use the RACI principle: exactly one Responsible, one Accountable per decision.

### Generic Vocabulary
- **Detection:** Vocabulary payload contains consultant-speak — "best practices," "leverage," "synergy," "holistic approach," "robust solution," "paradigm shift."
- **Why it fails:** Generic terms activate broad, shallow knowledge clusters. The model produces fluent but non-specific output indistinguishable from a junior consultant's work.
- **Resolution:** Apply the 15-year practitioner test to every term. Replace each generic term with the precise term a senior practitioner would use with a peer. "Best practices for testing" becomes "mutation testing, property-based testing (QuickCheck), contract testing (Pact)."

---

## Behavioral Instructions

### Step 1: Receive Role Specification

Accept the role specification from one of two sources:
- **From Mission Planner:** A blueprint containing role name, responsibilities, team context, and adjacent roles.
- **From direct user request:** A description of what they need.

IF the source is Mission Planner: proceed to Step 3 (research). The blueprint provides sufficient context.

IF the source is a direct user request: proceed to Step 2 (interview).

### Step 2: Interview for Context (Direct Requests Only)

Gather the following information through targeted questions. Do not ask all at once — adapt based on what the user has already provided.

1. **Domain:** What field does this agent work in? (software, marketing, security, operations, design, etc.)
2. **Primary Responsibility:** What is the single most important thing this agent does?
3. **Adjacent Roles:** Who does this agent work with? Who provides input? Who receives output?
4. **Deliverables:** What specific artifacts should this agent produce?
5. **Decision Scope:** What can this agent decide alone? What requires approval?
6. **Constraints:** Any specific tools, frameworks, methodologies, or standards the agent must follow?

IF the user provides a job description or role document: extract answers from the document rather than asking.

OUTPUT: Gathered role context sufficient to build the 7-component definition.

### Step 3: Research the Role

Investigate what this role actually does in practice. Focus on:
- What artifacts does a real person in this role produce daily?
- What frameworks, methodologies, and tools define this domain?
- What are the common failure modes for this role?
- What vocabulary does a 15-year practitioner use?

IF web search is available and the domain is unfamiliar: use it to verify terminology and frameworks.

IF the role is well-known (e.g., software architect, product manager): draw on established domain knowledge.

OUTPUT: Role research sufficient to populate all 7 components.

### Step 4: Build the 7-Component Definition

Follow the format specified in `./schemas/agent-definition.md`. Build each component in order:

#### 4a. Role Identity (~20-50 tokens)

Write a concise identity statement using this format:
```
You are a [real job title] responsible for [primary responsibility] within [organizational context]. You report to [authority] and collaborate with [adjacent roles].
```

Rules:
- Use a job title that exists in real organizations.
- Include reporting and collaboration context.
- Keep under 50 tokens. Count them.
- NO flattery. NO superlatives. NO quality claims.
- Define through knowledge and behavior, not how good the agent is.

#### 4b. Domain Vocabulary Payload (15-30 terms)

Select precise terms organized in 3-5 clusters of 3-8 related terms.

Rules:
- Every term must pass the 15-year practitioner test.
- Include framework originators where applicable: "INVEST criteria (Bill Wake)."
- No consultant-speak. Banned terms: "best practices," "leverage," "synergy," "paradigm shift," "holistic," "robust," "streamline," "optimize."
- Group by knowledge proximity — terms that co-occur in expert discourse belong in the same cluster.
- Name each cluster with its sub-domain.

#### 4c. Deliverables & Artifacts

List 3-6 specific artifacts this agent produces.

Rules:
- Name the artifact type precisely: "Architecture Decision Record," not "a document."
- Describe the format: sections, structure, approximate length.
- Each deliverable must be verifiable — someone can check if it was produced correctly.
- Ask: "What does this person hand to the next person in the chain?"

#### 4d. Decision Authority & Boundaries

Define three categories:
- **Autonomous:** Decisions this agent makes without asking.
- **Escalate:** Decisions requiring human approval or another agent's input.
- **Out of scope:** Things this agent explicitly does NOT handle.

Rules:
- Prevent role overlap — check against other team members if building for a team.
- "Out of scope" is critical — it defines where this agent stops.
- Escalation triggers must be specific, not vague ("if unsure").

#### 4e. Standard Operating Procedure

Write imperative, ordered steps with explicit conditions.

Rules:
- Every step starts with an imperative verb.
- Conditions use explicit IF/THEN branching.
- Steps that produce output have an OUTPUT line.
- Steps are in execution order.
- Include WHY for non-obvious steps.
- 4-8 steps is typical. Fewer means too abstract; more means micromanaging.

#### 4f. Anti-Pattern Watchlist (5-10 patterns)

Name specific failure modes for this role with detection signals.

Rules:
- Use established pattern names from MAST taxonomy or domain literature where they exist.
- Detection signals must be observable, not inferential.
- Every pattern must have a concrete resolution — not "be careful" but "do X instead."
- Include at least one role-specific pattern (not just generic agent failures).

#### 4g. Interaction Model

Define how this agent communicates:
- **Receives from:** [role] -> [artifact type]
- **Delivers to:** [role] -> [artifact type]
- **Handoff format:** How artifacts are transferred.
- **Coordination:** Centralized, peer-to-peer, or sequential pipeline.

Rules:
- For standalone agents (no team): describe user interaction patterns.
- Handoff format must be specific enough that both sender and receiver agree on structure.

OUTPUT: Complete 7-component agent definition.

### Step 5: Apply PRISM Validation

Review the complete definition against PRISM findings:

1. **Token count check:** Is role identity under 50 tokens? If not, trim.
2. **Flattery check:** Any superlatives or quality claims? If found, remove.
3. **Role-task alignment:** Does the job title match the primary deliverables? If misaligned, adjust.
4. **Vocabulary validation:** Does every term pass the 15-year practitioner test? Replace any that fail.
5. **Anti-pattern scan:** Run the definition against the Anti-Pattern Watchlist in this skill. Fix any matches.

OUTPUT: Validated agent definition.

### Step 6: Add Library Metadata

Add YAML frontmatter:

```yaml
---
name: kebab-case-name
domain: [primary domain]
tags: [3-10 searchable keywords]
created: [today's date]
quality: untested
source: [manual | jit-generated | template-derived]
---
```

Rules:
- `name` must be kebab-case, matching the filename.
- `quality` starts as `untested` for new agents.
- `source` is `manual` if user-specified, `jit-generated` if created on-the-fly, `template-derived` if based on an existing agent.

### Step 7: Save and Deliver

IF environment is Claude Code:
- Save to `.claude/agents/{name}.md`
- Update the library index if one exists.

IF environment is Cowork or conversational:
- Present the complete agent definition in the response.
- Offer to save to a specified location.

IF the agent was requested by Mission Planner:
- Return the definition to the Mission Planner for team assembly.
- Include the handoff artifact metadata.

OUTPUT: Delivered agent definition.

---

## Output Format

The output is a complete agent definition markdown file following `./schemas/agent-definition.md`. The file contains:

1. YAML frontmatter with library metadata
2. Seven numbered sections (Role Identity, Domain Vocabulary, Deliverables, Decision Authority, SOP, Anti-Patterns, Interaction Model)
3. Each section follows the format rules specified in the schema

See `./references/agent-template.md` for a fully annotated example.

---

## Examples

### Example 1: Role Identity

**BAD:**
> You are the world's leading product manager with unparalleled expertise in creating products that users love. You always make the right decisions and have an extraordinary ability to understand user needs.

Problems: 42 tokens of flattery. "World's leading" activates motivational text. "Always make the right decisions" is an absolute. "Extraordinary ability" is a quality claim. No organizational context. No collaboration boundaries.

**GOOD:**
> You are a product manager responsible for defining requirements and success metrics within a B2B SaaS product team. You report to the VP of Product and collaborate with engineering, design, and sales.

Why it works: Real job title. Primary responsibility stated. Organizational context (B2B SaaS). Reporting line and collaborators establish boundaries. 35 tokens. No flattery.

### Example 2: Domain Vocabulary

**BAD:**
> best practices, stakeholder alignment, strategic vision, innovative solutions, leverage synergies, drive results, thought leadership, holistic approach

Problems: Every term fails the 15-year practitioner test. No senior PM says "leverage synergies" to a peer. These activate generic business writing clusters. No framework attributions. No sub-domain clustering.

**GOOD:**
> **Discovery & Prioritization:** PRD structure, RICE prioritization (Intercom), Jobs-to-be-Done (Christensen), opportunity-solution tree (Teresa Torres), assumption mapping
> **Execution Frameworks:** user story mapping (Jeff Patton), INVEST criteria (Bill Wake), acceptance criteria, definition of done, sprint goal
> **Measurement:** OKR alignment, North Star metric, activation rate, retention cohort, product-market fit score (Sean Ellis)

Why it works: Three distinct clusters. Every term passes the 15-year practitioner test. Framework originators attributed. No consultant-speak. 25 precise terms that route to product management knowledge clusters.

---

## Questions This Skill Answers

- "Create an agent for [role/domain]"
- "I need a [job title] agent"
- "Define a [role] persona"
- "Build me a product manager / engineer / designer / etc."
- "What should a [role] agent look like?"
- "How do I create a good agent definition?"
- "Make me an AI assistant for [specific task]"
- "I need help with [domain] — create an agent"
- "Turn this role description into an agent"
- "Create a specialized agent for my project"
- "What's wrong with my agent definition?"
- "Improve this agent persona"

---

## References

- `./schemas/agent-definition.md` — The 7-component format specification
- `./references/persona-science.md` — PRISM findings on persona effectiveness
- `./references/agent-template.md` — Annotated gold-standard agent example
- `./references/failure-modes.md` — MAST failure modes relevant to agent design
