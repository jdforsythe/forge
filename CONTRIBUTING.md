# Contributing to Forge

Thank you for your interest in contributing to Forge. This project builds science-backed agent definitions, skills, and team templates for AI-assisted workflows. We value **quality over quantity** — one well-crafted agent definition is worth more than ten sloppy ones.

This guide covers how to contribute, what standards we hold, and how the review process works.

---

## How to Contribute

### Adding a Team Template

Team templates live in `plugins/forge/library/templates/`. Each template is a Markdown file that follows the blueprint format defined in [`plugins/forge/schemas/team-blueprint.md`](plugins/forge/schemas/team-blueprint.md).

1. Create a new `.md` file in `plugins/forge/library/templates/` with a descriptive kebab-case name (e.g., `saas-mvp-launch.md`).
2. Include the required YAML frontmatter (`goal`, `domain`, `complexity`, `topology`, `agent_count`, `estimated_cost_tier`).
3. Include all body sections: Roles, Artifact Chain, Quality Gates, Topology Rationale, and Anti-Patterns to Guard Against.
4. Add **3 realistic test prompts** that demonstrate when this template should be selected.

### Adding Agent Definitions

Agent definitions live in `plugins/forge/library/agents/{domain}/`, organized by domain (e.g., `software`, `marketing`, `security`, `operations`, `design`).

1. Create a new `.md` file named to match the agent's `name` field (e.g., `plugins/forge/library/agents/software/backend-engineer.md`).
2. Follow the 7-component format specified in [`plugins/forge/schemas/agent-definition.md`](plugins/forge/schemas/agent-definition.md).
3. Include YAML frontmatter with all required fields.
4. Add **3 realistic test prompts** that exercise the agent's core capabilities.

### Submitting New Skills

Skills live in `plugins/forge/skills/`. Each skill is defined in a `SKILL.md` file that meets the quality gates described below.

1. Create a directory for your skill with a clear name.
2. Write a `SKILL.md` that passes all quality gates (see Quality Standards).
3. Include any reference files the skill needs, keeping each under 300 lines.
4. Add **3 realistic test prompts** showing the skill in action.

### Improving Documentation

Improvements to docs, schemas, and examples are always welcome. If you spot an error, an unclear explanation, or a missing example, open a PR.

### Reporting Bugs and Requesting Features

- **Bugs:** Open a GitHub Issue with steps to reproduce, expected behavior, and actual behavior.
- **Feature requests:** Open a GitHub Issue describing the use case, why it matters, and a rough idea of the solution.

---

## Quality Standards

Every contribution must meet the standards below. PRs that don't meet these will be asked to revise before merge.

### Agent Definitions

Agent definitions must follow the 7-component format in [`plugins/forge/schemas/agent-definition.md`](plugins/forge/schemas/agent-definition.md):

1. **YAML Frontmatter** — All required fields (`name`, `domain`, `tags`, `created`, `quality`, `source`).
2. **Role Identity** — 20-50 tokens. Real job title. No flattery words ("world-class," "expert").
3. **Domain Vocabulary Payload** — 15-30 terms in 3-5 clusters. Must pass the 15-year practitioner test.
4. **Deliverables & Artifacts** — Concrete, verifiable outputs with format descriptions.
5. **Decision Authority & Boundaries** — Autonomous, escalate, and out-of-scope sections.
6. **SOPs / Workflow Steps** — Imperative verbs, explicit IF/THEN conditions, OUTPUT lines.
7. **Anti-Pattern Watchlist** — 5-10 named patterns with detection signals and concrete resolutions.
8. **Interaction Model** — Receives-from, delivers-to, handoff format, coordination style.

### Skills

Skills must meet these quality gates:

| Gate | Requirement |
|------|-------------|
| Size | `SKILL.md` < 500 lines |
| Reference files | Each < 300 lines |
| Dual-register description | Present (both plain-language and technical) |
| Vocabulary payload | 15-30 domain terms |
| Anti-pattern watchlist | 5+ named patterns |
| Instructions | Imperative, ordered, with explicit conditionals |
| Examples | At least 2 worked examples |
| Questions This Skill Answers | 8+ entries |

### Team Templates

Team templates must follow [`plugins/forge/schemas/team-blueprint.md`](plugins/forge/schemas/team-blueprint.md), including:

- YAML frontmatter with all required fields
- Roles with project-specific descriptions (not generic)
- Artifact Chain showing concrete handoffs
- Quality Gates defining review checkpoints
- Topology Rationale explaining why this coordination model was chosen
- Anti-Patterns to Guard Against specific to the project type

### Test Prompts

Every contributed agent, skill, or template must include **3 realistic test prompts**. These should:

- Cover different aspects of the agent/skill's capabilities
- Be specific enough to produce a verifiable output
- Represent real-world usage, not toy examples

---

## PR Process

1. **Fork and branch.** Create a feature branch from `main` with a descriptive name (e.g., `add-devops-engineer-agent` or `fix-template-frontmatter`).

2. **Make your changes.** Follow the quality standards above.

3. **Run validation.** Check that YAML frontmatter is valid and all required fields are present. At minimum:
   - Frontmatter parses without errors
   - Required fields are populated
   - File is in the correct directory for its type

4. **Submit your PR.** Include a description covering:
   - **What** you added or changed
   - **Why** this contribution is valuable
   - Which test prompts you used to validate the output

5. **Review.** Every PR requires at least one approving review before merge. Reviewers will check against the quality standards above. Address feedback promptly — we try to keep the review cycle short.

---

## Code of Conduct

This project follows the [Contributor Covenant Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/). By participating, you agree to uphold a welcoming, respectful, and harassment-free environment for everyone.

Please report unacceptable behavior by opening a GitHub Issue or contacting the maintainers directly.

---

## Getting Help

- **GitHub Issues** — For bugs, feature requests, and specific questions about a contribution.
- **GitHub Discussions** — For broader questions, ideas, and community conversation.

If you are unsure whether something belongs as an issue or a discussion, start with a discussion. We will convert it if needed.

---

Thank you for helping make Forge better. Quality contributions compound — every well-built agent and skill raises the bar for the entire library.
