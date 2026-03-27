# agent-design

A meta-skill library for building high-quality Claude Code skills and agents, grounded in transformer research and Anthropic's prompt engineering documentation.

## What's here

| Skill | Purpose |
|---|---|
| `create-skill` | Creates a production-quality Claude Code skill from a domain description |

## Why this approach

Generic skills produce generic output. This library applies research-backed techniques — expert vocabulary payloads that route attention into the right knowledge clusters, named anti-pattern watchlists that push output away from the statistical average, and structured instructions that respect the transformer's U-shaped attention curve — so that skills built here consistently hit an expert-level bar.

The practices are derived from peer-reviewed research (Liu et al. 2024 on positional bias, Wei et al. 2022 on chain-of-thought, Vaswani et al. 2017) and Anthropic's own engineering documentation. The full synthesis is in `docs/skill-design-research-synthesis.md`.

## Skills

### `create-skill`

Creates a complete, deployable Claude Code skill from scratch. Given a domain description, it:

- Assembles an expert vocabulary payload for the target domain
- Writes behavioral instructions with a named anti-pattern watchlist
- Produces BAD/GOOD example pairs as few-shot demonstrations
- Generates a dual-register description (formal + colloquial) for reliable triggering
- Outputs a ready-to-deploy directory with `SKILL.md` and reference files

**Invoke:** `/create-skill` or describe what you want Claude to be better at

**Output:** A `.claude/skills/<name>/` directory with `SKILL.md` and a `references/` subdirectory for extended material

The skill itself is the canonical example of what it produces — read `.claude/skills/create-skill/SKILL.md` to see the pattern applied to skill creation.

## Roadmap

- `create-agent` — Skill for designing high-quality Claude Code agents with tool selection, memory architecture, loop design, and evaluation criteria

## Project structure

```
agent-design/
├── .claude/
│   └── skills/
│       └── create-skill/
│           ├── SKILL.md                    # Core meta-skill definition
│           └── references/
│               ├── skill-architecture-spec.md    # File structure, section ordering, line budgets
│               ├── anti-pattern-catalog.md       # Named failure modes with DNERP resolution
│               ├── description-authoring.md      # Triggering surface design rules
│               ├── vocabulary-engineering.md     # Domain terminology methodology
│               ├── example-library.md            # BAD/GOOD pair templates
│               ├── science-foundations.md        # Deep research explanations
│               └── evaluation-checklist.md       # Pre-deployment verification checklist
└── docs/
    └── skill-design-research-synthesis.md   # 42KB research synthesis with citations
```

## Research foundation

`docs/skill-design-research-synthesis.md` covers:

- How transformers allocate attention and why position matters
- Vocabulary as routing signals in embedding space
- Why negative constraints produce better output than positive-only instructions
- Few-shot examples vs verbose instructions (when each works)
- Structural clarity: XML tags vs Markdown
- Progressive disclosure architecture for large skill payloads
- A 40-item pre-deployment evaluation checklist

Primary sources include Liu et al. (2024), Wei et al. (2022), Vaswani et al. (2017), and nine Anthropic engineering documents.
