# Source Index

> Complete bibliography for the Forge research reference files. **Citation policy:** every source must have a working URL or arXiv/DOI identifier; quantitative claims may only cite sources that state the number. Forge's own conventions are registered separately as design standards, never as findings. Evidence baseline: July 2026, targeting the Claude 4.5+/5 model generation. All entries verified against their sources in the July 2026 red-team audit.

---

## Tier 1 — Research Papers

| # | Source | Authors | Year | Key Contribution | URL |
|---|---|---|---|---|---|
| 1 | Towards a Science of Scaling Agent Systems | Kim et al. (Google Research / MIT / Google DeepMind) | 2025 | ~45% single-agent accuracy threshold; sequential tasks degrade −39% to −70% under every MAS variant; error amplification 17.2x vs 4.4x; overhead 58-515%; effective teams 3-4 under fixed budgets | arxiv.org/abs/2512.08296 |
| 2 | Why Do Multi-Agent LLM Systems Fail? (MAST) | Cemri et al. (UC Berkeley), NeurIPS 2025 | 2025 | Multi-Agent System Failure Taxonomy: 14 modes, 3 categories (specification 41.77%, misalignment 36.94%, verification 21.30%); taxonomy-guided fixes +15.6/+9.4 pts | arxiv.org/abs/2503.13657 |
| 3 | Expert Personas Improve LLM Alignment but Damage Accuracy (PRISM) | Hu, Rostami, Thomason (USC) | 2026 | Alignment-accuracy tradeoff; personas damage MMLU (71.6%→68.0%→66.3%); longer personas damage knowledge tasks more | arxiv.org/abs/2603.18507 |
| 4 | When "A Helpful Assistant" Is Not Really Helpful | Zheng et al., Findings of EMNLP 2024 | 2024 | 162 personas, 4 model families, 2,410 questions: personas do not improve factual accuracy | arxiv.org/abs/2311.10054 |
| 5 | Prompting Science Report 4: Playing Pretend | Wharton Generative AI Labs | 2025 | Expert personas don't improve factual accuracy on modern frontier models | ssrn.com/abstract=5879722 |
| 6 | When Does Persona Prompting Actually Help? | Xiao et al. | 2026 | Personas help advisory questions; increase depth, reduce clarity | arxiv.org/abs/2605.29420 |
| 7 | Prompt Engineering: How Prompt Vocabulary affects Domain Knowledge | Schreiter | 2025 | Optimal *mid-range* terminology specificity; maximal jargon does not win | arxiv.org/abs/2505.17037 |
| 8 | MetaGPT: Meta Programming for a Multi-Agent Collaborative Framework | Hong et al., ICLR 2024 oral | 2023 | Structured artifact handoffs beat dialogue-based ChatDev: executability 3.75 vs 2.25; revision cost 0.83 vs 2.5; +4.2/+5.4 Pass@1 from executable feedback | arxiv.org/abs/2308.00352 |
| 9 | Adaptive In-conversation Team Building (CaptainAgent) | Song et al. (AutoGen/AG2) | 2024 | Adaptive per-subtask team assembly: +21.94% average accuracy vs existing multi-agent baselines (not vs compute-matched single agents) | arxiv.org/abs/2405.19425 |
| 10 | Single-Agent LLMs Outperform MAS Under Equal Thinking Budgets | Tran & Kiela | 2026 | Compute confound: with equal thinking tokens, single agents match/beat 5 MAS architectures on multi-hop reasoning | arxiv.org/abs/2604.02460 |
| 11 | The Ringelmann Effect in Multi-Agent LLM Systems | Bertalanič & Fortuna | 2026 | Team-size scaling law with hard-ceiling/sublinear/linear regimes; homogeneous teams saturate; N≤5 pilot predicts ceiling | arxiv.org/abs/2606.02646 |
| 12 | Scaling LLM-based Multi-Agent Collaboration (MacNet) | Qian et al., ICLR 2025 | 2024 | Collaborative scaling law: logistic growth, early saturation; irregular topologies win | arxiv.org/abs/2406.07155 |
| 13 | Multi-Agent Design: Optimizing Agents with Better Prompts and Topologies (MASS) | Zhou et al. (Google), ICLR 2026 | 2025 | Prompt optimization dominates topology choice; influential topologies are a small fraction of the design space | arxiv.org/abs/2502.02533 |
| 14 | Lost in the Middle | Liu et al., TACL vol. 12 | 2024 | Mid-context accuracy drops >20 percentage points (GPT-3.5-era models) | aclanthology.org/2024.tacl-1.9 |
| 15 | Found in the Middle | Hsieh et al., Findings of ACL 2024 | 2024 | U-shaped attention bias; calibration improves RAG by up to 15 percentage points (needs attention-weight access) | aclanthology.org/2024.findings-acl.890 |
| 16 | On the Emergence of Position Bias in Transformers | Wu et al. (MIT), ICML 2025 | 2025 | Causal masking biases toward early positions; RoPE adds recency decay; their interplay yields the U-shape | arxiv.org/abs/2502.01951 |
| 17 | NoLiMa: Long-Context Evaluation Beyond Literal Matching | Modarressi et al., ICML 2025 | 2025 | Without literal matches, 11/12 models drop below 50% of short-context baseline by 32K tokens | arxiv.org/abs/2502.05167 |
| 18 | Chain-of-Thought Prompting Elicits Reasoning | Wei et al., NeurIPS 2022 | 2022 | CoT improves arithmetic/commonsense/symbolic reasoning (pre-reasoning-model era) | arxiv.org/abs/2201.11903 |
| 19 | Prompting Science Report 2: The Decreasing Value of Chain of Thought | Meincke, Mollick, Mollick, Shapiro (Wharton) | 2025 | CoT prompting: marginal gains for reasoning models at 20-80% time cost; can increase variability on non-reasoning models | arxiv.org/abs/2506.07142 |
| 20 | What Prompts Don't Say | Yang et al. (CMU) | 2025 | Requirements adherence drops as count grows (~95% at n=5 → 85.0% at n=19, gpt-4o); underspecified prompts 2x regression risk; both over- and under-specification fail | arxiv.org/abs/2505.13360 |
| 21 | How Many Instructions Can LLMs Follow at Once? (IFScale) | Jaroslawicz et al. | 2025 | ~68% adherence at 500 instructions for best models; universal primacy effect; errors shift to omission | arxiv.org/abs/2507.11538 |
| 22 | Why Johnny Can't Prompt | Zamfirescu-Pereira et al., CHI 2023 | 2023 | Non-experts favor "Do not X" phrasing; observed less effective than "Do Y" (n=10 qualitative study) | doi.org/10.1145/3544548.3581388 |
| 23 | Does Prompt Formatting Have Any Impact on LLM Performance? | He et al. (Microsoft) | 2024 | GPT-3.5-turbo varied up to 40% by format (code translation; XML not tested); GPT-4 far more robust; no universal best format | arxiv.org/abs/2411.10541 |
| 24 | The Few-shot Dilemma: Over-prompting LLMs | Tang et al. | 2025 | Excess examples degrade small (~≤8B) models; large models stay stable | arxiv.org/abs/2509.13196 |
| 25 | Calibrate Before Use | Zhao et al., ICML 2021 | 2021 | Recency bias: models overweight labels/examples near the end of the prompt | arxiv.org/abs/2102.09690 |
| 26 | Self-Refine: Iterative Refinement with Self-Feedback | Madaan et al. | 2023 | Refinement gains plateau by roughly iteration 3 (single-agent setting) | arxiv.org/abs/2303.17651 |
| 27 | DeepSeek-R1 | DeepSeek-AI | 2025 | Few-shot prompting consistently degrades reasoning-model performance; zero-shot recommended | arxiv.org/abs/2501.12948 |
| 28 | ExpertPrompting | Xu et al. | 2023 | Detailed expert personas improved LLM-judged quality (GPT-3.5 era); its ~150-token template is what PRISM later showed damages knowledge accuracy | arxiv.org/abs/2305.14688 |

