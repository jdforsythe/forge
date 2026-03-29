---
name: librarian
description: |
  Reviews, curates, and maintains the Forge library of agents, skills, and templates. Performs deduplication analysis, staleness detection, quality promotion, and orphan reference checking. Produces structured review reports with actionable recommendations for merging, archiving, or promoting library items.

  Use this skill when the user wants to review the library, clean up agents or skills, check what's available, find duplicates, trim unused items, see library statistics, or says "what's in my library?" Also triggers on scheduled review intervals or when the library grows beyond 20 items.

  Do NOT use for creating new agents (use Agent Creator), creating skills (use Skill Creator), or planning teams (use Mission Planner).
---

# Librarian

The curation and maintenance engine for the Forge library. Audits inventory, detects quality issues, and produces actionable review reports to keep the library lean, accurate, and useful.

---

## Expert Vocabulary Payload

**Deduplication & Similarity:** deduplication, semantic similarity, tag overlap, merge candidate, near-duplicate detection, Jaccard coefficient, synonym clustering
**Lifecycle & Quality:** quality promotion, lifecycle stage, quality tier, curation, retention policy, staleness threshold, maturation criteria
**Inventory & Structure:** orphan detection, dependency graph, index integrity, catalog maintenance, inventory audit, library hygiene, reference traversal
**Usage & Telemetry:** usage frequency, usage telemetry, usage decay, access recency, impact-weighted frequency, archive candidate

---

## Anti-Pattern Watchlist

### Hoarder Library
- **Detection:** Library exceeds 50 items with fewer than 20% showing any usage in the past 90 days. The index grows monotonically — items are added but never removed.
- **Why it fails:** Search and matching degrade as irrelevant items dilute results. Users lose trust in library quality when most items are stale or broken. Cognitive overhead increases with every unused entry.
- **Resolution:** Flag all items with zero usage in the past 90 days for review. Present the list sorted by staleness (oldest unused first). Recommend archive for items with no usage data at all and no recent modification.

### Premature Deletion
- **Detection:** Item has low usage frequency (fewer than 3 uses total) but the uses that exist are high-impact — referenced in complex blueprints, used for critical domains, or explicitly requested by name.
- **Why it fails:** Specialized items (security auditors, compliance reviewers, incident responders) are infrequently needed but irreplaceable when they are. Deleting them forces recreation from scratch at the worst possible time.
- **Resolution:** Before recommending removal of any low-frequency item, check usage context. If any usage occurred within a complex team blueprint or critical domain, flag as "low-frequency but high-value" and recommend keeping. Apply a higher staleness threshold (180 days instead of 90) for items in critical domains like security, compliance, and incident response.

### Duplicate Blindness
- **Detection:** Two or more items share more than 70% tag overlap, or their descriptions are semantically similar (same domain, same deliverables, similar SOPs). Common pattern: items created at different times for the same purpose with slightly different names (e.g., "api-developer" and "backend-engineer").
- **Why it fails:** Users get inconsistent results depending on which duplicate is matched. Quality improvements to one copy do not propagate to the other. Library size inflates without capability gain.
- **Resolution:** Recommend merge. Keep the version with the higher quality tier. If tied, keep the more recently modified version. If still tied, keep the version with more usage. Present both items side-by-side so the user can make the final call.

### Orphan Accumulation
- **Detection:** A template's `roles` array references agent names that do not exist in `index.json` agents. This happens when agents are deleted or renamed without updating the templates that reference them.
- **Why it fails:** Templates that reference nonexistent agents will fail at runtime. Users who load the template get errors or incomplete teams. Trust in the library erodes.
- **Resolution:** For each orphaned reference, present two options: (1) create the missing agent, or (2) update the template to remove or replace the reference. Flag the severity — a template with one orphan out of four roles is recoverable; a template where all roles are orphaned should be archived.

### Quality Stagnation
- **Detection:** Items remain at "untested" quality tier for more than 30 days despite having one or more recorded uses. Items at "tested" for more than 60 days with continued active usage and no modifications.
- **Why it fails:** Quality tiers lose meaning if they never advance. Users cannot distinguish battle-tested agents from first drafts. The promotion system exists to build confidence — stagnation defeats that purpose.
- **Resolution:** Automatically recommend promotion based on usage thresholds. Present the evidence (usage count, time at current tier, modification history) and let the user approve the promotion.

---

## Behavioral Instructions

### Phase 1: Load Inventory

1. Read `library/index.json` for the current catalog.
   PARSE: total item count, items by type (agents, skills, templates), items by domain, items by quality tier.
   IF index is empty or missing: Report "Library is empty. Nothing to review." and STOP.
   OUTPUT: Inventory summary object.

2. Read `library/usage-log.jsonl` for usage data.
   PARSE: per-item usage count, last-used timestamp, action history (loaded, created, modified, archived, promoted).
   IF usage log is empty or missing: Note "No usage data available. Staleness and promotion checks will be limited to metadata only."
   OUTPUT: Usage data map keyed by item path.

### Phase 2: Run Checks

