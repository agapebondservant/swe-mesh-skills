# swe-mesh-skills

A collection of AI agent skills built to the [Agent Skills open standard](https://agentskills.io). 
Compatible with any agent that supports SKILL.md.

## Available Skills

| Skill | Description |
|-------|-------------|
| [`rhel10-code-compat`](./rhel10-code-compat/SKILL.md) | Analyzes a codebase's dependencies and assesses RHEL 10 compatibility |

---

## Installation

### Claude Code

Copy or symlink the skill directory into your agent's skills folder. For example:

```bash
# OpenCode
cp rhel10-code-compat ~/.config/opencode/skills/rhel10-code-compat

# Cursor / Windsurf
cp rhel10-code-compat ~/.cursor/skills/
```
