# Library Review Criteria

Scoring rubrics, threshold definitions, and decision rules used by the Librarian skill when reviewing the Forge library.

---

## Deduplication Criteria

### Tag Overlap Calculation

Tag overlap between two items uses the Jaccard coefficient:

```
overlap = |tags_A ∩ tags_B| / |tags_A ∪ tags_B|
```

Where `tags_A` and `tags_B` are the tag arrays from each item's index entry.

**Thresholds:**

| Overlap | Classification | Action |
|---------|---------------|--------|
| > 0.70 | Strong duplicate | Recommend merge |
| 0.50 - 0.70 | Potential duplicate | Flag for manual review (only if same domain) |
| < 0.50 | Distinct | No action |

**Example calculation:**
- Agent A tags: `["api", "rest", "backend", "nodejs", "database", "testing"]`
- Agent B tags: `["api", "rest", "backend", "nodejs", "microservices", "testing", "docker"]`
- Intersection: `{"api", "rest", "backend", "nodejs", "testing"}` = 5
- Union: `{"api", "rest", "backend", "nodejs", "database", "testing", "microservices", "docker"}` = 8
- Jaccard coefficient: 5/8 = 0.625
- Classification: Potential duplicate (same domain assumed) — flag for manual review.

### Semantic Similarity

When tag overlap alone is inconclusive (0.40-0.70 range), apply semantic similarity as a secondary check. Two items are semantically similar if they share ALL of the following:

1. **Same domain** — identical `domain` field in index.json.
2. **Same item type** — both agents, both skills, or both templates.
3. **Similar purpose** — their descriptions or file contents describe the same core responsibility. Look for shared key phrases: same deliverables, same tools, same workflow steps.

Semantic similarity is a manual judgment call. The Librarian flags candidates and presents them side-by-side for the user to decide. It does not auto-merge based on semantic similarity alone.

### Cross-Type Comparison

Deduplication only compares items of the same type:
- Agents are compared to agents.
- Skills are compared to skills.
- Templates are compared to templates.

An agent and a skill may have overlapping tags (e.g., both tagged "testing") but serve fundamentally different purposes. Cross-type overlap is not flagged as duplication.

---

## Staleness Criteria

### Default Thresholds

| Condition | Threshold | Action |
|-----------|-----------|--------|
| No usage data exists, quality is "untested" | Any age | Flag as "unknown — never used" |
| No usage data exists, quality is "tested" or higher | N/A | Do not flag (was promoted at some point, may be valid) |
| Last activity > 90 days | 90 days | Flag as stale |
| Last activity > 180 days | 180 days | Flag as strongly stale — recommend archive |
| Critical domain item, last activity > 180 days | 180 days | Flag as stale (extended threshold) |
| Critical domain item, last activity <= 180 days | N/A | Do not flag |

### Critical Domains

The following domains receive an extended staleness threshold of 180 days (instead of 90):

- security
- compliance
- incident-response
- disaster-recovery
- legal

These domains contain items that are infrequently needed but high-value when required. Standard staleness thresholds would incorrectly flag them.

### What Counts as Activity

Any action in usage-log.jsonl counts as activity for staleness purposes:
- `loaded` — item was used in a session
- `created` — item was first added
- `modified` — item was updated
- `archived` — item was archived (resets staleness if later unarchived)
- `promoted` — item's quality tier was advanced

The most recent timestamp across all action types determines the last activity date.

### Items With No Usage Data

Items that exist in index.json but have no entries in usage-log.jsonl require special handling:

1. **If quality is "untested":** The item was likely added but never used. Flag as "unknown — never used" and recommend review. These are the most common archive candidates.
2. **If quality is "tested" or higher:** The item was promoted at some point, which implies past usage even if the log does not capture it (e.g., usage log was introduced after the item). Do not flag as stale based on missing data alone.
3. **If the item was recently added** (within 14 days based on index.json `updated` timestamp or file creation date): Do not flag. New items need time to be discovered and used.

---

## Quality Promotion Criteria

### Tier Definitions

| Tier | Meaning | How to reach |
|------|---------|-------------|
| untested | Newly created, no validated usage | Default for all new items |
| tested | Used successfully multiple times | 5+ "loaded" actions, no reported failures |
| iterated | Proven through sustained use without needing changes | 10+ "loaded" actions, zero "modified" actions after creation |
| curated | Reviewed and endorsed by the user | Manual approval only — Librarian can recommend but not auto-promote to curated |

### Promotion Thresholds

**Untested to Tested:**
- Required: 5 or more `loaded` actions in usage-log.jsonl.
- Time at current tier: at least 7 days (prevents premature promotion from a single burst of testing).
- No blocking condition.

