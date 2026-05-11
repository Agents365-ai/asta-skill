# Skill Installation

[中文](INSTALL_SKILL_CN.md)

The skill body lives at `skills/asta-skill/SKILL.md`. The easiest path is the plugin marketplace.

## Plugin marketplace (recommended)

```bash
# Any agent (Claude Code, Cursor, Copilot, etc.)
npx skills add Agents365-ai/365-skills -g

# Claude Code only
/plugin marketplace add Agents365-ai/365-skills
/plugin install asta
```

Also indexed on [SkillsMP](https://skillsmp.com/) and [ClawHub](https://clawhub.ai/) — each handles updates through its own marketplace.

## Manual clone (any host)

```bash
git clone https://github.com/Agents365-ai/asta-skill.git /tmp/asta-skill
cp -r /tmp/asta-skill/skills/asta-skill <your-host's-skills-dir>/asta-skill
```

## Verification

After registering the MCP server and restarting your host, ask:

> "Use Asta to get the paper ARXIV:1706.03762 with fields title,year,authors,venue,tldr"

A successful call returns *Attention Is All You Need*, NeurIPS 2017, Vaswani et al., with TLDR.
