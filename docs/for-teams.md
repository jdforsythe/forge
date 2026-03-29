# Using Forge in a Team Setting

Forge works well for individual use, but it is designed to scale to teams. This guide covers how to share agent definitions, maintain consistency, and onboard new team members.

## Install Forge for the Team

Forge has two layers: the **plugin** (core skills, shipped via marketplace) and the **project** (agent definitions and customizations, committed to your repo). Both are required for a full team setup.

### The Plugin Layer

Each team member adds the Forge marketplace and installs the plugin once:

**Claude Code:**
```
/plugin marketplace add jdforsythe/forge
/plugin install forge@forge
```

### The Project Layer

For Claude Code teams, commit a `.claude/settings.json` to your repo. When team members trust the project folder, Claude Code prompts them to install the marketplace and plugin automatically — no manual step needed:

```json
{
  "extraKnownMarketplaces": {
    "forge": {
      "source": { "source": "github", "repo": "jdforsythe/forge" }
    }
  },
  "enabledPlugins": {
    "forge@forge": true
  }
}
```

Add project-specific agent definitions and skill overrides alongside it:

```
your-repo/
  .claude/
    settings.json  # Forge marketplace + plugin (auto-installs for team members)
    agents/        # Project-specific agent definitions
    skills/        # Skill overrides for this project
```

Project-level files take precedence over the plugin, so the team gets shared defaults plus project-specific customizations.

## Shared Agent Definitions

When agents are committed to the repo, the team benefits from consistency:

- Everyone uses the same reviewer agent with the same quality standards
- New hires work with the same agent definitions as senior engineers
- Agent improvements propagate through normal pull requests
- Code review applies to agent definitions just like it applies to code

Treat agent definitions as part of your codebase. Review changes to them with the same rigor you apply to source code.

## Growing the Library Together

As your team uses Forge, the library grows:

- **Librarian** tracks which agents and templates are used and how often
- Patterns that work well get promoted to team templates
- Agents that underperform get refined or retired
- Usage data in `usage-log.jsonl` shows what is actually valuable

Encourage team members to submit new agent definitions when they solve a novel problem. The library becomes a shared repository of team knowledge about how to structure AI-assisted work.

## Custom Templates for Your Team

Every team has recurring project types. Create templates for yours:

- **Sprint planning**: Planner + Implementer + Reviewer
- **Incident response**: Investigator + Fixer + Documenter
- **Feature development**: Architect + Builder + Tester

Templates encode your team's best practices for structuring work. Instead of each person deciding how to set up agents for a feature, the template provides a proven starting configuration.

Store custom templates in your project's `.claude/templates/` directory and commit them to the repo, or contribute them upstream to the Forge library.

## Onboarding New Team Members

New team members get up to speed in two steps:

1. **Install Forge once** — add the marketplace and install the plugin (`/plugin marketplace add jdforsythe/forge` + `/plugin install forge@forge`). If your repo has `.claude/settings.json` with `extraKnownMarketplaces`, Claude Code handles this automatically when they trust the project folder.
2. **Clone the repo** — project-specific agent definitions in `.claude/agents/` activate automatically. Pre-configured templates give them a working setup for common tasks.

Agent definitions encode team standards they would otherwise need to learn gradually. This reduces onboarding time from days to hours for AI-assisted workflows.

## Best Practices

- **Pin versions**: Project agent definitions are already pinned by git history — no action needed. For the Forge plugin itself, pin the version by adding a `sha` to the marketplace source in `.claude/settings.json` and upgrade together as a team by updating it in a PR
- **Document custom agents**: Add a comment at the top of each agent definition explaining why your team created it
- **Review agent changes**: Include agent definition changes in your normal code review process
- **Start small**: Begin with 1-2 shared agents and expand based on actual need
- **Measure outcomes**: Track whether shared agents improve consistency and quality across the team
- **Rotate ownership**: Let different team members own different agent definitions to spread knowledge

## What Not to Do

- Do not create a different agent for every team member's preferences — aim for shared standards
- Do not skip reviewing agent definition changes in PRs
- Do not add agents preemptively — only add them when there is a demonstrated need
- Do not commit personal experimental agents to the shared directory — use a separate branch or local directory for experimentation