## Tier 1 — Lab Engineering Docs (Anthropic, current generation)

| # | Source | Date | Key Contribution | URL |
|---|---|---|---|---|
| 29 | Building Effective Agents | 2024-12-19 | Simplicity first; workflow vs agent taxonomy; five workflow patterns | anthropic.com/engineering/building-effective-agents |
| 30 | Effective Context Engineering for AI Agents | 2025-09-29 | Attention budget; context rot; right-altitude prompts; "laundry list of edge cases... we do not recommend this"; diverse canonical examples (no count); "if a human engineer can't definitively say which tool should be used... an AI agent can't be expected to do better" | anthropic.com/engineering/effective-context-engineering-for-ai-agents |
| 31 | Writing Effective Tools for AI Agents — Using AI Agents | 2025-09-11 | Few consolidated tools; non-overlapping toolsets; eval-first tool development | anthropic.com/engineering/writing-tools-for-agents |
| 32 | Equipping Agents for the Real World with Agent Skills | 2025-10-16 | Canonical skill definition; three-level progressive disclosure; name+description as triggering surface | anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills |
| 33 | Skill Authoring Best Practices | 2025-10, maintained | SKILL.md <500 lines; description ≤1024 chars, third person; one-level-deep references; TOC >100 lines; degrees-of-freedom framework; eval-first | platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices |
| 34 | skill-creator (anthropics/skills) | 2025-10, living | Models "undertrigger" skills — make descriptions "pushy"; "explain why... in lieu of heavy-handed musty MUSTs"; ~100-word metadata level | github.com/anthropics/skills |
| 35 | Improving Frontend Design through Skills | 2025-11-12 | Distributional convergence ("AI slop"); ~400-token vocabulary skill with explicit never-use lists unlocks latent design capability | claude.com/blog/improving-frontend-design-through-skills |
| 36 | Effective Harnesses for Long-Running Agents | 2025-11-26 | Initializer + coding-agent pattern; progress files; feature lists; compaction insufficient alone | anthropic.com/engineering/effective-harnesses-for-long-running-agents |
| 37 | How We Built Our Multi-Agent Research System | 2025-06-13 | Orchestrator-worker +90.2% on breadth-first research at ~15x tokens; token spend explains ~80% of variance | anthropic.com/engineering/multi-agent-research-system |
| 38 | Harness Design for Long-Running Application Development | 2026-03-24 | Separate generator from standalone skeptical evaluator; weighted eval criteria; context anxiety gone with Opus 4.5+; scaffolding is model-generation-dependent | anthropic.com/engineering/harness-design-long-running-apps |
| 39 | Scaling Managed Agents | 2026-04-08 | Brain/hands/session decoupling for production harnesses | anthropic.com/engineering/managed-agents |
| 40 | Prompting Claude Fable 5 | 2026-06 | Brief instructions suffice; over-prescriptive prompts/skills degrade output — refactor old skills; never instruct reasoning-echo (refusal classifier); fresh-context verifier subagents > self-critique; ground progress claims in tool results; memory systems; parallel subagent delegation | platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5 |
| 41 | Claude Prompting Best Practices | 2026, living | 3-5 diverse examples; XML tags "help Claude parse complex prompts unambiguously"; query-at-end "up to 30%" on long multi-document inputs; role = behavior/tone focusing; adaptive thinking replaces manual CoT | platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices |
| 42 | Claude Code Skills / Subagents / Agent Teams docs | 2026, living | Slash commands merged into skills; when_to_use, context: fork, paths, hooks frontmatter; 1,536-char description budget within 1%-of-context listing; 5k-token per-skill compaction; subagent model aliases incl. fable; nested subagents; agent teams for parallel research/review only | code.claude.com/docs/en/skills |
| 43 | Agent Skills Specification (open standard) | 2025-12-18 | SKILL.md as cross-vendor standard, 26+ platforms | agentskills.io/specification |
| 44 | Introducing Claude Fable 5 and Claude Mythos 5 | 2026-06-09 | 1M context, 128k output; adaptive-thinking-only; stop_reason "refusal" with Opus 4.8 fallback; elevated false positives on security-adjacent work post-redeploy | anthropic.com/news/claude-fable-5-mythos-5 |

