---
goal: "Conduct a comprehensive security audit"
domain: security
complexity: team
topology: parallel-independent
agent_count: 3
estimated_cost_tier: high
---

## Roles
1. **Lead Auditor** — Coordinates the security audit, defines scope and threat model, synthesizes findings from all streams, and produces the final remediation roadmap
2. **Penetration Tester** — Identifies vulnerabilities through systematic testing of systems, applications, and infrastructure, delivering proof-of-concept exploits and CVSS-scored findings
3. **Compliance Analyst** — Evaluates compliance posture against applicable security frameworks and regulatory requirements, producing gap analysis and control mapping

## Artifact Chain
1. Lead Auditor produces **Audit Scope Document** (markdown: target systems, boundaries, rules of engagement, timeline, risk appetite, stakeholder sign-off)
2. Lead Auditor produces **Threat Model** (markdown + diagrams: STRIDE analysis, attack surface map, trust boundaries, threat prioritization)
3. Penetration Tester receives Audit Scope Document + Threat Model, produces **Vulnerability Assessment Report** (markdown: CVSS-scored findings, proof of concept, reproduction steps, affected assets, attack surface map)
4. Compliance Analyst receives Audit Scope Document + Threat Model, produces **Compliance Gap Analysis** (markdown: control mapping matrix, evidence package references, framework coverage, gap severity ratings)
5. Lead Auditor receives Vulnerability Assessment Report + Compliance Gap Analysis, produces **Consolidated Findings Report** (markdown: executive summary, unified risk matrix, cross-referenced technical and compliance findings, business impact assessment)
6. Lead Auditor produces **Remediation Roadmap** (markdown: prioritized remediation actions, ownership assignments, timelines, compensating controls, residual risk acceptance criteria)

## Quality Gates
- **Audit Scope Document** must be approved by the user and relevant stakeholders before any testing begins
- **Threat Model** must be reviewed by the user before Penetration Tester and Compliance Analyst begin parallel work
- **Vulnerability Assessment Report** must have all findings verified with proof of concept before inclusion in consolidated report
- **Compliance Gap Analysis** must reference specific evidence for each control assessment before inclusion in consolidated report
- **Remediation Roadmap** must be reviewed by Lead Auditor for feasibility, priority accuracy, and completeness before delivery

## Topology
**Selected:** Parallel-independent with coordinator synthesis
**Rationale:** Penetration testing and compliance analysis are largely independent activities that can execute simultaneously once scope and threat model are established. The Lead Auditor acts as coordinator, producing the initial scope artifacts that enable parallel work, then synthesizing the independent findings into a unified report and remediation plan. This maximizes throughput while ensuring coherent final output.
**Alternatives considered:** Sequential pipeline rejected because penetration testing and compliance analysis do not depend on each other — forcing sequential execution wastes time without improving quality. Centralized-coordinator considered but the Lead Auditor already fills this role within the parallel-independent topology. Hierarchical rejected because there are only two parallel workers and no sub-delegation needed.

## Anti-Patterns to Guard Against
- **Scope Creep:** Expanding audit boundaries mid-engagement without formal scope change approval. Prevention: Audit Scope Document includes explicit boundaries and a change control process. Any scope expansion requires user sign-off and timeline reassessment.
- **Checkbox Compliance:** Treating compliance as a form-filling exercise without testing whether controls actually work. Prevention: Compliance Analyst must test control effectiveness, not just verify documentation exists. Gap analysis must include evidence of operational testing.
- **Vulnerability Without Remediation:** Reporting security findings without actionable remediation guidance. Prevention: Every finding in the Consolidated Findings Report must have a corresponding entry in the Remediation Roadmap with specific, implementable actions.
- **Production Testing Without Authorization:** Penetration Tester executing exploits against production systems without explicit authorization. Prevention: Audit Scope Document includes rules of engagement specifying authorized testing targets, methods, and time windows. Penetration Tester must verify authorization before each test phase.
