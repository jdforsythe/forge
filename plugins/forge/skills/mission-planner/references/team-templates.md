# Team Templates Index

> Pre-built team configurations for common project archetypes. The Mission Planner loads and adapts these rather than designing from scratch.

---

## How to Use Templates

1. Match the user's goal to a template by domain and archetype.
2. Load the template as a starting point.
3. Adapt to the specific goal: rename roles, adjust deliverables, modify quality gates.
4. Never use a template blindly — every adaptation should reflect the user's stated constraints and context.

Templates are starting points, not prescriptions. If the user's goal does not fit any template, design from scratch using the topology guide and scaling laws.

---

## Template 1: SaaS Product Team

### Overview

| Field | Value |
|---|---|
| Domain | Software |
| Archetype | Product build (web application, SaaS, platform) |
| Topology | Sequential pipeline |
| Agent count | 4 |
| Estimated cost tier | High |

### Roles

1. **Product Manager** — Defines requirements, user personas, acceptance criteria, and priority matrix for the product.
2. **Software Architect** — Designs system architecture, API contracts, data model, and makes technology selection decisions.
3. **Lead Engineer** — Implements the application based on architecture, writes tests, and produces deployment configuration.
4. **QA Engineer** — Validates implementation against requirements, tests edge cases, and verifies acceptance criteria.

### Artifact Chain

```
Product Manager → PRD (requirements, personas, acceptance criteria)
    ↓
Software Architect → Architecture Document (component diagram, API contracts, data model, ADRs)
    ↓
Lead Engineer → Implementation (application code, tests, deployment config)
    ↓
QA Engineer → Test Results Report (coverage, defects, acceptance verification)
```

### Quality Gates

- PRD must be reviewed by user before architecture begins
- Architecture Document must be reviewed by user before implementation begins
- Implementation must pass automated tests before QA review
- Test Results Report must show all critical acceptance criteria passing

### When to Use

- Building a web application, SaaS product, or platform
- Clear product requirements exist or can be defined
- Standard software development lifecycle applies
- Team needs to go from idea to working software

### When NOT to Use

- Simple scripts, CLIs, or single-file utilities (use single agent)
- Prototyping or rapid experimentation (too much process overhead)
- Infrastructure-only work with no application layer
- The "product" is really just a feature addition to existing code

### Typical Modifications

- **Drop QA Engineer** for MVPs or prototypes — Lead Engineer handles testing inline. Reduces to 3 agents.
- **Add DevOps Engineer** if deployment is complex (Kubernetes, multi-region, CI/CD pipeline). Increases to 5 agents — justify the cost.
- **Merge PM and Architect** for small products where one person would own both in a real company. Reduces to 3 agents.
- **Split Lead Engineer** into Frontend and Backend only for large applications with genuinely different tech stacks. Increases to 5 agents — justify explicitly.

---

## Template 2: Marketing Campaign

### Overview

| Field | Value |
|---|---|
| Domain | Marketing |
| Archetype | Campaign (product launch, content campaign, brand campaign) |
| Topology | Parallel-independent with centralized strategy |
| Agent count | 4 |
| Estimated cost tier | Medium |

### Roles

1. **Campaign Strategist** — Defines campaign goals, target audience, messaging framework, and channel strategy. Also serves as synthesis agent.
2. **Content Writer** — Produces long-form content: blog posts, landing page copy, email sequences.
3. **Social Media Specialist** — Creates platform-specific social content, hashtag strategy, posting schedules.
4. **Performance Analyst** — Defines KPIs, measurement framework, tracking setup, and reporting templates.

### Artifact Chain

```
Campaign Strategist → Campaign Brief (goals, audience, messaging, channels)
    ↓ (distributed to all)
Content Writer → Content Package (blog posts, landing pages, emails)
Social Media Specialist → Social Package (posts, schedules, hashtag strategy)  [parallel]
Performance Analyst → Measurement Plan (KPIs, tracking, reporting templates)   [parallel]
    ↓ (all flow back)
Campaign Strategist → Integrated Campaign Plan (all assets unified, timeline, budget)
```

### Quality Gates

- Campaign Brief must be approved by user before content creation begins
- All content must align with messaging framework from Campaign Brief
- Integrated Campaign Plan must be reviewed by user before launch

### When to Use

- Launching a product, feature, or brand campaign
- Multiple content channels need coordinated messaging
- Campaign requires both creative content and measurement rigor

### When NOT to Use

