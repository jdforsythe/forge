# Frequently Asked Questions

## Is this a framework?

No. Forge is not a framework, library, or runtime. It is a collection of pure markdown skills and agent definitions. There is no code to import, no dependencies to install, and no process to run. Claude Code reads the markdown files and uses them as context to guide its behavior.

## Do I need Python?

Only if you want to use `package_skill.py` for packaging skills into installable bundles. Everything else in Forge is plain markdown that requires no programming language or runtime. You can create, edit, and use skills and agent definitions with nothing more than a text editor.

## How is this different from other agent frameworks?

Most agent frameworks run agents themselves — they provide orchestration, tool calling, memory, and execution loops. Forge does none of that. Instead, Forge creates high-quality agent definitions and skills that Claude Code executes. The distinction matters:

- **Other frameworks**: Framework runs agents, manages state, handles tool calls
- **Forge**: Produces markdown definitions, Claude Code handles execution

This means Forge has zero runtime overhead, no vendor lock-in on orchestration, and works with whatever execution environment reads the definitions.

## Can I use this with other LLMs?

The principles behind Forge — structured agent definitions, clear role boundaries, artifact chains — apply broadly to any LLM-based workflow. However, the skill format (SKILL.md with specific frontmatter) is designed for Claude Code. Other LLMs may not interpret the instructions the same way.

If you want to adapt Forge patterns for other systems, the methodology in `METHODOLOGY.md` is a good starting point.

## How many agents should I use?

Start with one. A single well-defined agent handles most tasks better than a poorly coordinated team. Only add more agents when you hit a clear limitation:

- The task requires genuinely different expertise (e.g., coding vs. visual design)
- A single agent's context window cannot hold all the relevant information
- Sequential handoff improves quality (e.g., write then review)

For most projects, 2-3 agents is the sweet spot. The maximum recommended is 5. Beyond that, coordination overhead outweighs the benefits.

## What if my domain is not in the library?

The library covers common patterns, but Forge handles novel domains through two paths:

1. **Mission Planner (Level 2)**: Analyzes your project requirements and designs a custom team structure, even for domains not in the library
2. **Agent Creator**: Builds agent definitions for any role you describe, from scratch

You do not need a library match to use Forge effectively.

## Can agents share context or pass work between each other?

Yes. Agent definitions specify deliverables — concrete artifacts like documents, code, or plans. These artifacts are the interface between agents. When one agent finishes, its deliverables become input for the next. This is called the artifact chain, and it is defined in team templates.

## What is a "skill" vs. an "agent definition"?

- A **skill** is a SKILL.md file that gives Claude Code a specific capability (e.g., how to create agents, how to manage a library)
- An **agent definition** is a markdown file that describes a role — its responsibilities, constraints, deliverables, and working style

Skills teach Claude Code *how* to do something. Agent definitions tell it *what role to play*.

## Can I contribute?

Yes. See [CONTRIBUTING.md](../CONTRIBUTING.md) for guidelines on submitting new agent definitions, skills, templates, and improvements. We value quality over quantity — one well-crafted contribution is worth more than ten hasty ones.

## Where do I start?

See the [Quick Start guide](quick-start.md). The shortest path: install the Mission Planner skill and describe your project. It will figure out the rest.
