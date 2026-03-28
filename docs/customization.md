# Customizing Forge

How to extend the library, configure maintenance, and share your setup across a team.

---

## 1. Adding Team Templates

Team templates tell the Mission Planner how to staff a project. Every template follows the format defined in `schemas/team-blueprint.md`.

**Steps:**

1. Copy an existing template from `library/templates/` as a starting point:
   ```
   cp library/templates/saas-product-team.md library/templates/your-template-name.md
   ```
2. Edit the new file. Every template requires these sections:
   - **YAML frontmatter** with `goal`, `domain`, `complexity`, `topology`, `agent_count`, and `estimated_cost_tier`
   - **Roles** -- numbered list of role titles with one-sentence project-specific descriptions
   - **Artifact Chain** -- the sequence of artifacts flowing between roles, with format descriptions
   - **Quality Gates** -- which handoffs require review before the next step proceeds
   - **Topology** -- the selected coordination architecture with rationale
   - **Anti-Patterns to Guard Against** -- team-level failure modes for this project type
3. Register the template in `library/index.json` under the `templates` array:
   ```json
   {
     "name": "your-template-name",
     "domain": "software",
     "path": "templates/your-template-name.md",
     "tags": ["relevant", "searchable", "tags"],
     "agent_count": 4
   }
   ```
4. Keep `agent_count` between 3 and 5. Exceeding 5 agents requires explicit justification in the topology rationale.

---

## 2. Adding Domain Agents

Every agent follows the 7-component format defined in `schemas/agent-definition.md`.

**Steps:**

1. Create the domain directory if it does not exist:
   ```
   mkdir -p library/agents/your-domain
   ```
2. Create the agent file:
   ```
   touch library/agents/your-domain/your-agent.md
   ```
3. Write all seven components in order:
   - **YAML frontmatter** -- `name`, `domain`, `tags` (3-10), `created`, `quality`, `source`
   - **Role Identity** -- real job title, primary responsibility, org context (under 50 tokens)
   - **Domain Vocabulary Payload** -- 15-30 terms in 3-5 clusters
   - **Deliverables & Artifacts** -- concrete, verifiable outputs with format descriptions
   - **Decision Authority & Boundaries** -- autonomous, escalate, out of scope
   - **SOPs / Workflow Steps** -- imperative verbs, IF/THEN conditions, OUTPUT lines
   - **Anti-Pattern Watchlist** -- 5-10 named failure modes with detection and resolution
   - **Interaction Model** -- receives from, delivers to, handoff format, coordination style
4. Set `quality: untested` and `source: manual` in the frontmatter.
5. Register in `library/index.json` under the `agents` array:
   ```json
   {
     "name": "your-agent",
     "domain": "your-domain",
     "path": "agents/your-domain/your-agent.md",
     "tags": ["tag1", "tag2", "tag3"],
     "quality": "untested"
   }
   ```

Quality starts at `untested`. The Librarian promotes automatically based on usage: 5 uses promotes to `tested`, 10 uses without modification promotes to `iterated`. Hand-crafted agents reviewed by a human can be set to `curated`.

**Shortcut:** Ask the Agent Creator instead of writing manually:
```
Create a [role title] agent for [domain]
```

---

## 3. Creating Domain Skills

Skills are the specialized knowledge packages that agents draw from.

### Using the Skill Creator (recommended)

```
Create a skill for [domain]
```

The Skill Creator handles the format automatically.

### Manual creation

Follow the architecture used by existing skills (see `skills/librarian/SKILL.md` for reference).

1. Create the skill directory and file:
   ```
   mkdir -p library/skills/your-skill
   touch library/skills/your-skill/SKILL.md
   ```
