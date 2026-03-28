# Forge
An open-source system for science-backed AI team assembly.

## Tech Stack
- Pure markdown skills and agent definitions (no framework dependency)
- Python scripts for packaging (package_skill.py)
- JSON for library index and usage tracking

## Conventions
- All skill/agent files use YAML frontmatter
- Agent definitions follow the 7-component structure (see schemas/agent-definition.md)
- Team blueprints follow the blueprint format (see schemas/team-blueprint.md)
- SKILL.md files must be <500 lines; overflow goes in references/
- Reference files must be <300 lines with TOC if >150 lines
- File/folder names: kebab-case
- All research claims must cite sources

## Key Principles
- Start simple, add complexity only when demonstrated need requires it
- Vocabulary routing is the primary quality lever
- Real-world roles activate real expertise
- 3-5 agents maximum per team
- Structured artifacts for handoffs, not free-form dialogue
