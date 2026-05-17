# asta-skill — Semantic Scholar via Ai2 Asta MCP

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/Agents365-ai/asta-skill?style=flat&logo=github)](https://github.com/Agents365-ai/asta-skill/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/Agents365-ai/asta-skill?style=flat&logo=github)](https://github.com/Agents365-ai/asta-skill/network/members)
[![Latest Release](https://img.shields.io/github/v/release/Agents365-ai/asta-skill?logo=github)](https://github.com/Agents365-ai/asta-skill/releases/latest)
[![Last Commit](https://img.shields.io/github/last-commit/Agents365-ai/asta-skill?logo=github)](https://github.com/Agents365-ai/asta-skill/commits/main)

[![SkillsMP](https://img.shields.io/badge/SkillsMP-listed-1f6feb)](https://skillsmp.com/skills/agents365-ai-asta-skill-skills-asta-skill-skill-md)
[![ClawHub](https://img.shields.io/badge/ClawHub-listed-ff6b35)](https://clawhub.ai/agents365-ai/asta-pro-skill)
[![Claude Code Plugin](https://img.shields.io/badge/Claude%20Code-plugin-8a2be2)](https://github.com/Agents365-ai/365-skills)
[![Agent Skills](https://img.shields.io/badge/Agent%20Skills-compatible-2ea44f)](https://agentskills.io)
[![Discord](https://img.shields.io/badge/Discord-Join-5865F2?logo=discord&logoColor=white)](https://discord.gg/79JF5Atuk)

**English** · [中文](README_CN.md) · [Asta MCP Overview](https://allenai.org/asta/resources/mcp) · [Request API Key](https://share.hsforms.com/1L4hUh20oT3mu8iXJQMV77w3ioxm)

A pure instruction-pack skill that turns natural-language research questions into well-formed calls against Ai2's Asta MCP server (Semantic Scholar). Routes intent → tool, fills safe defaults, chains workflows, and warns you off the usual pitfalls. Works with **Claude Code, Codex, Cursor, Windsurf, Hermes, opencode, OpenClaw, pi-mono**, and any agent compatible with the [Agent Skills](https://agentskills.io) format.

## What it does

- **Search** the Semantic Scholar academic corpus by keyword, title, author, or full-text snippet
- **Look up** a paper from any ID (DOI, arXiv, PMID, PMCID, CorpusId, MAG, ACL, SHA, URL)
- **Traverse citations** — find who cited a given paper, with filtering and pagination
- **Batch-lookup** multiple papers in one call via `get_paper_batch`
- **Snippet search** — retrieve ~500-word passages from paper bodies for evidence grounding
- **Author discovery** — find researchers and list their publications
- **Zero-code integration** — the skill is a pure instruction pack; all I/O goes through the Asta MCP server
- Triggers automatically whenever the user asks for papers, citations, academic search, or literature discovery and Asta tools are registered

## Multi-Platform Support

Works on any host that speaks MCP and loads [Agent Skills](https://agentskills.io) — verified on **Claude Code, Codex, Cursor, Windsurf, Hermes, opencode, OpenClaw/ClawHub,** and **[pi-mono](https://github.com/badlogic/pi-mono)**; indexed on **SkillsMP**. **LM Studio** (0.3.17+) supports MCP but does not auto-load skills — paste `SKILL.md` into the system prompt (see [LM Studio (manual mode)](docs/INSTALL_MCP.md#lm-studio-manual-mode) in the install guide).

## Prerequisites

- An agent host with MCP support (Claude Code, Codex, Cursor, Windsurf, opencode, OpenClaw/ClawHub, pi-mono, etc.)
- An Asta API key — [request here](https://share.hsforms.com/1L4hUh20oT3mu8iXJQMV77w3ioxm)

  ```bash
  export ASTA_API_KEY=xxxxxxxxxxxxxxxx
  ```

## Installation

Two steps — register the MCP server first, then drop the skill into your host:

1. **[Register the Asta MCP server](docs/INSTALL_MCP.md)** — per-host recipes for Claude Code, Codex, Cursor / Windsurf / Hermes, and LM Studio.
2. **[Install the skill](docs/INSTALL_SKILL.md)** — plugin marketplace (recommended) or manual clone.

## Usage

See [USAGE.md](docs/USAGE.md) for natural-language query examples and a chained `asta-skill` + `paper-fetch` walkthrough with screenshots.

## 🔗 Related Skills

Part of the [Agents365-ai research-skill family](https://github.com/Agents365-ai) — pick the right tool for the job:

| Skill | Niche | When to use |
|---|---|---|
| [semanticscholar-skill](https://github.com/Agents365-ai/semanticscholar-skill) | Direct Semantic Scholar API (Python) | When MCP isn't available or you prefer scripted access |
| [paper-fetch](https://github.com/Agents365-ai/paper-fetch) | DOI → PDF, 7-source fallback | When you need full text after finding citations |
| [scholar-deep-research](https://github.com/Agents365-ai/scholar-deep-research) | 8-phase literature review pipeline | When the user wants a structured cited report |
| [zotero-research-assistant](https://github.com/Agents365-ai/zotero-research-assistant) | Zotero library workflows | When references go into Zotero |

## 💬 Community

Join us for help, Q&A, and updates:

- **Discord:** https://discord.gg/79JF5Atuk
- **WeChat:** scan the QR code below

<p align="center">
  <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/agents365ai_wechat_1.png" width="200" alt="WeChat Community Group">
</p>

## ❤️ Support

If this skill helps you, consider supporting the author:

<table>
  <tr>
    <td align="center">
      <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/wechat-pay.png" width="180" alt="WeChat Pay">
      <br>
      <b>WeChat Pay</b>
    </td>
    <td align="center">
      <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/alipay.png" width="180" alt="Alipay">
      <br>
      <b>Alipay</b>
    </td>
    <td align="center">
      <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/buymeacoffee.png" width="180" alt="Buy Me a Coffee">
      <br>
      <b>Buy Me a Coffee</b>
    </td>
    <td align="center">
      <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/awarding/award.gif" width="180" alt="Give a Reward">
      <br>
      <b>Give a Reward</b>
    </td>
  </tr>
</table>

## 👤 Author

**Agents365-ai**

- GitHub: https://github.com/Agents365-ai
- Bilibili: https://space.bilibili.com/441831884

## 📄 License

[MIT](LICENSE)
