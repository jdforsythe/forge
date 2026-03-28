---
goal: "Build a B2B SaaS product"
domain: software
complexity: team
topology: sequential-pipeline
agent_count: 4
estimated_cost_tier: high
---

## Roles
1. **Product Manager** — Defines requirements, user personas, and success metrics for the B2B SaaS product
2. **Software Architect** — Designs the system architecture including service boundaries, API contracts, and data model
3. **Lead Engineer** — Implements the product based on the architecture, manages technical execution and deployment
4. **QA Engineer** — Validates the product against requirements through systematic testing at every level of the test pyramid

## Artifact Chain
1. Product Manager produces **Product Requirements Document** (markdown: user personas, feature specs, acceptance criteria, priority matrix, success metrics)
2. Software Architect receives PRD, produces **Architecture Document** (markdown + Mermaid: component diagram, API contracts, data model, ADRs, deployment topology)
3. Lead Engineer receives Architecture Document, produces **Task Specs + Code** (task breakdown, working code with tests, deployment configuration, technical debt log)
4. QA Engineer receives Implementation + PRD, produces **Test Results Report** (markdown: test plan, test coverage, defect list, acceptance criteria verification, regression status)

## Quality Gates
- **PRD** must be reviewed by the user before architecture begins
- **Architecture Document** must be reviewed by the user before implementation begins
- **All code** must be reviewed before merge — Lead Engineer self-reviews against architecture, then QA verifies against PRD
- **Test Results Report** must show all critical acceptance criteria passing before delivery

## Topology
**Selected:** Sequential pipeline
**Rationale:** B2B SaaS product development has strong sequential dependencies — architecture depends on requirements, implementation depends on architecture, testing depends on implementation. Each phase produces a concrete artifact consumed by the next phase. Parallelizing would cause costly rework when upstream assumptions prove wrong.
**Alternatives considered:** Parallel-independent rejected because each phase depends on the previous output. Centralized-coordinator unnecessary because the pipeline is linear with clear handoffs. Hierarchical rejected because there is no delegation pattern — each role owns a distinct phase.

## Anti-Patterns to Guard Against
- **Requirements Drift:** Product Manager changing requirements after architecture is finalized. Prevention: Quality gate requires user sign-off on PRD before proceeding. Changes after sign-off trigger impact assessment across all downstream artifacts.
- **Gold Plating:** Architect over-designing for hypothetical scale or future features not in the PRD. Prevention: Every architectural component must trace back to a stated requirement or quality attribute. If it cannot be justified against the PRD, remove it.
- **Integration Blindness:** Engineer building components that work in isolation but fail to connect. Prevention: Architecture document includes explicit API contracts, data flow diagrams, and integration test specifications. Lead Engineer validates integration points early.
- **Happy Path Testing:** QA only testing the expected user flow and ignoring edge cases. Prevention: Test plan must include error states, boundary values, load scenarios, and accessibility checks. Risk-based testing prioritizes areas with highest failure impact.