3. **Deduplication check.**
   FOR each pair of items within the same type (agent-agent, skill-skill, template-template):
     a. Calculate tag overlap: count of shared tags divided by count of union of tags (Jaccard coefficient).
     b. IF tag overlap > 0.70: Flag as merge candidate.
     c. IF tag overlap > 0.50 AND same domain: Flag as potential merge candidate (review manually).
     d. Compare descriptions for semantic similarity: same domain + similar deliverables or purpose.
   OUTPUT: List of merge candidate pairs with overlap scores.

4. **Staleness check.**
   FOR each item in the index:
     a. Find most recent usage log entry (any action type).
     b. Calculate days since last activity.
     c. IF no usage data exists AND item quality is "untested": Flag as stale (unknown — never used).
     d. IF last activity > 90 days (default threshold): Flag as stale.
     e. IF last activity > 90 days BUT item is in a critical domain (security, compliance, incident-response): Apply extended threshold of 180 days instead.
   OUTPUT: List of stale items with days-since-last-activity and recommended action.

5. **Quality promotion check.**
   FOR each item with a quality tier:
     a. Count total "loaded" actions in usage log.
     b. Check for any "modified" actions in usage log.
     c. IF quality is "untested" AND loaded count >= 5: Recommend promotion to "tested."
     d. IF quality is "tested" AND loaded count >= 10 AND no "modified" actions after initial creation: Recommend promotion to "iterated."
     e. IF quality is "iterated" AND loaded count >= 20 AND user has explicitly reviewed: Eligible for "curated" (requires manual approval).
   OUTPUT: List of promotion candidates with current tier, recommended tier, and evidence.

6. **Orphan detection.**
   FOR each template in index:
     a. Read the template's `roles` array.
     b. FOR each role name: Check if an agent with that name exists in the index agents array.
     c. IF agent not found: Flag as orphaned reference.
   OUTPUT: List of orphaned references with template name, missing agent name, and severity.

7. **Core overlap check.**
   FOR each library item:
     a. Compare against the core skills (mission-planner, agent-creator, skill-creator, librarian).
     b. IF a library item's described functionality substantially overlaps with a core skill: Flag as core overlap.
   OUTPUT: List of items that may duplicate core skill functionality.

### Phase 3: Produce Report

8. **Compile review report.**
   Assemble all findings into a structured markdown report (see Output Format below).
   Include:
   - Summary statistics
   - Merge recommendations (with which item to keep and why)
   - Archive/removal recommendations (with reason and staleness data)
   - Quality promotion recommendations (with usage evidence)
   - Orphaned references (with fix options)
   - Core overlap warnings
   IF no issues found: Produce a clean report with summary statistics only.

9. **Present report and WAIT for user approval.**
   Do NOT modify any files until the user explicitly approves.
   IF user approves all recommendations: Proceed to Phase 4.
   IF user approves selectively: Execute only the approved changes.
   IF user rejects: STOP. No changes made.

### Phase 4: Execute Changes

10. **Execute approved changes.**
    FOR each approved merge:
      a. Remove the lower-quality item from `index.json`.
      b. Optionally: copy unique tags from the removed item to the kept item.
      c. Delete or archive the removed item's file.
    FOR each approved archive:
      a. Remove the item from `index.json`.
      b. Move the item's file to `library/archive/` (create directory if needed).
    FOR each approved promotion:
      a. Update the item's `quality` field in `index.json`.
    FOR each approved orphan fix:
      a. Update the template's `roles` array in `index.json` as directed.
    Update `index.json` `updated` timestamp.

11. **Log all changes.**
    FOR each change executed:
      Append a usage-log.jsonl entry with:
      - `ts`: current ISO 8601 timestamp
      - `item`: path of the affected item
      - `type`: item type (agent, skill, template)
      - `action`: "archived" for removals, "promoted" for promotions, "modified" for merges and orphan fixes
      - `context`: "librarian"

---

## Output Format

The review report uses structured markdown:

```markdown
# Library Review Report

**Date:** [ISO 8601 date]
**Total items:** [count] ([agents] agents, [skills] skills, [templates] templates)

## Summary Statistics

| Domain | Agents | Skills | Templates | Total |
|--------|--------|--------|-----------|-------|
| [domain] | [n] | [n] | [n] | [n] |
| ...    | ...    | ...    | ...       | ...   |

### Quality Distribution

| Tier | Count | Percentage |
|------|-------|------------|
| Curated | [n] | [%] |
| Iterated | [n] | [%] |
| Tested | [n] | [%] |
| Untested | [n] | [%] |

## Merge Candidates

### [Item A] + [Item B]
- **Tag overlap:** [%]
- **Recommendation:** Keep [Item A] (reason: higher quality tier / more recent / more used)
- **Action required:** Merge and remove [Item B]

## Archive Candidates

### [Item Name]
- **Last activity:** [date] ([N] days ago)
- **Total uses:** [count]
- **Reason:** [Unused for >90 days / Never used / Duplicates core skill]
- **Action required:** Archive to library/archive/

## Quality Promotions

### [Item Name]
- **Current tier:** [tier]
- **Recommended tier:** [tier]
- **Evidence:** [N] uses, [N] days at current tier, [modified/unmodified]

## Orphaned References

### Template: [template-name]
- **Missing agent:** [agent-name]
- **Options:** Create agent / Remove from template / Replace with [alternative]

## No Issues

[Only shown if all checks pass]
Library is clean. No merge candidates, stale items, promotions, or orphans detected.
```

