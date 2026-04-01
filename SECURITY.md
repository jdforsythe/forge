# Security Policy

## Trust Model

Forge provides markdown-based skills and agent definitions that are loaded and executed by Claude Code. When a skill or agent definition is loaded, it becomes part of the active prompt context and can influence what code Claude generates and runs.

**Skills and agent definitions from this repository are curated and reviewed.** However, third-party or user-contributed skills execute with the same permissions as the user running Claude Code. There is no sandbox boundary between a skill's instructions and your own commands.

This means:
- A malicious skill could instruct Claude to read, modify, or delete files
- A malicious agent definition could direct Claude to execute harmful commands
- Any skill installed into `.claude/` operates with your full user permissions

## Input Validation Requirements

Forge is designed to accept user input and generate code/configurations. To prevent prompt injection attacks, all components follow these requirements:

### YAML Frontmatter Escaping
- All user-provided values written to YAML must be escaped using a YAML-safe library (not string concatenation)
- YAML fields like `name`, `domain`, `description` must be quoted/escaped
- No user input should contain unescaped newlines in YAML context
- Invalid YAML characters should trigger clear error messages, not silent escaping

**Reference Implementation:**
```python
import yaml
# Safe: uses YAML library for quoting
agent_data = {'name': user_input, 'domain': user_domain}
yaml_output = yaml.dump(agent_data)

# Unsafe: string concatenation without escaping
yaml_output = f"name: {user_input}\ndomain: {user_domain}"
```

### Configuration File Validation
All configuration files (`CLAUDE.md`, `library/index.json`, template files) must be validated against formal schemas before loading:

**CLAUDE.md Schema:**
- Valid top-level fields: `project_name`, `tech_stack`, `constraints`, `team_preferences`, `integrations`
- All other fields are rejected
- String fields have max 500 character limit
- No field value may contain instruction keywords: `OVERRIDE`, `INJECT`, `IGNORE`, `BYPASS`, `MALICIOUS`

**library/index.json Schema:**
- Follows `schemas/library-index-schema.json` strictly
- Descriptions are limited to 200 characters
- No description may contain instruction keywords
- Quality field must be from enum: `untested`, `tested`, `curated`

### Template Content Validation
All template files (`library/templates/*.md`) must pass content validation:

- Role descriptions must be under 200 characters
- No role description may contain instruction keywords: `OVERRIDE`, `INJECT`, `HIDDEN`, `ALIAS`, `ESCALATE`
- Artifact chain must follow defined format with explicit handoff descriptions
- Templates must have explicit "When NOT to Use" section

### Multi-Turn Conversation Safety
Skills that accept modifications across multiple turns must:

1. Hash or fingerprint agent/skill definitions at creation time
2. Validate that modifications don't alter decision authority or security boundaries
3. Reject modifications that change:
   - Decision Authority (autonomous/escalate/out-of-scope sections)
   - Anti-Pattern Watchlist structure
   - Escalation/approval requirements

### Reference File Limits
Reference files loaded via `references/` directory must be:
- Under 500 lines total
- Under 100KB file size
- Scanned for hidden instruction blocks (comments with OVERRIDE, INJECT, etc.)
- User must explicitly confirm loading large reference files

## Review Before Installing

Always review the content of any `SKILL.md` or agent definition before installing it:

1. Read the full file — skills are plain markdown, so this is straightforward
2. Check for instructions that access files outside the expected scope
3. Look for instructions that download or execute external scripts
4. Be cautious of obfuscated or overly complex instructions
5. Verify YAML/JSON syntax is well-formed (no embedded YAML injection attempts)
6. Look for instruction keywords in descriptions: OVERRIDE, INJECT, BYPASS

If a skill comes from an untrusted source, treat it like you would any script you download from the internet.

## Schema Validation

Forge includes formal JSON schemas for all configuration files:

- `schemas/claude-md-schema.json` — Project context file validation
- `schemas/library-index-schema.json` — Library catalog validation
- `schemas/agent-definition-schema.json` — Agent definition validation (planned)
- `schemas/template-blueprint-schema.json` — Team template validation (planned)

All configuration files MUST be validated against these schemas before loading.

## Reporting Vulnerabilities

If you discover a security vulnerability in Forge itself (the skills, packaging scripts, or templates shipped in this repository), please report it through one of these channels:

- **GitHub Security Advisories**: Use the "Report a vulnerability" button on the Security tab of this repository
- **Email**: Contact the maintainers directly (see repository owner profile)

Please include:
- Description of the vulnerability
- Steps to reproduce (including specific YAML/JSON payloads if applicable)
- Potential impact
- Suggested remediation

We will acknowledge reports within 72 hours and provide a timeline for fixes.

## What Is NOT a Security Issue

The following are quality issues, not security vulnerabilities:

- An agent definition producing unexpected, low-quality, or incorrect output
- A skill that does not work as documented
- Claude interpreting an agent definition in an unintended way
- Template configurations that produce suboptimal team structures

Please file these as regular bug reports using the issue templates.

## Security Audit Trail

All security changes should:
1. Include test cases demonstrating the vulnerability is fixed
2. Be documented in CHANGELOG.md with `[SECURITY]` tag
3. Be reviewed by at least two maintainers before merge
4. Include validation that legitimate use cases still work

## Contact Security

For security matters, contact the maintainers through GitHub Security Advisories or direct email.