## Tier 2 — Practitioner Sources (corroboration only, never sole support for a rule)

| # | Source | Date | Key Contribution | URL |
|---|---|---|---|---|
| 45 | Don't Build Multi-Agents (Cognition) | 2025-06-12 | Share full context; single-threaded writes; extra agents for reads not actions | cognition.ai/blog/dont-build-multi-agents |
| 46 | Teaching Claude to Design Better (Wetch) | 2026-01-05 | Cross-conversation isolation; blind A/B skill eval (75% win, p=0.0125); clarity gains largest on smaller models | justinwetch.com/blog/improvingclaudefrontend |
| 47 | Implementing the Official Angular Claude Skills (Angular.love) | 2026-03-24 | Router SKILL.md → 30+ reference files; deterministic ng build verification | angular.love/implementing-the-official-angular-claude-skills |
| 48 | Company-Wide Knowledge Layer with Claude Skills (Hedgineer) | 2026-05-12 | Domain depth beats breadth; narrow precise triggers | hedgineer.io/content/claude-skills-knowledge-layer/ |
| 49 | Prompt Engineering Is System Design (Vaarta Analytics) | c. 2026 | Atomic binary checks reduced false negatives; more instructions ≠ better | vaartaanalytics.com/blogs/prompt-engineering-system-design-structured-llm-workflows |
| 50 | Elastic Agent Builder prompt engineering docs | 2025, living | "Begin with clarity... Only add granular, step-by-step logic if the model fails a specific use case during testing" | elastic.co/docs/explore-analyze/ai-features/agent-builder/prompt-engineering |
| 51 | Prompt Engineering Best Practices (DigitalOcean) | c. 2024-2025 | Heavy-handed role assignments "may actually backfire by limiting helpfulness" | digitalocean.com/resources/articles/prompt-engineering-best-practices |
| 52 | Impeccable (Paul Bakaus) | 2026, active | Shared design vocabulary + anti-pattern library with deterministic detectors | github.com/pbakaus/impeccable |
| 53 | Context Rot (Chroma Research) | 2025-07-14 | 18 models: monotonic, non-uniform degradation with input length; focused prompts beat full prompts; primacy bias persists | trychroma.com/research/context-rot |