2. Structure the file with these sections:
   - **YAML frontmatter** with `name` and `description`. The description must serve as a dual-register trigger: first paragraph explains what the skill does; second paragraph lists the natural-language phrases that should activate it; third paragraph lists what it does NOT handle.
   - **Expert Vocabulary Payload** -- 15-30 precise terms in 3-5 clusters. Apply the 15-year practitioner test: would a senior use this exact term with a peer?
   - **Anti-Pattern Watchlist** -- at least 5 named failure modes with detection signals, failure mechanism, and concrete resolution.
   - **Imperative Behavioral Instructions** -- ordered SOPs telling the skill how to operate. Every step starts with a verb.
   - **Examples** -- concrete input/output pairs showing the skill in action.
   - **Questions This Skill Answers** -- a list of representative queries the skill handles.
3. Keep `SKILL.md` under 500 lines. If you need more reference material, place it in a `references/` subdirectory:
   ```
   mkdir -p library/skills/your-skill/references
   ```
4. Register in `library/index.json` under the `skills` array.

---

## 4. Configuring the Librarian

The Librarian audits library health. Its behavior is controlled by thresholds in `skills/librarian/SKILL.md`.

| Setting | Default | What it controls |
|---|---|---|
| Staleness threshold | 90 days | Items with no usage in this window are flagged for review |
| Critical-domain staleness | 180 days | Higher threshold for security, compliance, incident response |
| Dedup sensitivity | >70% tag overlap | Items exceeding this are flagged as merge candidates |
| Promotion: untested to tested | 5 uses | Automatic quality tier advancement |
| Promotion: tested to iterated | 10 uses (no modifications) | Automatic quality tier advancement |
| Hoarder threshold | 50 items, <20% active | Triggers library-wide cleanup recommendation |

To adjust a threshold, edit the corresponding value in `skills/librarian/SKILL.md` directly.

**Running a manual review:**
```
Review the library
```

The Librarian produces a structured report covering: inventory summary, staleness candidates, duplicate pairs, orphan references, and promotion recommendations.

---

## 5. Team Distribution

Forge is designed to be shared across a team.

**Plugin install (recommended):** Every team member installs Forge as a plugin:
```
/plugin add https://github.com/jdforsythe/forge
```

**Project-level customizations:** To share custom agents and templates specific to a project, commit them to your repo's `.claude/` directory:
```
git add .claude/agents/ .claude/skills/
git commit -m "Add project agent definitions"
```

**Precedence rules:**
- Project-level items (in your repo's `.claude/skills/`) override plugin-provided ones.
- If the same skill name exists at both levels, the project-level version wins.
- This lets you customize skills for a specific project without affecting the global plugin.

**Sharing individual items:**
- Copy specific agents or templates between repos by moving the `.md` files and updating each repo's `library/index.json`.
- Skills can be shared the same way -- copy the skill directory and register it.

---

## 6. Overriding Core Skills

The four core skills (`mission-planner`, `agent-creator`, `skill-creator`, `librarian`) live in `skills/`. To customize them without losing the ability to receive upstream updates:

1. Copy the skill to your project's local skills directory:
   ```
   mkdir -p .claude/skills
   cp -r skills/librarian .claude/skills/librarian
   ```
2. Edit the copy in `.claude/skills/`. Project-level skills take precedence over the originals.
3. Do not modify the files in `skills/` directly if you want to pull future updates from the upstream Forge repository.

**Common overrides:**
- Adjusting the Librarian's staleness thresholds for a fast-moving project (e.g., 30 days instead of 90)
- Changing the Mission Planner's default topology preferences for your team's workflow
- Adding domain-specific anti-patterns to the Agent Creator's watchlist
- Customizing the Skill Creator's vocabulary cluster size for a specialized domain

---

## Quick Reference

| Task | Command or path |
|---|---|
| Add a template | `library/templates/your-template.md` + update `library/index.json` |
| Add an agent | `library/agents/{domain}/your-agent.md` + update `library/index.json` |
| Add a skill | `library/skills/your-skill/SKILL.md` + update `library/index.json` |
| Review library health | "Review the library" |
| Create agent via assistant | "Create a [role] agent for [domain]" |
| Create skill via assistant | "Create a skill for [domain]" |
| Share with team | `/plugin add https://github.com/jdforsythe/forge` |
| Override a core skill | Copy from `skills/` to `.claude/skills/`, edit the copy |
