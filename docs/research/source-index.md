# Source Index

> Complete bibliography for the Forge research reference files.

---

## Primary Sources

| # | Source | Authors/Origin | Year | Key Contribution | Used In |
|---|---|---|---|---|---|
| 1 | PRISM persona research | Various (meta-analysis) | 2024 | Persona length effects, alignment-accuracy tradeoff, role-task alignment rule | persona-science.md |
| 2 | DeepMind multi-agent scaling | DeepMind | 2025 | Three scaling principles, 45% threshold, team size saturation at 4, topology selection | scaling-laws.md |
| 3 | MAST framework | Multi-agent testing research | 2024-2025 | 14-failure-mode taxonomy (communication, coordination, quality) | failure-taxonomy.md |
| 4 | MetaGPT | Hong et al. | 2023 | Structured artifact handoffs > free dialogue, ~40% error reduction | team-design.md |
| 5 | Captain Agent | Adaptive composition research | 2024 | Dynamic team selection > static assignment, 15-25% improvement | team-design.md |
| 6 | Context engineering research | Various (prompt engineering literature) | 2023-2025 | U-curve, attention budget, progressive disclosure, position effects | context-engineering.md |
| 7 | Vocabulary routing research | Various (embedding/prompt studies) | 2023-2025 | Term-to-cluster activation, attribution amplification | vocabulary-routing.md |

## Domain Reference Works

| # | Source | Authors | Year | Key Contribution |
|---|---|---|---|---|
| 8 | Building Evolutionary Architectures | Ford, Parsons | 2017 | Fitness functions for architecture quality |
| 9 | Domain-Driven Design | Evans | 2003 | Bounded contexts, ubiquitous language, context mapping |
| 10 | Release It! | Nygard | 2007/2018 | Circuit breaker, bulkhead, stability patterns |
| 11 | Cynefin framework | Snowden | 2007 | Complexity-aware decision making |
| 12 | Observability Engineering | Majors et al. | 2022 | Modern observability, SLI/SLO, distributed tracing |
| 13 | OWASP Top 10 | OWASP Foundation | Updated annually | Web application security risks |
| 14 | Threat Modeling | Shostack | 2014 | STRIDE methodology |
| 15 | Hexagonal Architecture | Cockburn | 2005 | Ports and adapters pattern |
| 16 | User Story Mapping | Patton | 2014 | Story mapping technique |
| 17 | INVEST Criteria | Bill Wake | 2003 | User story quality criteria |

## Key Thresholds and Numbers

| Finding | Value | Source (#) |
|---|---|---|
| Optimal persona length | <50 tokens | PRISM (1) |
| Persona accuracy degradation | >100 tokens | PRISM (1) |
| Optimal context utilization | 15-40% of window | Context engineering (6) |
| Optimal team size | 3-5 agents | DeepMind (2) |
| Team saturation point | 4 agents | DeepMind (2) |
| Single-agent sufficiency threshold | 45% of optimal | DeepMind (2) |
| Performance degradation | 7+ agents | DeepMind (2) |
| Cost multiplier at 3 agents | 3.5x | DeepMind (2) |
| Cost multiplier at 5 agents | 7.0x | DeepMind (2) |
| Efficiency ratio at 4 agents | 0.56 | DeepMind (2) |
| Error reduction (structured handoffs) | ~40% | MetaGPT (4) |
| Adaptive vs static team improvement | 15-25% | Captain Agent (5) |
| Vocabulary payload size | 15-30 terms | Vocabulary routing (7) |
| Vocabulary clusters per agent | 3-5 | Vocabulary routing (7) |
| Max review iterations | 3 rounds | MAST (3) |
| Anti-patterns per agent | 5-10 | Forge design standard |
| Always-loaded context budget | 200-500 tokens | Context engineering (6) |
| Most common failure mode | FM-3.1 Rubber-Stamp Approval | MAST (3) |

## Cross-Reference: Research File to Sources

| Research File | Primary Sources Used |
|---|---|
| context-engineering.md | Context engineering research (6) |
| vocabulary-routing.md | Vocabulary routing research (7), Domain references (8-17) |
| persona-science.md | PRISM (1), Vocabulary routing (7) |
| scaling-laws.md | DeepMind scaling (2) |
| failure-taxonomy.md | MAST framework (3) |
| team-design.md | MetaGPT (4), Captain Agent (5), DeepMind (2), MAST (3) |
| master-synthesis.md | All sources (1-17) |

---

*This index covers all sources referenced across the Forge research reference files.*