## Domain Reference Works

| # | Source | Authors | Year | Key Contribution |
|---|---|---|---|---|
| 54 | Building Evolutionary Architectures | Ford, Parsons | 2017 | Fitness functions for architecture quality |
| 55 | Domain-Driven Design | Evans | 2003 | Bounded contexts, ubiquitous language, context mapping |
| 56 | Release It! | Nygard | 2007/2018 | Circuit breaker, bulkhead, stability patterns |
| 57 | Cynefin framework | Snowden | 2007 | Complexity-aware decision making |
| 58 | Observability Engineering | Majors et al. | 2022 | Modern observability, SLI/SLO, distributed tracing |
| 59 | OWASP Top 10 | OWASP Foundation | Annual | Web application security risks |
| 60 | Threat Modeling | Shostack | 2014 | STRIDE methodology |
| 61 | Hexagonal Architecture | Cockburn | 2005 | Ports and adapters pattern |
| 62 | User Story Mapping | Patton | 2014 | Story mapping technique |
| 63 | INVEST Criteria | Bill Wake | 2003 | User story quality criteria |

---

## Verified Key Numbers

Only numbers that appear in their cited source:

| Finding | Value | Source (#) |
|---|---|---|
| Single-agent accuracy threshold (negative returns above) | ~45% (β=−0.408, p<0.001) | Kim et al. (1) |
| Sequential-task degradation under multi-agent | −39% to −70% | Kim et al. (1) |
| Decomposable-task gain (centralized) | up to +80.9% | Kim et al. (1) |
| Error amplification, independent vs centralized | 17.2x vs 4.4x | Kim et al. (1) |
| Coordination overhead by topology | +58% to +515% | Kim et al. (1) |
| Effective team size under fixed budgets | 3-4 (T ∝ n^1.724) | Kim et al. (1) |
| MAST category shares | 41.77% / 36.94% / 21.30% | MAST (2) |
| Most common MAST mode | FM-1.3 Step repetition (17.14%) | MAST (2) |
| Taxonomy-guided intervention gains | +15.6 / +9.4 pts | MAST (2) |
| Persona effect on MMLU | 71.6% → 68.0% (min) → 66.3% (long) | PRISM (3) |
| MetaGPT vs ChatDev executability / revision cost | 3.75 vs 2.25 / 0.83 vs 2.5 | MetaGPT (8) |
| CaptainAgent vs multi-agent baselines | +21.94% | CaptainAgent (9) |
| Mid-context drop, GPT-3.5-era | >20 percentage points | Lost in the Middle (14) |
| Attention calibration gain | up to 15 percentage points | Found in the Middle (15) |
| Requirement adherence n=5 → n=19 | ~95% → 85.0% (gpt-4o) | What Prompts Don't Say (20) |
| Instruction adherence at 500 instructions | ~68% (best models) | IFScale (21) |
| Format swing, GPT-3.5-turbo code translation | up to 40% (XML not tested) | He et al. (23) |
| Orchestrator-worker research gain / cost | +90.2% / ~15x tokens | Anthropic (37) |
| Query-at-end on long multi-doc inputs | up to 30% | Anthropic docs (41) |
| Few-shot example count | 3-5 | Anthropic docs (41) |
| Skill description budget (Claude Code) | 1,536 chars in 1%-of-context listing | Claude Code docs (42) |
| SKILL.md size limit | <500 lines | Skill best practices (33) |

## Forge Design Standards (house conventions, not findings)

These are Forge's own engineering defaults. They are qualitatively motivated (attention budget, context rot, early team saturation, refinement plateau) but the specific values are conventions.

| Standard | Value | Qualitative basis |
|---|---|---|
| Recommended team size / hard cap | 3-4 / 5 | Kim et al. fixed-budget analysis (1); Ringelmann (11); MacNet (12) |
| Role identity length | ~20-50 tokens (1-3 sentences) | Length-scaling direction in PRISM (3); context economy (30) |
| Vocabulary payload | 15-30 terms in 3-5 clusters | Vocabulary steers generative output (35); mid-range specificity optimum (7) |
| Anti-patterns per agent | 5-10 | Named negative constraints steer away from distribution center (35) |
| Max review iterations | 3 rounds, then escalate | Refinement plateau ~3 iterations (26); bounded-loop hygiene (2) |
| Always-loaded context budget | ~200-500 tokens | Attention budget / minimal high-signal tokens (30) |

---

## Cross-Reference: Research File to Sources

| Research File | Primary Sources Used |
|---|---|
| context-engineering.md | 30, 53, 14, 16, 17, 41 |
| vocabulary-routing.md | 7, 35, 13, 52, 54-63 |
| persona-science.md | 3, 4, 5, 6, 28, 41, 51 |
| scaling-laws.md | 1, 10, 11, 12, 37, 45 |
| failure-taxonomy.md | 2, 26, 36, 38, 1 |
| team-design.md | 8, 9, 1, 2, 37, 45 |
| master-synthesis.md | All sources |

---

*This index covers all sources referenced across the Forge research reference files. Last verified: July 2026 red-team audit (36-agent adversarial review; every entry checked against its live source).*