- Single-channel content (just a blog post, just social posts) — use single agent
- Purely analytical work (market research, competitive analysis) — use single agent
- Brand identity or logo design — this template is for campaigns, not brand creation

### Typical Modifications

- **Drop Performance Analyst** for awareness campaigns where measurement is simple. Reduces to 3 agents.
- **Add Designer** for campaigns requiring visual assets (infographics, ad creatives). Increases to 5 agents.
- **Merge Content Writer and Social Media** for small campaigns where one writer handles all channels. Reduces to 3 agents.
- **Replace Social Media with PR Specialist** for campaigns focused on media coverage rather than social.

---

## Template 3: Security Audit

### Overview

| Field | Value |
|---|---|
| Domain | Security |
| Archetype | Audit (security assessment, compliance review, penetration test plan) |
| Topology | Parallel-independent with coordinator |
| Agent count | 3 |
| Estimated cost tier | Medium |

### Roles

1. **Lead Auditor** — Defines audit scope, methodology, risk framework. Synthesizes findings into final report. Serves as coordinator.
2. **Technical Reviewer** — Performs code review, architecture analysis, dependency audit, and configuration review.
3. **Compliance Analyst** — Maps findings to compliance frameworks (SOC 2, GDPR, HIPAA, etc.), identifies gaps, recommends remediation.

### Artifact Chain

```
Lead Auditor → Audit Plan (scope, methodology, risk criteria, timeline)
    ↓ (distributed)
Technical Reviewer → Technical Findings (vulnerabilities, code issues, config problems)  [parallel]
Compliance Analyst → Compliance Mapping (framework gaps, regulatory risks)               [parallel]
    ↓ (all flow back)
Lead Auditor → Audit Report (executive summary, findings, risk ratings, remediation plan)
```

### Quality Gates

- Audit Plan must be approved by user (defines scope — what is in and out)
- Technical findings must include severity ratings using consistent framework (CVSS or equivalent)
- Compliance mapping must reference specific framework controls
- Final Audit Report must be reviewed by user before distribution

### When to Use

- Assessing security posture of an application, system, or organization
- Compliance requirement (SOC 2, GDPR, HIPAA, PCI-DSS)
- Pre-launch security review
- Periodic security assessment

### When NOT to Use

- Fixing a specific known vulnerability (use single agent)
- Writing security policies from scratch without an existing system to audit
- Incident response (requires different workflow — time-critical, dynamic)
- Penetration testing execution (requires actual tool access, not just planning)

### Typical Modifications

- **Add Infrastructure Reviewer** for cloud-heavy environments (AWS/GCP/Azure configuration audit). Increases to 4 agents.
- **Drop Compliance Analyst** if no regulatory framework applies (internal tool, no customer data). Reduces to 2 agents — consider using Level 2 (worker + reviewer) instead.
- **Add Threat Modeler** for high-security applications (STRIDE analysis, attack tree generation). Increases to 4 agents.
- **Replace Compliance Analyst with Privacy Specialist** for GDPR-focused audits requiring data flow mapping.

---

## Template Selection Quick Reference

| User Goal | Template | Modifications to Consider |
|---|---|---|
| "Build a SaaS / web app / platform" | SaaS Product Team | Drop QA for MVPs |
| "Build an API / backend service" | SaaS Product Team | Drop PM if specs exist, merge with Architect |
| "Launch a product / marketing campaign" | Marketing Campaign | Drop Analyst for simple campaigns |
| "Content marketing strategy" | Marketing Campaign | Merge Content + Social for small scope |
| "Security review / audit" | Security Audit | Drop Compliance if no regulatory need |
| "Compliance assessment" | Security Audit | Emphasize Compliance Analyst role |
| "Build a mobile app" | SaaS Product Team | Consider adding Designer as 5th role |
| "Write a book / long content" | None — design from scratch | Sequential pipeline: Research → Outline → Write → Edit |
| "Data pipeline / ML project" | None — design from scratch | Sequential pipeline: Data Eng → ML Eng → Validator |
| "DevOps / infrastructure" | None — design from scratch | Often single agent (Level 0-1) |

---

## Creating New Templates

When the Mission Planner encounters a recurring project archetype not covered by existing templates:

1. Design the team using topology guide and scaling laws.
2. Validate with the user.
3. After successful use, propose saving as a new template.
4. Template must include: overview table, roles, artifact chain, quality gates, when/when-not, typical modifications.
5. Add to this file and update library/index.json.

---

*Reference for Mission Planner template loading. See also: topology-guide.md for selection criteria, scaling-laws.md for cost justification.*
