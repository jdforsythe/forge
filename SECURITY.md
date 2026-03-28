# Security Policy

## Trust Model

Forge provides markdown-based skills and agent definitions that are loaded and executed by Claude Code and Cowork. When a skill or agent definition is loaded, it becomes part of the active prompt context and can influence what code Claude generates and runs.

**Skills and agent definitions from this repository are curated and reviewed.** However, third-party or user-contributed skills execute with the same permissions as the user running Claude Code. There is no sandbox boundary between a skill's instructions and your own commands.

This means:
- A malicious skill could instruct Claude to read, modify, or delete files
- A malicious agent definition could direct Claude to execute harmful commands
- Any skill installed into `.claude/` operates with your full user permissions

## Review Before Installing

Always review the content of any `SKILL.md` or agent definition before installing it:

1. Read the full file — skills are plain markdown, so this is straightforward
2. Check for instructions that access files outside the expected scope
3. Look for instructions that download or execute external scripts
4. Be cautious of obfuscated or overly complex instructions

If a skill comes from an untrusted source, treat it like you would any script you download from the internet.

## Reporting Vulnerabilities

If you discover a security vulnerability in Forge itself (the skills, packaging scripts, or templates shipped in this repository), please report it through one of these channels:

- **GitHub Security Advisories**: Use the "Report a vulnerability" button on the Security tab of this repository
- **Email**: Contact the maintainers directly (see repository owner profile)

Please include:
- Description of the vulnerability
- Steps to reproduce
- Potential impact

We will acknowledge reports within 72 hours and provide a timeline for fixes.

## What Is NOT a Security Issue

The following are quality issues, not security vulnerabilities:

- An agent definition producing unexpected, low-quality, or incorrect output
- A skill that does not work as documented
- Claude interpreting an agent definition in an unintended way
- Template configurations that produce suboptimal team structures

Please file these as regular bug reports using the issue templates.