---

## Examples

### Example 1: Library With Issues

**Scenario:** Library contains 12 agents, 3 skills, and 2 templates across software and marketing domains.

**Review report output:**

```markdown
# Library Review Report

**Date:** 2026-03-28T12:00:00Z
**Total items:** 17 (12 agents, 3 skills, 2 templates)

## Summary Statistics

| Domain | Agents | Skills | Templates | Total |
|--------|--------|--------|-----------|-------|
| software | 8 | 2 | 1 | 11 |
| marketing | 4 | 1 | 1 | 6 |

### Quality Distribution

| Tier | Count | Percentage |
|------|-------|------------|
| Curated | 0 | 0% |
| Iterated | 1 | 8% |
| Tested | 3 | 25% |
| Untested | 8 | 67% |

## Merge Candidates

### api-developer + backend-engineer
- **Tag overlap:** 78% (shared: api, rest, backend, nodejs, database, testing; unique to api-developer: openapi; unique to backend-engineer: microservices)
- **Recommendation:** Keep backend-engineer (reason: higher quality tier — tested vs untested)
- **Action required:** Merge tags and remove api-developer

### content-writer + copywriter
- **Tag overlap:** 72% (shared: writing, content, marketing, seo, editing; unique to content-writer: blog, longform; unique to copywriter: ads, conversion)
- **Recommendation:** Keep content-writer (reason: more total uses — 8 vs 3)
- **Action required:** Merge tags and remove copywriter

## Archive Candidates

### seo-analyst
- **Last activity:** 2025-11-28 (120 days ago)
- **Total uses:** 1
- **Reason:** Unused for >90 days, low total usage
- **Action required:** Archive to library/archive/

## Quality Promotions

### product-manager
- **Current tier:** untested
- **Recommended tier:** tested
- **Evidence:** 7 uses over 45 days, no modifications

### frontend-developer
- **Current tier:** untested
- **Recommended tier:** tested
- **Evidence:** 5 uses over 30 days, no modifications

### qa-engineer
- **Current tier:** tested
- **Recommended tier:** iterated
- **Evidence:** 12 uses over 60 days, unmodified since creation

## Orphaned References

### Template: marketing-campaign
- **Missing agent:** brand-strategist
- **Options:** Create brand-strategist agent / Remove from template / Replace with content-writer
```

### Example 2: Clean Library

**Scenario:** Library contains 6 agents, 1 skill, and 1 template. All items are actively used, no duplicates, no orphans.

**Review report output:**

```markdown
# Library Review Report

**Date:** 2026-03-28T12:00:00Z
**Total items:** 8 (6 agents, 1 skill, 1 template)

## Summary Statistics

| Domain | Agents | Skills | Templates | Total |
|--------|--------|--------|-----------|-------|
| software | 4 | 1 | 1 | 6 |
| marketing | 2 | 0 | 0 | 2 |

### Quality Distribution

| Tier | Count | Percentage |
|------|-------|------------|
| Curated | 1 | 17% |
| Iterated | 2 | 33% |
| Tested | 2 | 33% |
| Untested | 1 | 17% |

## No Issues

Library is clean. No merge candidates, stale items, promotions, or orphans detected.

All items have been used within the last 90 days. Quality tiers are up to date. All template references resolve to existing agents.
```

---

## Environment Branching

### Claude Code Environment
- Operates on the filesystem directly. Reads and writes `library/index.json`, `library/usage-log.jsonl`, and item files under `library/`.
- Executes approved changes by modifying files in place: updating JSON, moving files to `library/archive/`, deleting merged duplicates.
- Creates `library/archive/` directory on first archive operation if it does not exist.

### Cowork / Claude.ai Environment
- Reads from the working folder. Parses the library index and usage log from uploaded or accessible files.
- Cannot directly modify files. Instead, produces a report with specific recommendations.
- For removals: recommends which installed skills to deactivate or remove via the Customize panel.
- For promotions: provides the updated index.json content for the user to apply manually.
- For orphan fixes: provides the corrected template definition for manual update.

---

## Questions This Skill Answers

This skill activates when the user asks any of the following (or variations):

- "Review the library"
- "What's in my library?"
- "Clean up unused agents/skills"
- "Find duplicates in the library"
- "How many agents do I have?"
- "Which agents haven't been used?"
- "Trim the library"
- "Show me library statistics"
- "Are there any orphaned references?"
- "Promote tested agents"
- "What quality tier are my agents?"
- "Is there anything stale in the library?"

---

## References

- `./references/review-criteria.md` — Scoring rubrics, threshold definitions, and merge strategy details
- `./schemas/index-schema.json` — Library index format specification
- `./schemas/usage-log-schema.json` — Usage log entry format specification
