# Forge

Science-backed AI team assembly. From goal to agents to artifacts.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Contributions Welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg)](CONTRIBUTING.md)

Forge is an open-source system that uses research from DeepMind, PRISM persona science, and context engineering to assemble AI agent teams. It takes a goal, determines whether you need one agent or a team, selects the right coordination topology, and produces structured agent definitions with expert vocabulary, clear deliverables, and anti-pattern guardrails. Works with Claude Code and Cowork.

## The Core Insight

The single highest-leverage intervention in AI agent quality is **vocabulary routing** — using precise domain terminology that activates expert knowledge clusters in the model's embedding space. Real-world job titles and role structures activate relevant training data better than custom personas. And DeepMind's scaling research shows that more agents isn't always better — teams of 3-5 with structured artifact handoffs outperform larger groups.

## Quick Start: Claude Code

```bash
# 1. Clone into your Claude config
git clone https://github.com/jdforsythe/meta-skill.git ~/.claude/forge

# 2. In any project, just describe what you want:
"Build me a SaaS analytics product"  # Mission Planner activates
"Create an agent for code review"     # Agent Creator activates
```

## Quick Start: Cowork

1. Copy `skills/` to your Cowork skills folder.
2. Start a conversation: *"I need to plan a marketing campaign"*

## What's Included

```
forge/
├── skills/          4 core meta-skills
│   ├── mission-planner/   Decomposes goals into team blueprints
│   ├── agent-creator/     Builds individual agent definitions
│   ├── skill-creator/     Creates reusable skill packages
│   └── librarian/         Manages the agent/template library
│
├── agents/          3 infrastructure agents
│   ├── verifier.md        Validates outputs against schemas
│   ├── researcher.md      Gathers context and source material
│   └── reviewer.md        Reviews and critiques agent definitions
│
├── library/         Starter collection
│   └── index.json         11 domain agents, 3 team templates
│       ├── software/      Product Manager, Architect, Lead Engineer, QA
│       ├── marketing/     Campaign Strategist, Content Creator, Designer, Analytics Lead
│       └── security/      Lead Auditor, Penetration Tester, Compliance Analyst
│
├── schemas/         Format specifications
│   ├── agent-definition.md    7-component agent structure
│   ├── team-blueprint.md      Blueprint format for coordinated teams
│   ├── index-schema.json      Library index format
│   └── usage-log-schema.json  Usage tracking format
│
├── research/        Scientific foundation (8 reference documents)
│   ├── scaling-laws.md        DeepMind multi-agent scaling findings
│   ├── vocabulary-routing.md  Domain terminology activation patterns
│   ├── persona-science.md     PRISM persona research
│   ├── team-design.md         Topology selection and team structure
│   ├── context-engineering.md Context window optimization
│   ├── failure-taxonomy.md    Common failure modes and mitigations
│   ├── master-synthesis.md    Unified framework across all research
│   └── source-index.md        Full bibliography
│
├── docs/            User documentation
└── schemas/         Format specifications
```

## How It Works

Forge uses a 3-level decision flow:

**Level 0 — Single Agent.** The goal is simple enough for one agent. Forge produces a single well-prompted agent definition with the right vocabulary, deliverables, and guardrails. No coordination overhead.

**Level 1 — Known Pattern.** The goal matches a template in the library (e.g., SaaS product, marketing campaign, security audit). Forge loads the template, adapts roles to your specific goal, and creates the full agent team with artifact handoff chains.

**Level 2 — Novel Domain.** No template exists. Forge decomposes the goal into workstreams, proposes a team topology (pipeline, parallel, coordinator, or hierarchical), defines roles with precise vocabulary, and iterates with you until the blueprint is right.

At every level, the same principles apply: real-world role titles, domain-specific vocabulary, structured artifacts between agents, and a hard cap of 3-5 agents per team.

## Research Foundation

Every design decision in Forge traces back to published research. The `research/` directory contains synthesized findings from DeepMind's multi-agent scaling laws, PRISM persona science, and context engineering best practices.

For the full methodology, see [METHODOLOGY.md](METHODOLOGY.md).

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on adding agents, templates, skills, and research.

## License

[MIT](LICENSE)
