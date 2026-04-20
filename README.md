# asta-skill — Semantic Scholar via Ai2 Asta MCP 🔭

[中文文档](README_CN.md) | [Asta MCP Overview](https://allenai.org/asta/resources/mcp) | [Request API Key](https://share.hsforms.com/1L4hUh20oT3mu8iXJQMV77w3ioxm)

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

Works with any agent that speaks MCP and any host that loads [Agent Skills](https://agentskills.io):

| Platform | Status | Details |
|----------|--------|---------|
| **Claude Code** | ✅ Full support | Native SKILL.md + `claude mcp add` registration |
| **Codex** | ✅ Full support | MCP entry in `~/.codex/config.toml` |
| **Cursor / Windsurf / Hermes** | ✅ Full support | Standard `mcpServers` JSON block |
| **opencode** | ✅ Full support | Native skills + MCP in `~/.config/opencode/opencode.json` |
| **OpenClaw/ClawHub** | ✅ Full support | `metadata.openclaw` namespace + MCP config |
| **[pi-mono](https://github.com/badlogic/pi-mono)** | ✅ Full support | `metadata.pimo` namespace |
| **SkillsMP** | ✅ Indexed | GitHub topics configured |

## Comparison

### vs. `semanticscholar-skill` (our REST-based sibling)

| Capability | `semanticscholar-skill` | `asta-skill` |
|---|---|---|
| Transport | Python + direct REST (`s2.py`) | MCP (streamable HTTP) |
| Host requirement | Python + `S2_API_KEY` | Host with MCP support |
| Auth variable | `S2_API_KEY` | `ASTA_API_KEY` (via `x-api-key`) |
| Best for | Scripted batch workflows, custom filters | Zero-code agent integration |
| Works in Cursor / Windsurf out of the box | ❌ | ✅ |

### vs. no skill (native agent)

| Feature | Native agent | This skill |
|---|---|---|
| Knows Asta endpoint & `x-api-key` header | ❌ | ✅ |
| Intent → tool decision table | ❌ | ✅ |
| Workflow patterns (discovery / seed expansion / author / evidence) | ❌ | ✅ |
| Warns against context-blowing `fields=citations` | ❌ | ✅ |
| Install recipes for every MCP host | ❌ | ✅ |

## Prerequisites

- An agent host with MCP support (Claude Code, Codex, Cursor, Windsurf, opencode, OpenClaw/ClawHub, pi-mono, etc.)
- An Asta API key — [request here](https://share.hsforms.com/1L4hUh20oT3mu8iXJQMV77w3ioxm)

  ```bash
  export ASTA_API_KEY=xxxxxxxxxxxxxxxx
  ```

## MCP Server Registration

Register the Asta MCP server with your host **before** installing the skill.

### Claude Code

```bash
claude mcp add -t http -s user asta https://asta-tools.allen.ai/mcp/v1 \
  -H "x-api-key: $ASTA_API_KEY"
```

Then restart Claude Code so the MCP tools load at session start.

### Codex CLI

Edit `~/.codex/config.toml`:

```toml
[mcp_servers.asta]
type = "http"
url = "https://asta-tools.allen.ai/mcp/v1"
headers = { "x-api-key" = "${ASTA_API_KEY}" }
```

### Cursor / Windsurf / Hermes / other MCP clients

```json
{
  "mcpServers": {
    "asta": {
      "serverUrl": "https://asta-tools.allen.ai/mcp/v1",
      "headers": { "x-api-key": "<YOUR_API_KEY>" }
    }
  }
}
```

## Skill Installation

### Claude Code

```bash
# Global (available in all projects)
git clone https://github.com/Agents365-ai/asta-skill.git ~/.claude/skills/asta-skill

# Project-level
git clone https://github.com/Agents365-ai/asta-skill.git .claude/skills/asta-skill
```

### Codex

```bash
git clone https://github.com/Agents365-ai/asta-skill.git ~/.codex/skills/asta-skill
```

### OpenClaw/ClawHub

```bash
git clone https://github.com/Agents365-ai/asta-skill.git ~/.openclaw/skills/asta-skill

# Project-level
git clone https://github.com/Agents365-ai/asta-skill.git skills/asta-skill
```

### pi-mono

```bash
git clone https://github.com/Agents365-ai/asta-skill.git ~/.pimo/skills/asta-skill
```

### SkillsMP

```bash
skills install asta-skill
```

### Installation paths summary

| Platform | Global path | Project path |
|----------|-------------|--------------|
| Claude Code | `~/.claude/skills/asta-skill/` | `.claude/skills/asta-skill/` |
| Codex | `~/.codex/skills/asta-skill/` | N/A |
| OpenClaw/ClawHub | `~/.openclaw/skills/asta-skill/` | `skills/asta-skill/` |
| pi-mono | `~/.pimo/skills/asta-skill/` | — |
| SkillsMP | N/A (installed via CLI) | N/A |

## Usage

Just describe what you want:

```
> Use Asta to get the paper with DOI 10.48550/arXiv.1706.03762

> Search Asta for recent papers on mixture-of-experts at NeurIPS since 2023

> Who cited "Attention Is All You Need"? Show me the top 20 by citation count

> Find snippets in the Asta corpus that mention "flash attention latency"

> Look up Yann LeCun on Asta and list his 2024 papers
```

The skill picks the right Asta tool, attaches safe `fields`, and follows the documented workflow patterns.

## Available Asta Tools

| Tool | Purpose |
|---|---|
| `get_paper` | Single-paper lookup by any supported ID |
| `get_paper_batch` | Batch lookup of multiple IDs in one call |
| `search_papers_by_relevance` | Broad keyword search with venue + date filters |
| `search_paper_by_title` | Title-based lookup |
| `get_citations` | Paginated citation traversal |
| `search_authors_by_name` | Author profile search |
| `get_author_papers` | All papers by a given author |
| `snippet_search` | ~500-word passages from paper bodies |

## Files

- `SKILL.md` — **the only required file**. Loaded by all hosts as the skill instructions.
- `README.md` — this file (English, displayed on GitHub homepage)
- `README_CN.md` — Chinese documentation

## Verification

After registering the MCP server and restarting your host, ask:

> "Use Asta to get the paper ARXIV:1706.03762 with fields title,year,authors,venue,tldr"

A successful call returns *Attention Is All You Need*, NeurIPS 2017, Vaswani et al., with TLDR.

## FAQ

### Why do I need this skill if Asta is already an MCP server?

The MCP server gives your agent raw **tools** (function names + parameter schemas). The skill gives your agent the **expertise** to use them well. Without the skill, the agent must figure everything out from scratch each session:

| Layer | What it provides |
|-------|-----------------|
| **MCP server** | 8 callable tools with input/output schemas |
| **This skill** | Intent routing, safe defaults, workflow patterns, pitfall warnings |

Concretely, the skill adds:

1. **Intent → tool mapping** — which of the 8 tools to call for "find papers about X" vs. "who cited paper Y"
2. **Context-overflow protection** — warns agents to never request `fields=citations` (a single high-citation paper returns 200k+ characters)
3. **Multi-step workflow patterns** — topic discovery, seed-paper expansion, author deep-dive, evidence retrieval
4. **Parallel batching guidance** — prefer `get_paper_batch` over N sequential `get_paper` calls
5. **Safe `fields` defaults** — curated field list that prevents context blowups
6. **Consistent output formatting** — tables, counts, follow-up menus

Think of it like API documentation vs. the API itself: the schema tells the agent *what's possible*, the skill tells it *what's wise*.

## Known Limitations

- **`fields=citations` / `fields=references` blows up context** — a single highly-cited paper returns 200k+ characters. Use the dedicated `get_citations` tool (which paginates) instead. The SKILL.md warns against this explicitly.
- **API key required for production use** — unauthenticated access hits strict rate limits fast
- **Author disambiguation** — common names collide; always inspect affiliations in `search_authors_by_name` before calling `get_author_papers`
- **MCP loads at session start** — if you register the server mid-session, restart your host to pick up the new tools
- **Abstract availability** — not every paper in the corpus has a full abstract; use `snippet_search` or `tldr` as fallback

## Contributing

Suggestions, bug reports, and pull requests are all welcome! If you have ideas to improve this skill — new workflow patterns, better defaults, additional MCP host recipes, documentation fixes, or anything else — feel free to [open an issue](https://github.com/Agents365-ai/asta-skill/issues) or submit a PR directly.

This skill is community-friendly: every contribution, no matter how small, helps make it better for everyone.

## License

MIT

## Support

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
  </tr>
</table>

## Author

**Agents365-ai**

- Bilibili: https://space.bilibili.com/441831884
- GitHub: https://github.com/Agents365-ai
