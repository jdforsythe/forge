# Forge Quick Start Guide

Get from zero to a working agent team in under five minutes.

---

## Installation

### Claude Code

Install Forge as a marketplace — this enables the team `settings.json` workflow and is the recommended path:

```
/plugin marketplace add jdforsythe/forge
/plugin install forge@forge
```

Or install directly as a single command:

```
/plugin add jdforsythe/forge
```

Both methods make all four core skills available in every project.

---

## Walkthrough 1: Build a SaaS Analytics Product

### What you type

> I want to build a SaaS analytics dashboard

### What happens

1. **Mission Planner activates.** It reads your project context (CLAUDE.md, existing files) and checks the library index (`library/index.json`).

2. **Library match found.** The `saas-product-team` template matches your goal. It covers four curated roles: Product Manager, Software Architect, Lead Engineer, and QA Engineer.

3. **Blueprint presented.** Mission Planner adapts the template to your specific goal and shows you a team blueprint:

```yaml
goal: "Build a SaaS analytics dashboard"
domain: software
complexity: team
topology: sequential-pipeline
agent_count: 4
estimated_cost_tier: high
```

**Roles:**
| Role | Key Deliverable |
|---|---|
| Product Manager | PRD with analytics-specific user personas and acceptance criteria |
| Software Architect | System architecture, API contracts, data pipeline design |
| Lead Engineer | Working application with tests, CI/CD pipeline |
| QA Engineer | Test plan, regression suite, performance benchmarks |

**Artifact chain:**
PRD --> Architecture Doc --> Implementation --> Test Report

**Quality gates:** User sign-off required after PRD and after Architecture Doc before engineering begins.

4. **You approve (or modify).** You can adjust roles, swap the topology, or remove a role entirely before anything is created.

5. **Agent Creator produces definitions.** Four agent markdown files are written to your project:

```
.claude/agents/product-manager.md
.claude/agents/software-architect.md
.claude/agents/lead-engineer.md
.claude/agents/qa-engineer.md
```

Each file contains a full agent definition: role identity, domain vocabulary, SOP, anti-patterns, deliverables, and decision authority.

---

## Walkthrough 2: Plan a Marketing Campaign

### What you type

> Help me plan a product launch campaign

### What happens

1. **Mission Planner classifies the goal** as marketing domain, campaign archetype. It finds the `marketing-campaign` template in the library.

2. **Blueprint presented** with four adapted roles:

```yaml
goal: "Product launch campaign"
domain: marketing
complexity: team
topology: sequential-pipeline
agent_count: 4
estimated_cost_tier: high
```

**Roles:**
| Role | Key Deliverable |
|---|---|
| Campaign Strategist | Strategy brief with audience segments, channel plan, timeline |
| Content Creator | Blog posts, email sequences, social copy, SEO content |
| Designer | Visual assets, ad creatives, landing page mockups |
| Analytics Lead | KPI framework, attribution model, performance report |

3. **Artifact chain made explicit:**

```
Strategy Brief --> Content Drafts + Creative Briefs --> Final Assets --> Performance Report
```

The strategist's brief feeds both the Content Creator and Designer in parallel. The Analytics Lead defines KPIs upfront and produces the final performance report after launch.

4. **You approve and agents are created** in `.claude/agents/`, each tailored to your specific product launch rather than generic marketing.

---

## Walkthrough 3: Security Review

### What you type

> I need to review our app's security

### What happens

Mission Planner evaluates the scope before deciding on team size. This is where complexity assessment matters.

**If the scope is narrow** (e.g., "check our auth flow for vulnerabilities"):

- Mission Planner determines this is **Level 0 -- single agent sufficient**.
- Produces one **Lead Auditor** agent definition with an SOP focused on your specific area.
- No team overhead. Fast, focused, cheap.

```yaml
goal: "Review authentication flow security"
domain: security
complexity: single-agent
topology: n/a
agent_count: 1
estimated_cost_tier: low
```

**If the scope is comprehensive** (e.g., "full security audit before our SOC 2 certification"):

- Mission Planner determines this is **Level 1 -- team warranted**.
- Loads the `security-audit` template with three roles.
- Presents a team blueprint:

```yaml
goal: "Comprehensive security audit for SOC 2 readiness"
domain: security
complexity: team
topology: sequential-pipeline
agent_count: 3
estimated_cost_tier: high
```

**Roles:**
| Role | Key Deliverable |
|---|---|
| Lead Auditor | Threat model (STRIDE), risk assessment, remediation plan |
| Penetration Tester | Vulnerability report with exploitability ratings |
| Compliance Analyst | Compliance gap analysis (SOC 2, GDPR, etc.) |

**Artifact chain:**
Threat Model --> Vulnerability Report --> Compliance Gap Analysis --> Final Remediation Plan

The Lead Auditor's threat model guides where the Penetration Tester focuses. The Compliance Analyst maps findings against regulatory frameworks. The Lead Auditor consolidates everything into a prioritized remediation plan.

---

## What to Expect

### Approval steps

Mission Planner always shows the plan before creating agents. You will see the full blueprint -- roles, artifact chain, quality gates, topology rationale -- and can modify any of it before a single agent file is written. Nothing is created without your explicit approval.

### Token usage

Typical costs by operation:

| Operation | Estimated tokens |
|---|---|
| Single agent definition (Level 0) | ~5K tokens |
| Team blueprint generation | ~10-20K tokens |
| Full team creation (blueprint + all agent definitions) | ~30-50K tokens |

These are estimates. Larger teams and more complex domains trend toward the upper end. The Mission Planner's complexity assessment is specifically designed to avoid unnecessary multi-agent setups that waste tokens.

### Iterative workflow

The process is interactive, not fire-and-forget:

- **Before blueprint approval:** Ask Mission Planner to add a role, remove one, change the topology, or adjust scope. It will regenerate the blueprint.
- **After agent creation:** Each agent definition can be edited directly (they are just markdown files). Tweak SOPs, add domain-specific anti-patterns, or adjust deliverables.
- **Between runs:** Agent definitions persist in `.claude/agents/`. Reuse them across sessions, share them with your team, or version them in git.

### When in doubt

If you are not sure whether your goal needs a team or a single agent, just describe what you want. Mission Planner applies evidence-based scaling criteria (the DeepMind decomposability framework) to make that call. It will choose single-agent when that is the right answer -- it is not biased toward creating teams.

---

## Library contents

Forge ships with curated agents and templates ready to use:

**Agents** (11 curated):
- Software: Product Manager, Software Architect, Lead Engineer, QA Engineer
- Marketing: Campaign Strategist, Content Creator, Designer, Analytics Lead
- Security: Lead Auditor, Penetration Tester, Compliance Analyst

**Templates** (3 curated):
- `saas-product-team` -- 4-role software product team
- `marketing-campaign` -- 4-role campaign team
- `security-audit` -- 3-role audit team

All items in the library are loaded and adapted by Mission Planner automatically. You do not need to reference them directly.
