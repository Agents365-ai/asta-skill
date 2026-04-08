# asta-skill 🔭

Give any MCP-capable agent (Claude Code, Codex, Hermes, Cursor, Windsurf, OpenClaw, …) access to the Semantic Scholar academic corpus via **Ai2's Asta MCP Server**.

- **MCP endpoint:** `https://asta-tools.allen.ai/mcp/v1`
- **Transport:** streamable HTTP
- **Auth:** `x-api-key` header ([request an API key](https://share.hsforms.com/1L4hUh20oT3mu8iXJQMV77w3ioxm))

## What it is

`asta-skill` is a **zero-code agent skill pack**. It doesn't make HTTP requests itself; instead it teaches the host agent:

1. Which tools the Asta MCP server exposes
2. Which tool matches which user intent
3. How to compose tools into workflows (topic discovery, seed expansion, author deep-dive, evidence retrieval)
4. How to register the Asta MCP server in each host

## Tools exposed by Asta

| Tool | Purpose |
|---|---|
| `get_paper` | Look up a paper by DOI / arXiv / PMID / PMCID / CorpusId / MAG / ACL / SHA / URL |
| `search_papers_by_relevance` | Broad keyword search (supports venue + date filters) |
| `search_paper_by_title` | Title-based lookup |
| `get_citations` | Papers citing a given publication |
| `search_authors_by_name` | Author profile search |
| `get_author_papers` | All papers by an author |
| `snippet_search` | ~500-word passages from paper bodies matching a query |

## Installation

Set the API key first:

```bash
export ASTA_API_KEY="..."
```

### Claude Code

```bash
claude mcp add asta \
  --transport http \
  --url https://asta-tools.allen.ai/mcp/v1 \
  --header "x-api-key: $ASTA_API_KEY"
```

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

### Installing the skill itself

| Platform | Path |
|---|---|
| Claude Code (global) | `~/.claude/skills/asta-skill/` |
| Claude Code (project) | `.claude/skills/asta-skill/` |
| OpenClaw (global) | `~/.openclaw/skills/asta-skill/` |
| OpenClaw (project) | `skills/asta-skill/` |

Clone or copy this repository into any of the paths above.

## Verification

Ask the agent:

> "Use Asta to look up the paper with DOI `10.48550/arXiv.1706.03762`."

A successful call returns the *Attention Is All You Need* metadata.

## vs. `semanticscholar-skill`

| | semanticscholar-skill | asta-skill |
|---|---|---|
| Transport | Python + direct REST | MCP (streamable HTTP) |
| Runtime needs | Python + `S2_API_KEY` | Host with MCP support |
| Best for | Scripted batch workflows | Zero-code agent integration |

Both wrap the Semantic Scholar corpus. Pick based on whether your host supports MCP.

## License

MIT