**Tested to Iterated:**
- Required: 10 or more `loaded` actions in usage-log.jsonl.
- Required: zero `modified` actions after the initial `created` action. If the item has been modified, the counter resets — it needs 10 more loads after the last modification.
- Time at current tier: at least 14 days.
- Rationale: "iterated" means the item is stable — it works well enough that nobody has needed to change it.

**Iterated to Curated:**
- Required: 20 or more `loaded` actions.
- Required: explicit user approval. The Librarian recommends promotion but does not execute it without confirmation.
- Rationale: "curated" is a trust signal. It means the user has personally reviewed and endorsed the item. Automated promotion would dilute this signal.

### What "Never Modified" Means

An item is considered "never modified" for promotion purposes if:
- There are zero `modified` actions in the usage log after the first `created` action.
- File content changes that are not logged (e.g., manual edits outside of Forge) are not detected. The Librarian relies on the usage log as the source of truth.

If an item has been modified but the modifications are minor (tag updates, description clarification), the user can manually override and approve promotion despite the modification record.

### Demotion

The Librarian does not automatically demote items. If an item at "tested" or higher is found to have issues, the Librarian flags it in the review report with a recommendation to investigate, but does not lower the tier without user approval.

---

## Orphan Detection Criteria

### Reference Traversal

Templates reference agents by name in their `roles` array. The orphan check traverses these references:

```
FOR each template in index.json:
  FOR each role_name in template.roles:
    SEARCH index.json.agents WHERE agent.name == role_name
    IF not found:
      FLAG as orphaned reference
```

### Severity Classification

| Condition | Severity | Recommendation |
|-----------|----------|----------------|
| 1 orphaned role out of 4+ total roles | Low | Template is mostly functional. Create the missing agent or remove the role. |
| 2+ orphaned roles out of 4+ total roles | Medium | Template is degraded. Prioritize fixing references. |
| All roles orphaned | High | Template is completely broken. Archive unless the user wants to recreate all agents. |
| 1 orphaned role out of 1-2 total roles | High | Template loses core functionality. Fix immediately or archive. |

### Common Causes of Orphans

1. **Agent renamed:** Agent was renamed in index.json but templates were not updated. Fix: update the template's roles array with the new name.
2. **Agent deleted:** Agent was removed during a previous cleanup. Fix: either recreate the agent or remove it from the template.
3. **Typo in template:** Template was created with an incorrect agent name. Fix: correct the name.
4. **Agent not yet created:** Template was designed before all its agents were built. Fix: create the missing agent.

---

## Merge Strategy

When two items are identified as duplicates, the Librarian must recommend which to keep. The decision follows a priority-ordered tiebreaker chain:

### Priority 1: Quality Tier

Keep the item with the higher quality tier.

```
curated > iterated > tested > untested
```

If one item is "tested" and the other is "untested," keep the "tested" item regardless of other factors.

### Priority 2: Recency

If quality tiers are equal, keep the more recently modified item (based on most recent `modified` or `created` action in usage log).

Rationale: the more recent item is more likely to reflect current best practices and project context.

### Priority 3: Usage Count

If quality tier and recency are equal, keep the item with more total `loaded` actions.

Rationale: higher usage suggests broader applicability and more validated behavior.

### Priority 4: Tag Completeness

If all above are equal, keep the item with more tags (richer metadata makes it more discoverable).

### Post-Merge Actions

After merging:
1. **Combine tags:** Add any unique tags from the removed item to the kept item. Do not create duplicate tags.
2. **Update references:** If any template references the removed item by name, update the reference to point to the kept item.
3. **Log the change:** Record a `modified` action for the kept item and an `archived` action for the removed item.

---

## Archive vs Delete

### When to Archive

Archive an item (move to `library/archive/`) when:
- The item is stale but might be useful again in a different project or context.
- The item is a duplicate but has unique tags or approaches worth preserving for reference.
- The item was created for a specific project that is complete but might resume.
- The user is uncertain about removal.

Archived items:
- Are removed from `index.json` (no longer searchable or matchable).
- Are preserved on disk under `library/archive/` with their original directory structure.
- Can be restored by moving them back and re-adding to `index.json`.

### When to Delete

Delete an item (remove from disk entirely) when:
- The item is a near-exact duplicate and the kept version fully subsumes it.
- The item has errors or broken content that make it unusable.
- The user explicitly requests deletion.

Default behavior: always archive rather than delete. Deletion is a destructive operation that requires explicit user confirmation. The Librarian recommends archive by default and only suggests deletion when the item is truly redundant.

### Archive Directory Structure

Archived items preserve their original path structure:

```
library/archive/agents/software/api-developer.md
library/archive/templates/marketing/campaign-template.md
```

This allows easy restoration — move the file back to `library/` and re-add to `index.json`.
