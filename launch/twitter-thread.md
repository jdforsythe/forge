# Forge Launch Thread

**1/**
DeepMind found that adding AI agents to a task can make it 39-70% worse.

Not a typo. On sequential tasks, multi-agent systems spend more tokens coordinating than they contribute in useful work.

We built an open-source tool based on what actually works.

**2/**
The single highest-leverage intervention in AI agent quality: vocabulary.

"Circuit breaker pattern (Nygard)" activates resilience engineering knowledge.

"Handle errors gracefully" activates blog-post-level try/catch advice.

One precise term replaces paragraphs of prompting.

**3/**
"You are the world's best programmer" performs WORSE than "You are a software engineer."

PRISM persona research found flattery activates generic aspirational content, not expertise. Brief identities under 50 tokens beat long elaborate personas every time.

**4/**
Team size saturates at 4 agents.

3 agents: 3.5x token cost, 2.3x output.
5 agents: 7x token cost, 3.1x output.
7+ agents: performance degrades.

If one well-prompted agent hits 45% of optimal, adding more agents gives diminishing returns.

**5/**
MetaGPT showed that agent teams passing structured artifacts (PRDs, ADRs, test reports) reduce error propagation by ~40% vs. free-form chat.

Agents should exchange typed deliverables, not have conversations. Schemas enforce clarity. Dialogue invites misinterpretation.

**6/**
So we built Forge: an open-source system that assembles AI agent teams from research, not vibes.

Give it a goal. It decides if you need one agent or a team, picks the topology, and produces structured definitions with expert vocabulary and anti-pattern guardrails.

**7/**
What's included:
- 4 meta-skills (planning, agent creation, skill creation, library management)
- 11 domain agents (software, marketing, security)
- 3 team templates with artifact handoff chains
- 8 research docs with full citations
- Works with Claude Code and Cowork

**8/**
Every design decision traces to published research: DeepMind scaling laws, PRISM persona science, MetaGPT, MAST failure taxonomy, context engineering literature.

The research/ directory is worth reading on its own even if you never use the tool.

MIT licensed. Contributions welcome.

**9/**
If you're building with AI agents, stop guessing. The research exists. The patterns are clear. More agents is not better. Precise vocabulary beats elaborate prompts. Real job titles beat invented personas.

**10/**
Forge is live and open source.

GitHub: [GitHub repo link]
Full writeup: [Full article link]
