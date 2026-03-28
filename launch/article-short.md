# Adding AI Agents to a Task Can Make It 39-70% Worse

DeepMind's 2025 multi-agent scaling study found that on sequential reasoning tasks, multi-agent systems degrade by 39-70% compared to a single agent. The agents spend more tokens coordinating than they contribute in useful work. Performance saturates at 4 agents. A 5-agent team costs 7x the tokens for only 3.1x the output. And if a single well-prompted agent already hits 45% of optimal performance, adding more agents yields diminishing returns.

This finding -- and several others like it -- led us to build **Forge**, an open-source system that assembles AI agent teams based on actual research instead of vibes. It takes a goal, decides whether you need one agent or a team, picks the right coordination topology, and produces structured agent definitions. Works with Claude Code and Cowork. MIT licensed.

We dug through the literature on multi-agent systems, persona assignment, context engineering, and prompt design. Three findings shaped the architecture:

**Vocabulary is the steering wheel.** One precise domain term activates more expert knowledge in an LLM than paragraphs of generic instruction. "Circuit breaker pattern (Nygard)" routes to resilience engineering. "Handle errors gracefully" routes to blog-post-level advice about try/catch. Forge gives every agent 15-30 precise terms organized into expert-discourse clusters. This is the single highest-leverage intervention we found. (Ranjan et al., 2024)

**Flattery makes your agents dumber.** PRISM persona research showed that "You are the world's best programmer" performs worse than "You are a software engineer." Superlatives activate generic aspirational content instead of domain expertise. Brief role identities under 50 tokens outperform long elaborate personas. Real job titles that exist in real organizations activate denser knowledge clusters than invented roles.

**More agents is usually worse.** Teams of 3-5 with structured artifact handoffs (not free-form chat) outperform larger groups. MetaGPT showed structured artifacts reduce error propagation by roughly 40% compared to open dialogue. Forge enforces a cascade: start with one agent, escalate only when the current level demonstrably fails.

## What's included

- 4 core skills: Mission Planner, Agent Creator, Skill Creator, Librarian
- 3 infrastructure agents: Verifier, Researcher, Reviewer
- 11 domain agents across software, marketing, and security
- 3 team templates with artifact handoff chains
- 8 research documents with full citations
- Schemas for agent definitions and team blueprints

The research directory contains synthesized findings from DeepMind's scaling laws, PRISM persona science, MetaGPT, MAST failure taxonomy, and context engineering literature. Every design decision traces to a citation.

GitHub: [jdforsythe/forge](https://github.com/jdforsythe/forge)

Full writeup: [Full article link]
