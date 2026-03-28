---
goal: "Plan and execute a marketing campaign"
domain: marketing
complexity: team
topology: parallel-independent
agent_count: 4
estimated_cost_tier: medium
---

## Roles
1. **Campaign Strategist** — Defines the campaign strategy, target audience, channel mix, messaging hierarchy, and success metrics that guide all downstream work
2. **Content Creator** — Produces campaign copy across all channels — blog posts, email sequences, social media posts, and landing page content aligned to the strategy brief
3. **Designer** — Creates visual assets for the campaign — social graphics, email templates, banner ads, and brand-compliant layouts per the creative brief
4. **Analytics Lead** — Defines measurement framework, tracks campaign performance, runs A/B test analysis, and delivers optimization recommendations

## Artifact Chain
1. Campaign Strategist produces **Strategy Brief** (markdown: target audience, channel plan, messaging hierarchy, budget allocation, success metrics, timeline)
2. Content Creator receives Strategy Brief, produces **Content Calendar + Channel-Specific Copy** (markdown + documents: editorial calendar, blog posts, email sequences, social copy, CTAs)
3. Designer receives Strategy Brief, produces **Visual Assets + Templates** (design files: social graphics, email templates, banner ads, brand-compliant layouts with specs)
4. Content Creator and Designer outputs converge into **Campaign Assets** (assembled content + visuals ready for launch)
5. Analytics Lead receives Strategy Brief + Campaign Assets, produces **Performance Report** (markdown: KPI dashboard, A/B test results, ROI analysis, optimization recommendations)

## Quality Gates
- **Strategy Brief** must be approved by the user before content or creative work begins — this is the single source of truth for campaign direction
- **All content** must be reviewed against brand voice guidelines and messaging hierarchy from the Strategy Brief before finalization
- **All visual assets** must pass brand guideline compliance and WCAG accessibility contrast checks before delivery
- **Performance metrics and tracking** must be defined and instrumented before campaign launch
- **Performance Report** must include actionable recommendations, not just metric summaries

## Topology
**Selected:** Parallel-independent with centralized strategy
**Rationale:** After the Strategy Brief is finalized, content creation and design are independent workstreams that can execute in parallel. The Campaign Strategist acts as the centralizing force — defining the brief that aligns all work, then the Analytics Lead closes the loop with measurement. This maximizes throughput during the production phase while maintaining strategic coherence.
**Alternatives considered:** Sequential pipeline rejected because content and design do not depend on each other — forcing them into sequence wastes time. Full centralized-coordinator rejected because the Strategist does not need to mediate every interaction — the Strategy Brief is sufficient alignment. Hierarchical rejected because there is no delegation pattern — each role owns a distinct competency.

## Anti-Patterns to Guard Against
- **Campaign Without Measurable Goals:** Launching without defined KPIs or success criteria. Prevention: Strategy Brief must include specific, quantified success metrics (not "increase awareness" but "achieve 5% CTR on email sequence"). Analytics Lead validates metric definitions before work begins.
- **Channel Proliferation Without Focus:** Spreading effort across too many channels instead of dominating a few. Prevention: Strategy Brief must justify each channel with audience data and expected ROI. Maximum of 3-4 primary channels per campaign.
- **Brand Inconsistency Across Channels:** Visual and verbal identity diverging between email, social, and web content. Prevention: All content and design reviewed against the Strategy Brief's messaging hierarchy and brand guidelines before finalization.
- **Vanity Metrics:** Reporting on impressions and likes instead of conversion and revenue impact. Prevention: Analytics Lead must tie every reported metric to a business outcome. Performance Report requires a "So What?" section linking data to decisions.
